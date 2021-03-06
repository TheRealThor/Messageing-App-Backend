# Migration `20200519134120-adding-field-id-in-member`

This migration has been generated by Harshit Sahu at 5/19/2020, 1:41:20 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE "public"."User" (
    "email" text  NOT NULL ,
    "id" SERIAL,
    "password" text  NOT NULL ,
    "username" text  NOT NULL ,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."Member" (
    "id" SERIAL,
    "memberUserId" integer  NOT NULL ,
    "teamUserId" integer  NOT NULL ,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."Team" (
    "id" SERIAL,
    "name" text  NOT NULL ,
    "userId" integer  NOT NULL ,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."Channel" (
    "id" SERIAL,
    "name" text  NOT NULL ,
    "public" boolean  NOT NULL DEFAULT true,
    "teamId" integer  NOT NULL ,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."Message" (
    "channelId" integer  NOT NULL ,
    "createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "id" SERIAL,
    "text" text  NOT NULL ,
    "userId" integer  NOT NULL ,
    PRIMARY KEY ("id")
) 

CREATE TABLE "public"."DirectMessage" (
    "createdAt" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "id" SERIAL,
    "receiverId" integer  NOT NULL ,
    "senderId" integer  NOT NULL ,
    "text" text  NOT NULL ,
    PRIMARY KEY ("id")
) 

CREATE UNIQUE INDEX "User.email" ON "public"."User"("email")

CREATE UNIQUE INDEX "Team.name" ON "public"."Team"("name")

ALTER TABLE "public"."Member" ADD FOREIGN KEY ("memberUserId")REFERENCES "public"."User"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Member" ADD FOREIGN KEY ("teamUserId")REFERENCES "public"."Team"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Team" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Channel" ADD FOREIGN KEY ("teamId")REFERENCES "public"."Team"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Message" ADD FOREIGN KEY ("userId")REFERENCES "public"."User"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."Message" ADD FOREIGN KEY ("channelId")REFERENCES "public"."Channel"("id") ON DELETE CASCADE  ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20200519134120-adding-field-id-in-member
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,60 @@
+generator client {
+  provider = "prisma-client-js"
+}
+
+datasource db {
+  provider = "postgresql"
+  url      = env("DATABASE_URL")
+}
+
+model User {
+  email   String   @unique
+  id      Int      @default(autoincrement()) @id
+  username    String
+  password String
+  teams Team[]
+  messages Message[]
+  memberId Member[]
+}
+
+model Member {
+  userId  User @relation(fields:[memberUserId], references: [id])
+  memberUserId Int
+  teamId  Team @relation(fields: [teamUserId], references: [id])
+  teamUserId Int
+  id  Int @id @default(autoincrement())
+}
+
+model Team {
+  id Int @id @default(autoincrement())
+  name String @unique
+  owner User  @relation(fields:[userId],references: [id])
+  userId Int
+  channels Channel[]
+  members Member[]
+}
+
+model Channel {
+  id Int @id @default(autoincrement())
+  name String
+  public Boolean @default(true)
+  team Team @relation(fields: [teamId], references: [id])
+  teamId Int
+}
+
+model Message {
+  id  Int @id @default(autoincrement())
+  createdAt  DateTime   @default(now())
+  text String 
+  user User @relation(fields: [userId], references: [id])
+  userId Int
+  channel Channel @relation(fields: [channelId], references: [id])
+  channelId Int
+}
+model DirectMessage {
+    id  Int @id @default(autoincrement())
+    createdAt  DateTime   @default(now())
+    senderId Int
+    receiverId Int
+    text String
+}
```


