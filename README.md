# SQL
ปูพื้นฐาน SQL สำหรับจัดการฐานข้อมูล

## Introduction
#### SQL: Structured Query Language
- เป็นภาษาที่ใช้ในการเขียนโปรแกรม เพื่อจัดการฐานข้อมูลโดยเฉพาะ (เป็นภาษาในการจัดการฐานข้อมูล)
- เป็นภาษามาตราฐานบนระบบฐานข้อมูลเชิงสัมพันธ์ (Relational Database)

#### DBMS: Database management system   
- เป็นระบบ/โปรแกรมจัดการฐานข้อมูล ที่สนับสนุนการใช้คำสั่ง SQL (ทำให้เราไม่ต้องเขียน sql โดยตรงก็ได้)
- ex. PostgreSQL, MySQL, ORACLE, SQLite, SQLServer, MariaDB (รองรับการใช้งานคำสั่ง sql)

_ทำไมต้องมีDBMS_
- ช่วยดำเนินการต่อฐานข้อมูลได้ง่ายขึ้น ไม่ต้องจำคำสั่งทั้งหมด
- ป้องกันเรื่องข้อมูลเสียหาย สูญหาย (มีการ backup)
- บางตัวอาจจะ UI แสดงความสัมพันธ์ด้วย

#### การสร้างฐานข้อมูล

| SQL                  | คำอธิยาน                        |
| -------------------- | ------------------------------ |
| Database             | ฐานข้อมูล                        |
| Table                | ตารางข้อมูล                      |
| Row                  | ชุดข้อมูล                         |
| Column               | ชื่อ field/key ที่เก็บข้อมูล          |
| Index                | ลำดับ                           |
| table joins          | เชื่อมข้อมูลภายในตาราง             |
| primary key (unique) | คีย์ที่ใช้ในการอ้างอิงข้อมูล row นั้นๆ    |
| aggregation (group)  | การรวมข้อมูลหลายตาราง            |


#### การสร้างตารางข้อมูล
| Type          | คำอธิบาย                              | 
| --------------| ----------------------------------   |
| INTEGER       | จำนวนเต็ม | 
| VARCHAR       | ข้อความ(จำกัดที่ความยาว 255) | 
| TEXT          | ข้อความ(ไม่จำกัดความยาว) |
| BLOB          | file, object |
| REAL          | จำนวนทศนิยม (ไม่มีการกำหนดตำแหน่งทศนิยมที่แน่นอน) |
| NUMBERRIC     | จำนวน สามารถเป็นเลขทศนิยม, DateTime (มีการกำหนดตำแหน่งทศนิยมที่แน่นอน เช่น 2 ตำแหน่ง `_.00`) |
| NOT NULL      | ต้องไม่เป็นค่าว่าง |
| PRIMARY KEY   | เป็นคีย์ที่ใช้ในการอ้งอิงถึงชุดข้อมูล |
| AUTOINCREMENT | นับค่าขึ้นอัตโนมัติ |
| UNIQUE        | ห้ามซ้ำ |
| DEFAULT	| ค่าเริ่มต้น |
| CHECK		| เงื่อนไข |


**ตาราง Employee**
| Column    | คำอธิบาย        | 
| ----------| -------------- |
| id        | รหัสพนักงาน      | 
| fname     | ชื่อจริงพนักงาน    |
| lname     | นามสกุลพนักงาน   |
| address   | ที่อยู่พนักงาน      |
| tel       | เบอร์โทรพนักงาน  |
| salary    | เงินเดือนพนักงาน  |

**ตาราง Product**
| Column    	| คำอธิบาย       | 
| --------------| ------------- |
| product_id	| รหัสสินค้า       | 
| product_name  | ชื่อสินค้า        |
| description   | รายละเอียดสินค้า |
| price       	| ราคาสินค้า  	  |



