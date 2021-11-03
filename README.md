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

##### การใช้ LIKE และ WildCard Character
LIKE: เปรียบเทียบส่วนประกอบของข้อมูล 
- ส่วนมากใช้ร่วมกับคำสั่ง WHERE
- นิยมใช้กับพวกตัวเลข ตัวอักษร (ระบบค้นหา)

WildCard Character: ส่วนที่ใช้กำหนดองค์ประกอบของข้อมูล
- % : ตัวอักษรตั้งแต่ 0 จนไปถึงกี่ตัวก็ได้ (ระบุขึ้นต้น ลงท้าย หรือส่วนประกอบ ไม่มีการกำหนด หรือจำกัดตัวอักษร | ไม่รู้จำนวน หรือลำดับที่ชัดเจน)
- _ : ตัวอักษร 1 ตัว (มีการจำกัดจำนวนตัวอักษร หรือลำดับที่ชัดเจน)
- สามารถระบุได้ 3 รูปแบบ
   - ขึ้นต้น
   - ส่วนประกอบ (ส่วนตรงกลาง)
   - ลงท้าย

```bash
SELECT * FROM Employee

# การใช้ %
#หาพนักงานที่ชื่อขึ้นต้นด้วย 'ส' (ส่วนประกอบกับส่วนลงท้ายจะเป็นอะไรก็ได้)
WHERE fname like "ส%"
#หาพนักงานที่ชื่อประกอบด้วย 'ส' (ส่วนขึ้นต้นกับส่วนลงท้ายจะเป็นอะไรก็ได้)
WHERE fname like "%ส%"
#หาพนักงานที่ชื่อลงท้ายด้วย 'ส' (ส่วนขึ้นต้นกับส่วนประกอบจะเป็นอะไรก็ได้)
WHERE fname like "%ส"
#หาพนักงานที่เงินเดือนประกอบด้วยเลข 5 
WHERE salary like "%5%"

# การใช้ _
#หาพนักงานที่เงินเดือนขึ้นต้นด้วยอะไรก็ได้ 1 หนึ่งอักษร แต่ตัวอักษรลำดับที่ 2-3 ต้องเป็นเลข 0 (ไม่ได้บอกเลขลงท้าย)
WHERE salary like "_00%"
#หาพนักงานที่เงินเดือนขึ้นต้นด้วยอะไรก็ได้ 1 หนึ่งอักษร แต่ตัวอักษรลำดับที่ 2 ต้องเป็นเลข 5 และลงท้ายด้วยเลข 0 
WHERE salary like "_5%0"
#หาพนักงานที่เงินเดือนขึ้นต้นด้วย 7 ลงท้ายด้วยเลข 0 และมีรวมจำนวนตัวอักษรได้ 5 ตัวอักษร
WHERE salary like "7___0"
#หาพนักงานที่เงินเดือนขึ้นต้นด้วย 7 เลขลำดับที่ 2 เป็นเลข 5 ลงท้ายด้วยเลข 0 และมีรวมจำนวนตัวอักษรได้ 5 ตัวอักษร
WHERE salary like "75__0"


#หาพนักงานที่มีที่อยู่ขึ้นต้นด้วย นคร
WHERE address like "นคร%"
#หาพนักงานที่มีที่อยู่ประกอบไปด้วย ย
WHERE address like "%ย%"
#หาพนักงานที่มีที่อยู่ขึ้นต้นด้วย ก ลงท้ายด้วย ร
WHERE address like "ก%ร"
```

##### ตัวดำเนินการทางคณิตศาสตร์ (+ - * /)

```bash
SELECT fname AS  "ชื่อพนักงาน", 
 lname AS "นามสกุลพนักงาน", 
 salary AS "เงินเดือน",
 salary + 2000 AS "ค่าบำรุงอุปกรณ์",
 salary - 1000 AS "ค่าเดินทาง",
 salary * 12 AS "รายได้ต่อปี",
 salary /  30 AS "รายได้ต่อวัน"
FROM Employee
#เพิ่มเงื่อนไขก็ได้
WHERE ( salary*12 )  >  200000
```

##### คำสั่ง IN และ NOT IN
IN: มีคุณสมบัติคล้าย OR (เป็นการยุบการทำงานจาก เงื่อนไข OR เงื่อนไข OR เงื่อนไข OR .. => (เงื่อนไข,เงื่อนไข,เงื่อนไข) )
NOT IN: ให้ผลลัพธ์ตรงกันข้ามกับ IN

```bash
SELECT * FROM Employee
#IN
#พนักงานที่อยู่จังหวัดกรุงเทพมหานคร หรือระยอง
WHERE address  IN ("กรุงเทพมหานคร","ระยอง")
#NOT IN
#พนักงานที่ไม่ได้อยู่จังหวัดกรุงเทพมหานคร หรือระยอง
WHERE address  NOT IN ("กรุงเทพมหานคร","ระยอง")
```

