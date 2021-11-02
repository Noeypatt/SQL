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

### การสร้างฐานข้อมูล

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


### การสร้างตารางข้อมูล
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

### การบันทึกข้อมูล
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

### การสอบถามข้อมูล (SELECT)
- สอบถามข้อมูลภายในตาราง: query data
- เป็นคำสั่งเพื่อใช้ในการเรียกดูข้อมูลภายในตาราง ตามเงื่อนไขที่เรากำหนด

```bash
#SELECT ชื่อคอลัมน์
#FROM ชื่อตาราง

#การสอบถามข้อมูลตามลำดับคอลัมน์ที่ระบุ
SELECT fname,lname
FROM Employee
--
SELECT product_id, product_name,price
FROM Products

#การสอบถามข้อมูลทุกคอลัมน์ (เรียงลำดับตามคอลัมน์ และข้อมูลที่ได้ระบุไป)
SELECT *
FROM Products
```
##### ตั้งชื่อคอลัมน์ด้วย AS
- เพื่อสื่อให้เข้าใจได้ว่าตัวคอลัมน์นี้ทำการเก็บค่าอะไร
- ไม่มีผลต่อคอลัมน์โดยตรง

```bash
SELECT fname AS "First Name",lname AS "Last Name"
FROM Employee
```
**ผลลัพธ์ที่ได้**

| First Name | Last Name |
| ---------- | --------- |
| ...	     | ...       |

##### แสดงข้อมูลไม่ซ้ำด้วย DISTINCT
- นำข้อมูลที่ซ้ำออกไป
- 
```bash
#นำชื่อพนักงานที่ซ้ำออกไป
SELECT DISTINCT fname
FROM Employee

#จำนวนข้อมูลที่ซ้ำ
SELECT DISTINCT fname AS "ชื่อพนักงาน",count(*) AS "จำนวน"
FROM Employee
GROUP By fname
```

##### การสอบถามข้อมูลแบบมีเงื่อนไข (WHERE)
```bash
SELECT *
FROM Employee
WHERE salary = 30000 #ดึงข้อมูลพนักงานที่มีเงินเดือน = 30000
```

##### ตัวดำเนินการเปรียบเทียบ
| เครื่องหมาย	  | คำอธิบาย	    |
| --------------| -------------- |
| =  		| เท่ากับ   	   | 
| <  		| น้อยกว่า	   |
| >  		| มากกว่า	  | 
| <= 		| น้อยกว่าหรือเท่ากับ |
| >= 		| มากกว่าหรือเท่ากับ |
| !=  		| ไม่เท่ากับ        |

##### เชื่อมเงื่อนไขด้วย AND OR NOT
| คำสั่ง	  | คำอธิบาย	    |
| --------------| -------------- |
| AND  		| เงื่อนไขทั้งสองฝั่งเป็นจริง | 
| OR  		| เงื่อนไขฝั่งใดฝั่งหนึ่งเป็นจริง หรือเป็นจริงทั้งคู่ |
| NOT  		| เป็นค่าตรงกันข้าม |

```bash
SELECT *
FROM Employee

#AND พนักงานมีเงินเดือนมากกว่า 30000 และอาศัยอยู่ที่ "กรุงเทพมหานคร"
WHERE salary > 30000 AND address = "กรุงเทพมหานคร" 
#OR  พนักงานมีเงินเดือนมากกว่า 30000 หรืออาศัยอยู่ที่ "กรุงเทพมหานคร"
WHERE salary > 30000 OR address = "กรุงเทพมหานคร"
#NOT พนักงานที่ไม่ได้อาศัยอยู่ที่ "กรุงเทพมหานคร"
WHERE NOT address = "กรุงเทพมหานคร"
```