```bash
CREATE TABLE "Employee" (
	"id"	INTEGER,
	"fname"	TEXT NOT NULL,
	"lname"	TEXT NOT NULL,
	"address"	TEXT,
	"tel"	TEXT,
	"salary"	NUMERIC NOT NULL DEFAULT 25000,
	PRIMARY KEY("id" AUTOINCREMENT)
);

CREATE TABLE "Product" (
	"product_id"	TEXT NOT NULL,
	"product_name"	TEXT NOT NULL UNIQUE,
	"description"	TEXT,
	"price"	REAL NOT NULL,
	PRIMARY KEY("product_id")
);
```

#### การบันทึกข้อมูล
- ต้องจับคู่ Column กับ Value ที่ต้องการบันทึกให้ตรงกัน

```bash
#แบบมีการระบุคอลัมน์
INSERT INTO Employee (fname,lname,address,tel,salary)
VALUES ("สมชาย","ใจดี","กรุงเทพมหานคร","0812345678",15000)

#แบบไม่ต้องระบุคอลัมน์
INSERT INTO Employee 
VALUES (NULL,"วุฒิชัย","แสงงาม","ระยอง","0912345678",25000)

#บันทึกข้อมูลบางคอลัมน์ (ค่าที่ไม่ได้กรอกต้องสามารถเป็นค่าว่างได้)
INSERT INTO Employee (fname,lname,salary) 
VALUES ("สมหญิง","ร่มเกล้า",40000)

#บันทึกข้อมูลหลายรายการในครั้งเดียว (insert > 1 row)
INSERT INTO Employee (fname,lname,salary)
VALUES ("สมพร","รักเรียน",15000),("สกาวใจ","ยิ้มเก่ง",12000),("สงกราน","รักเรียน",20000)

#บันทึกข้อมูลค่า Default (ระบุค่าเริ่มต้นให้กับข้อมูล กรณีไม่ได้เพิ่มค่าเข้ามา)
INSERT INTO Employee (fname,lname)
VALUES ("ภูผา","แกร่งดั่งภูผา")

#บันทึกข้อมูลไม่ซ้ำด้วย Unique (ตัว product_id เป็น PK มีคุณสมบัติ UNIQUE ในตัว)
INSERT INTO Products (product_id,product_name,description,price)
VALUES ("P001","มะม่วงกวน",NULL,150)
VALUES ("P002","มะม่วงเขียวเสวย",NULL,120)
```

#### เช็คข้อมูลตามเงื่อนไขด้วย Check
- กำหนดเงื่อนไขลงไปในคอลัมน์ เพื่อตรวจเช็คว่าค่าของข้อมูลที่รับเข้ามาตอนที่ทำการบันทึก ถูกต้อง/ตรงเงื่อนไข หรือไม่

**ตัวดำเนินการ**
**ตาราง Product**
| เครื่องหมาย	  | คำอธิบาย	    |
| --------------| -------------- |
| >  		| มากกว่า	  | 
| <  		| น้อยกว่า	   |
| >= 		| มากกว่าหรือเท่ากับ |
| <= 		| น้อยกว่าหรือเท่ากับ |

```bash
#อัปเดตตาราง Product 
#จำเป็นต้องลบข้อมูลเก่าออก เนื่องจากข้อมูลชุดเก่ายังไม่ได้รับการตั้งเงื่อนไข อาจส่งผลให้การแสดงข้อมูลผิดพลาด
CREATE TABLE "Products" (
	"product_id"	TEXT NOT NULL,
	"product_name"	TEXT NOT NULL UNIQUE,
	"description"	TEXT,
	#price ต้องไม่เป็นค่าว่าง และต้องมีค่ามากกว่าหรือกับ 100
	"price"	REAL NOT NULL CHECK(price>=100),
	PRIMARY KEY("product_id")
);

#เพิ่มข้อมูล
INSERT INTO Products (product_id,product_name,description,price)
#false
VALUES ("P001","มะม่วงกวน",NULL,90) 
#true
VALUES ("P001","มะม่วงกวน",NULL,100) 
```