##### คำสั่ง BETWEEN
BETWEEN: มีคุณสมบัติคล้าย AND สามารถระบุช่วงของข้อมูลได้

```bash
SELECT * FROM Employee
#พนักงานที่มีเงินเดือนในช่วง 30000-50000
WHERE salary BETWEEN 30000 AND 50000
```

##### ฟังก์ชั่นคณิตศาสตร์

| ฟังก์ชั่น   | คำอธิบาย   |
| ------- | --------- |
| count() | จำนวนข้อมูล |
| max()   | ค่าสูงสุด    |
| min()   | ค่าต่ำสุด    |
| sum()   | ผลรวม     |
| avg()   | ค่าเฉลี่ย    |


```bash
SELECT 
count(*) AS "จำนวนพนักงาน",
max(salary) AS "เงินเดือนค่าสูงสุด",
min(salary) AS "เงินเดือนค่าต่ำสุด",
sum(salary) AS "เงินเดือนยอดรวมทั้งหมด",
avg(salary) AS "ค่าเฉลี่ยนเงินเดือนพนักงาน"
FROM Employee
```

##### CASE...WHEN
- สอบถามข้อมูลรายกรณี

```bash
#พนักงานที่มีเงินเดือนเท่านี้ มีตำแหน่งอะไร
SELECT fname,lname,salary,
(CASE salary 
WHEN 40000 THEN "หัวหน้าทีม"
WHEN 35000 THEN "รองหัวหน้าทีม"
WHEN 30000 THEN "เลขา"
WHEN 25000 THEN "ประชาสัมพันธ์"
ELSE "สมาชิกทั่วไป" END) AS "ตำแหน่ง"
FROM Employee
ORDER By salary DESC
```

##### LIKE ร่วมกับ CASE...WHEN

ตาราง Skills
```bash
CREATE TABLE "Skills" (
	"id"	INTEGER NOT NULL,
	"name"	TEXT NOT NULL,
	"language"	TEXT,
	PRIMARY KEY("id" AUTOINCREMENT)
);

#นำชื่อพนักงานมาใส่
INSERT INTO Skills(name)
SELECT fname 
FROM Employee

#อัปเดตภาษาที่พนักงานทุกคนที่ชื่อมี ส เป็นตัวประกอบ
UPDATE Skills
SET language = "ภาษาอังกฤษ ภาษาจีน"
WHERE name like "%ส%"
```

```base
#ดูว่าพนักงานแต่ละคนสามารถสื่อสารได้กี่ภาษา
SELECT 
name,
language,
(CASE 
WHEN language like "%ภาษา%ภาษา%ภาษา%" THEN 3
WHEN language like "%ภาษา%ภาษา%" THEN 2
WHEN language like "%ภาษา%" THEN 1
ELSE 0 END
) AS "จำนวนภาษา"
FROM Skills
```

##### จัดกลุ่ม Group By
- ใช้งานร่วมกับคำสั่ง `SELECT`
- จัดกลุ่มข้อมูลที่มีลักษณะซ้ำกัน เพื่อใช้ในการค้นหา
- ใช้สรุปข้อมูล (จำนวนทั้งหมด)
- SELECT | GROUP BY

```bash
SELECT * FROM Employee
#จัดกลุ่ม โดยไม่นำชื่อซ้ำมาแสดง
GROUP BY fname 

#จำนวนพนักงานในแต่ละจังหวัด"
SELECT 
address AS  "ที่อยู่",
count(*) AS "จำนวนคนที่อยู่ในแต่ละจังหวัด"
FROM Employee
GROUP BY address 
```

##### Group By และ ฟังก์ชั่น

```bash
#หาค่าเงินเดือนต่ำสุด ค่าสูงสุดของข้อมูล ตามข้อมูลที่อยู่ 
#เช่น พนักงานที่ทำงานกทม. ค่าเงินเดือนสูงสุดคือเท่าไหร่ และค่าเงินเดือนต่ำสุดคือเท่าไหร่ ค่าเฉลี่ยน และผลรวมของพนักงานในจังหวัดนั้นๆ เป็นเท่าไหร่
SELECT 
fname AS "ชื่อพนักงาน" ,lname AS "นามสกุลพนักงาน",address AS "ที่อยู่ของพนักงาน", salary AS "เงินเดือน", 
min(salary) AS "เงินเดือนต่ำสุด", max(salary) AS "เงินเดือนสูงสุด",
avg(salary) AS "ค่าเฉลี่ยเงินเดือน",sum(salary) AS "รวม"
FROM Employee
GROUP BY address
```

##### เงื่อนไขกลุ่มข้อมูล (Having)
- คุณสมบัติคล้าย `WHERE`
- ทำงานร่วมกับ `GROUP BY`
- หลังจากจัดกลุ่มเสร็จ ต้องการกำหนดเงื่อนไขลงไป
- เป็นการกำหนดเงื่อนไขให้กลุ่มข้อมูล