##### เช็คค่าว่างด้วย IS NULL
- เช็คว่าข้อมูลของเรามีค่าว่าง NULL หรือไม่ 
- ชื่อคอลัมน์ IS NULL: เป็นค่าว่าง
- ชื่อคอลัมน์ NOT NULL: ไม่เป็นค่าว่าง
```bash
SELECT  *
FROM Employee
WHERE tel is NULL #ชุดข้อมูลที่มีเบอร์ติดต่อเป็นค่าว่าง
WHERE salary >= 25000 AND tel IS NULL #ชุดข้อมูลที่มีเบอร์ติดต่อเป็นค่าว่าง และมีเงินเดือนมากกว่าเท่ากับ 25000
```

##### เรียงลำดับข้อมูลด้วย ORDER BY
| คำสั่ง	  | คำอธิบาย	    |
| --------------| -------------- |
| ASC  		| เรียงจากน้อยไปมาก 0-9 aA-zZ พยัญชนะ-สระ |
| DESC  	| เรียงจากมากไปน้อย 9-0 Aa-Zz สระ-พยัญชนะ | 

```bash
SELECT  *
FROM Employee
WHERE address NOT NULL AND tel NOT NULL #ที่อยู่และเบอร์โทรต้องไม่เป็นค่าว่าง

#เรียงจากน้อยไปมาก
ORDER By  salary ASC
#เรียงจากมากไปน้อย
ORDER By  salary DESC
```

##### จำกัดข้อมูลในการแสดงผลด้วย LIMIT
- จำกัดข้อมูลในการแสดงผล

```bash
SELECT  *
FROM Employee
WHERE address = "กรุงเทพมหานคร"
ORDER By salary DESC
LIMIT 3 #ข้อมูล 3 ชุดแรก
```

##### เพิ่มข้อมูลด้วยข้อมูลตารางอื่น
- นำข้อมูลในตาราง A ไปบันทึกลงในตาราง B 
- ชนิดข้อมูลต้องตรงกัน

ตาราง Product
| Name | Type  |
| ---- | ----- |
| product_id   | TEXT NOT NULL |
| product_name | TEXT NOT NULL UNIQUE |
| description  | TEXT |
| price        | REAL NOT NULL CHECK("price" >= 100) |


ตาราง Product_Place
| Name | Type |
| ---- | ---- |
| id   | TEXT NOT NULL |
| name | TEXT NOT NULL UNIQUE |
| price  | REAL NOT NULL |
| market | TEXT NOT NULL DEFAULT 'ตลาดสด' |

```bash
#ทำการดึง product_id,product_name,price จากตาราง Product มาใส่ตาราง Product_Place
INSERT  INTO Product_Place (id,name,price)
SELECT product_id,product_name,price
FROM Products
WHERE เงื่อนไขที่ต้องการ
```

##### การอัปเดตข้อมูลในตาราง

```bash
UPDATE Employee

#แก้ไขข้อมูลทีละแถว
#ให้พนักงานที่ id=10 เปลี่ยนชื่อเป็น "ธิดา ภูคำเวียง"
SET fname="ธิดา", lname="ภูคำเวียง"
WHERE id="10"

#แก้ไขข้อมูลทีละหลายๆแถว
#ให้พนักงานทุกคนที่ยังไม่ใส่เบอร์โทร ให้มีเบอร์โทรเป็นเบอร์ "02-xxxxxx"
SET tel = "02-xxxxxx"
WHERE tel IS NULL
#ให้พนักงานทุกคนที่อาศัยอยู่ที่ กรุงเทพมหานคร มีเงินเดือน 25000
SET salary=25000
WHERE address="กรุงเทพมหานคร"
```
_ข้อควรระวัง_
- หากไม่มีการระบุเงื่อนไข มันจะทำการอัปเดตทุกค่าที่อยู่ในตาราง


##### การลบข้อมูลในตาราง

```bash
DELETE FROM Employee

#ลบข้อมูลทีละแถว
#ลบข้อมูลของพนักงานชื่อ "สุรศักดิ์"
WHERE fname="สุรศักดิ์"

#ลบข้อมูลทีละหลายๆแถว
#ลบพนักงานที่ไม่ระบุที่อยู่ออกไป
WHERE address IS NULL
```

_ข้อควรระวัง_
- หากไม่มีการระบุเงื่อนไข มันจะทำการลบทุกข้อมูลที่อยู่ในตาราง
