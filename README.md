- 👋 Hi, I’m @MaheswariBorra
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
MaheswariBorra/MaheswariBorra is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
"1-Users should have option to select their role option - user/Admin
2-Admin should select the Admin option. 
2-Admin's User id and password should be pre-loaded in the database, which can be used to log in.
"

create database testdb;
use testdb;
CREATE TABLE ADMIN(USER_ID VARCHAR(45) NOT NULL, PASSWORD VARCHAR(15) NOT NULL)
engine=InnoDB default charset=utf8 collate=utf8_unicode_ci;
insert into ADMIN values("admin@gmail.com","Abc@1234");