```bash
#หาเงินเดือนรวมตามข้อมูลที่อยู่ (GROUP BY address)
SELECT address AS "ที่อยู่" , count(*) AS "จำนวน" ,sum(salary) AS "เงินเดือนรวม"
FROM Employee
GROUP BY address
#โดยมีเงื่อนไขว่าจะกรองข้อมูลเอาเฉพาะจังหวัดที่มีเงินเดือนรวม >= 25000 และต้องมีจำนวนพนักงาน > 1
HAVING sum(salary) >=25000 AND count(*)  > 1
```

#####  Sub Query
- เขียน query ซ้อน query
- main query | sub query
- sub query ต้องเขียนภายในวงเล็บ  
- ไม่สามารถใช้ `ORDER BY | GROUP BY | BETWEEN` ต่อท้ายได้

ตาราง Customer
```bash
CREATE TABLE "Customer" (
	"id"	INTEGER NOT NULL,
	"fname"	TEXT NOT NULL,
	"lname"	TEXT NOT NULL,
	"city"	TEXT,
	"phone"	TEXT,
	"email"	TEXT,
	PRIMARY KEY("id")
);
```

```bash
#ทำการสอบถามว่ามีพนักงานคนใดบ้างที่อาศัยอยู่จังหวัดเดียวกับลูกค้า (ระบุชื่อลูกค้า)
#main query
SELECT fname,lname,address FROM Employee
#sub query
WHERE address = (SELECT city FROM Customer WHERE fname ="โจศักดิ์")
```

##### สร้างตารางด้วย Sub Query

```bash
#มาเก็บลงอีกตาราง เพื่อทำเป็นตารางพนักงานดีเด่น (ถ้ายังไม่มีตารางดังกล่าว จะทำการสร้างตารางใหม่ขึ้นมา)
CREATE TABLE bestEmployee AS
#นำข้อมูลของพนักงานที่อาศัยอยู่จังหวัดเดียวกับลูกค้าชื่อ "โจศักดิ์" และตัวพนักงานมีเงินเดือนมากกว่า 15000 (เพิ่มเงินเดือนให้)
SELECT id ,fname ,lname,address,salary + 5000 AS "salary" FROM Employee
WHERE address = (SELECT city FROM Customer WHERE fname ="โจศักดิ์" ) AND salary > 20000
```

##### การเชื่อมตาราง / การรวมตาราง (Join Table)
- การนำเอาตาราง 2 ตารางมารวมกันโดยข้อมูลจะต้องมีส่วนที่เชื่อมกันได้ในทั้ง 2 ตาราง
- โดยดำเนินการผ่าน Primary Key (PK) และ Foreign Key (FK)
- Primary Key: คีย์หลัก
- Foreign Key: คีย์รอง

ตารางข้อมูลสินค้า (Product)
| key          | คำอธิบาย        |     
| ------------ | -------------- |
| product_id   | รหัสสินค้า(PK)    |
| product_name | ชื่อสินค้า         |
| price        | ราคา           |
| unit         | หน่วย           | 
| type_id      | ประเภทสินค้า(FK) |
| status       | สถานะ          |

```bash
CREATE TABLE "Products" (
	"product_id"	TEXT,
	"product_name"	TEXT,
	"price"	REAL,
	"unit"	TEXT DEFAULT '-',
	"type_id"	TEXT,
	"status"	INTEGER,
	PRIMARY KEY("product_id")
);
```

ตารางข้อมูลประเภทสินค้า (Categories)
| key  | คำอธิบาย        |     
| ---- | -------------- |
| id   | รหัสสินค้า(PK)    |
| name | ชื่อสินค้า         |


```bash
CREATE TABLE "Categories" (
	"id"	TEXT,
	"name"	TEXT NOT NULL,
	PRIMARY KEY("id")
);
```

##### รวมตารางด้วย WHERE
- ใช้ type_id ของ product เป็น Foreign Key ชี้ไปยัง id ของตาราง categories
- เปรียบเทียบภายในคำสั่ง `WHERE` ด้วยเครื่องหมาย `=`
- ไม่สามารถใส่เงื่อนไขอื่นเพิ่มเติมได้ (ต้องย้ายไปใช้คำสั่ง JOIN)
- ข้อมูลทั้ง 2 ตารางต้องมีความสัมพันธ์กัน ถึงจะเอามาแสดง เช่นถ้าในตาราง Products ไม่ได้ระบุ type_id ไว้ ข้อมูลชุดนี้ก็จะไม่ถูกนำมาแสดง

```bash
#นำข้อมูล ชื่อสินค้ากับราคา จากตาราง Products และชื่อหมวดหมู่จากตาราง Categories มาแสดง
SELECT Products.product_name AS "ชื่อสินค้า",Products.price AS "ราคา",Categories.name AS "หมวดหมู่" 
FROM Products,Categories
#โดยมีเงื่อนไขว่า รหัสหมวดหมู่ของสินค้าในตาราง Products ต้องตรงกับรหัสของหมวดหมู่ในตาราง Categories
WHERE Products.type_id = Categories.id
```
