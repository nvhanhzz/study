# Learning SQL - Tóm tắt Chapter 2

> **Sách:** Learning SQL, 3rd Edition - Alan Beaulieu (O'Reilly)  
> **Ngày học:** 2026-01-17

---

## 📖 Chapter 2: Creating and Populating a Database

Chương này hướng dẫn cách tạo database, định nghĩa các kiểu dữ liệu, tạo bảng và thực hiện các thao tác CRUD (Create, Read, Update, Delete) cơ bản.

---

## 1. Tạo MySQL Database

### 1.1. Hai cách tiếp cận

| Cách                     | Mô tả                                  | Ưu điểm                    |
| ------------------------ | -------------------------------------- | -------------------------- |
| **Cài đặt MySQL Server** | Download MySQL 8.0+ và Sakila database | Dữ liệu được lưu vĩnh viễn |
| **Online Session**       | Sử dụng session tạm thời trên web      | Nhanh, không cần cài đặt   |

### 1.2. Kết nối MySQL Command-Line Tool

```bash
# Đăng nhập với root
mysql -u root -p

# Đăng nhập và chọn database luôn
mysql -u root -p sakila
```

### 1.3. Các lệnh cơ bản

```sql
-- Xem danh sách databases
SHOW DATABASES;

-- Chọn database để làm việc
USE sakila;

-- Xem danh sách bảng
SHOW TABLES;

-- Xem cấu trúc bảng
DESCRIBE customer;
-- hoặc viết tắt
DESC customer;

-- Lấy thời gian hiện tại
SELECT NOW();
```

> 💡 **Lưu ý về FROM clause:** Một số database (như Oracle) yêu cầu phải có FROM clause. Oracle cung cấp bảng `dual` để sử dụng trong trường hợp này. MySQL cũng hỗ trợ `dual` để tương thích.

---

## 2. MySQL Data Types (Kiểu dữ liệu)

### 2.1. Character Data (Dữ liệu ký tự)

#### A. Fixed-length vs Variable-length

| Kiểu         | Đặc điểm                           | Kích thước tối đa |
| ------------ | ---------------------------------- | ----------------- |
| `CHAR(n)`    | Độ dài cố định, padding với spaces | 255 bytes         |
| `VARCHAR(n)` | Độ dài thay đổi, không padding     | 65,535 bytes      |

```sql
-- Ví dụ
fname CHAR(20)     -- Luôn chiếm 20 bytes
fname VARCHAR(20)  -- Chỉ chiếm số bytes thực tế + 1-2 bytes length
```

**Khi nào dùng CHAR vs VARCHAR?**
- `CHAR`: Khi tất cả giá trị có cùng độ dài (mã quốc gia: 'VN', 'US', 'JP')
- `VARCHAR`: Khi độ dài giá trị khác nhau (tên người, địa chỉ)

#### B. Character Sets (Bộ ký tự)

```sql
-- Xem các character sets hỗ trợ
SHOW CHARACTER SET;
```

| Character Set | Mô tả                           | Maxlen  |
| ------------- | ------------------------------- | ------- |
| `latin1`      | West European                   | 1 byte  |
| `utf8`        | UTF-8 Unicode                   | 3 bytes |
| `utf8mb4`     | UTF-8 Unicode (default MySQL 8) | 4 bytes |

```sql
-- Chỉ định character set cho cột
fname VARCHAR(20) CHARACTER SET latin1

-- Chỉ định cho toàn bộ database
CREATE DATABASE european_sales CHARACTER SET latin1;
```

#### C. Text Types (Kiểu văn bản lớn)

| Kiểu         | Kích thước tối đa           | Ghi chú              |
| ------------ | --------------------------- | -------------------- |
| `TINYTEXT`   | 255 bytes                   | Ít dùng (varchar đủ) |
| `TEXT`       | 65,535 bytes (~64 KB)       | Ít dùng (varchar đủ) |
| `MEDIUMTEXT` | 16,777,215 bytes (~16 MB)   | Cho văn bản dài      |
| `LONGTEXT`   | 4,294,967,295 bytes (~4 GB) | Cho documents lớn    |

> ⚠️ **Lưu ý với TEXT types:**
> - Dữ liệu bị truncate nếu vượt quá kích thước
> - Trailing spaces không bị xóa
> - Khi sorting/grouping chỉ dùng 1,024 bytes đầu tiên

**So sánh với các DBMS khác:**

| DBMS       | Kiểu cho large text             |
| ---------- | ------------------------------- |
| MySQL      | `MEDIUMTEXT`, `LONGTEXT`        |
| Oracle     | `CLOB` (Character Large Object) |
| SQL Server | `TEXT`, `VARCHAR(MAX)`          |

---

### 2.2. Numeric Data (Dữ liệu số)

#### A. Integer Types (Số nguyên)

| Kiểu        | Signed Range                   | Unsigned Range    | Bytes |
| ----------- | ------------------------------ | ----------------- | ----- |
| `TINYINT`   | -128 → 127                     | 0 → 255           | 1     |
| `SMALLINT`  | -32,768 → 32,767               | 0 → 65,535        | 2     |
| `MEDIUMINT` | -8,388,608 → 8,388,607         | 0 → 16,777,215    | 3     |
| `INT`       | -2,147,483,648 → 2,147,483,647 | 0 → 4,294,967,295 | 4     |
| `BIGINT`    | -2^63 → 2^63-1                 | 0 → 2^64-1        | 8     |

```sql
-- Ví dụ sử dụng UNSIGNED
person_id SMALLINT UNSIGNED  -- Chỉ số dương: 0 → 65,535
quantity TINYINT UNSIGNED    -- 0 → 255
```

> 💡 **Tip:** Chọn kiểu đủ lớn cho dữ liệu nhưng không lãng phí storage.

#### B. Floating-Point Types (Số thực)

| Kiểu          | Phạm vi   | Precision        |
| ------------- | --------- | ---------------- |
| `FLOAT(p,s)`  | ±3.4E+38  | Single precision |
| `DOUBLE(p,s)` | ±1.7E+308 | Double precision |

**Tham số:**
- `p` (precision): Tổng số chữ số (trái + phải dấu thập phân)
- `s` (scale): Số chữ số sau dấu thập phân

```sql
-- Ví dụ
price FLOAT(4,2)  -- Lưu được: 27.44, 8.19
                  -- 17.8675 → làm tròn thành 17.87
                  -- 178.375 → LỖI (vượt precision)
```

---

### 2.3. Temporal Data (Dữ liệu thời gian)

#### A. Các kiểu temporal

| Kiểu        | Format              | Phạm vi                 | Use case                  |
| ----------- | ------------------- | ----------------------- | ------------------------- |
| `DATE`      | YYYY-MM-DD          | 1000-01-01 → 9999-12-31 | Ngày sinh, ngày giao hàng |
| `DATETIME`  | YYYY-MM-DD HH:MI:SS | 1000-01-01 → 9999-12-31 | Thời điểm order, shipment |
| `TIMESTAMP` | YYYY-MM-DD HH:MI:SS | 1970-01-01 → 2038-01-18 | Auto track last modified  |
| `YEAR`      | YYYY                | 1901 → 2155             | Năm xuất bản              |
| `TIME`      | HHH:MI:SS           | -838:59:59 → 838:59:59  | Thời gian elapsed         |

#### B. Date Format Components

| Component | Ý nghĩa        | Phạm vi   |
| --------- | -------------- | --------- |
| `YYYY`    | Năm (4 chữ số) | 1000-9999 |
| `MM`      | Tháng          | 01-12     |
| `DD`      | Ngày           | 01-31     |
| `HH`      | Giờ            | 00-23     |
| `MI`      | Phút           | 00-59     |
| `SS`      | Giây           | 00-59     |

#### C. Fractional Seconds (Phần thập phân của giây)

```sql
-- Hỗ trợ đến 6 decimal places (microseconds)
created_at DATETIME(2)   -- Độ chính xác đến 1/100 giây
log_time TIMESTAMP(6)    -- Độ chính xác đến microsecond
```

#### D. TIMESTAMP vs DATETIME

| Đặc điểm    | TIMESTAMP                  | DATETIME           |
| ----------- | -------------------------- | ------------------ |
| Phạm vi     | 1970-2038                  | 1000-9999          |
| Auto update | ✅ Có thể tự động cập nhật  | ❌ Không            |
| Timezone    | Được convert theo timezone | Lưu nguyên giá trị |

```sql
-- TIMESTAMP tự động cập nhật
last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
```

> ⚠️ **So sánh với các DBMS khác:**
> - **Oracle**: Hỗ trợ từ 4712 BC đến 9999 AD
> - **SQL Server**: 1753 AD đến 9999 AD (datetime2: từ 1 AD)
> - **MySQL**: 1000 AD đến 9999 AD

---

## 3. Table Creation (Tạo bảng)

### 3.1. Quy trình thiết kế bảng

```
Step 1: Design → Step 2: Refinement → Step 3: Build SQL
```

### 3.2. Step 1: Design (Brainstorm)

Ví dụ thiết kế bảng `person`:

| Cột            | Kiểu         | Giá trị cho phép |
| -------------- | ------------ | ---------------- |
| name           | VARCHAR(40)  |                  |
| eye_color      | CHAR(2)      | BL, BR, GR       |
| birth_date     | DATE         |                  |
| address        | VARCHAR(100) |                  |
| favorite_foods | VARCHAR(200) |                  |

### 3.3. Step 2: Refinement (Chuẩn hóa - Normalization)

**Các vấn đề cần giải quyết:**

| Vấn đề                              | Giải pháp                                                      |
| ----------------------------------- | -------------------------------------------------------------- |
| `name` là compound (first + last)   | Tách thành `first_name`, `last_name`                           |
| Không có unique identifier          | Thêm `person_id` làm PRIMARY KEY                               |
| `address` là compound               | Tách thành `street`, `city`, `state`, `country`, `postal_code` |
| `favorite_foods` chứa nhiều giá trị | Tạo bảng riêng `favorite_food` với FOREIGN KEY                 |

**Kết quả sau chuẩn hóa:**

**Bảng `person`:**

| Cột         | Kiểu              | Ghi chú     |
| ----------- | ----------------- | ----------- |
| person_id   | SMALLINT UNSIGNED | PRIMARY KEY |
| first_name  | VARCHAR(20)       |             |
| last_name   | VARCHAR(20)       |             |
| eye_color   | CHAR(2)           | BR, BL, GR  |
| birth_date  | DATE              |             |
| street      | VARCHAR(30)       |             |
| city        | VARCHAR(20)       |             |
| state       | VARCHAR(20)       |             |
| country     | VARCHAR(20)       |             |
| postal_code | VARCHAR(20)       |             |

**Bảng `favorite_food`:**

| Cột       | Kiểu              | Ghi chú     |
| --------- | ----------------- | ----------- |
| person_id | SMALLINT UNSIGNED | FK → person |
| food      | VARCHAR(20)       |             |

> PRIMARY KEY = (person_id, food) - Compound key

### 3.4. Step 3: Building SQL Schema

#### A. Tạo bảng với constraints

```sql
CREATE TABLE person (
  person_id SMALLINT UNSIGNED,
  fname VARCHAR(20),
  lname VARCHAR(20),
  eye_color ENUM('BR','BL','GR'),  -- MySQL ENUM thay cho CHECK constraint
  birth_date DATE,
  street VARCHAR(30),
  city VARCHAR(20),
  state VARCHAR(20),
  country VARCHAR(20),
  postal_code VARCHAR(20),
  CONSTRAINT pk_person PRIMARY KEY (person_id)
);
```

#### B. Check Constraint vs ENUM

```sql
-- Cách 1: CHECK constraint (chuẩn SQL - MySQL cho phép nhưng KHÔNG enforce!)
eye_color CHAR(2) CHECK (eye_color IN ('BR','BL','GR'))

-- Cách 2: ENUM (MySQL-specific - ĐƯỢC enforce!)
eye_color ENUM('BR','BL','GR')
```

> ⚠️ **Quan trọng:** MySQL cho phép định nghĩa CHECK constraint nhưng **không thực thi** nó! Sử dụng ENUM thay thế.

#### C. Tạo bảng với Foreign Key

```sql
CREATE TABLE favorite_food (
  person_id SMALLINT UNSIGNED,
  food VARCHAR(20),
  CONSTRAINT pk_favorite_food PRIMARY KEY (person_id, food),
  CONSTRAINT fk_fav_food_person_id FOREIGN KEY (person_id)
    REFERENCES person (person_id)
);
```

**Giải thích:**
- `PRIMARY KEY (person_id, food)`: Compound primary key (2 cột)
- `FOREIGN KEY (person_id) REFERENCES person (person_id)`: Đảm bảo `person_id` phải tồn tại trong bảng `person`

#### D. Xem cấu trúc bảng

```sql
mysql> DESC person;
+-------------+----------------------+------+-----+---------+-------+
| Field       | Type                 | Null | Key | Default | Extra |
+-------------+----------------------+------+-----+---------+-------+
| person_id   | smallint(5) unsigned | NO   | PRI | NULL    |       |
| fname       | varchar(20)          | YES  |     | NULL    |       |
| lname       | varchar(20)          | YES  |     | NULL    |       |
| eye_color   | enum('BR','BL','GR') | YES  |     | NULL    |       |
| birth_date  | date                 | YES  |     | NULL    |       |
| ...         | ...                  | ...  | ... | ...     | ...   |
+-------------+----------------------+------+-----+---------+-------+
```

**Giải thích các cột:**
| Cột     | Ý nghĩa                                                    |
| ------- | ---------------------------------------------------------- |
| Null    | YES = cho phép NULL, NO = bắt buộc có giá trị              |
| Key     | PRI = Primary Key, MUL = Multiple (Foreign Key hoặc Index) |
| Default | Giá trị mặc định                                           |
| Extra   | Thông tin bổ sung (auto_increment...)                      |

---

## 4. NULL - Khái niệm quan trọng

### 4.1. NULL là gì?

**NULL** biểu thị **sự vắng mặt của giá trị**, không phải:
- ❌ Số 0
- ❌ Chuỗi rỗng ''
- ❌ False

### 4.2. Các trường hợp sử dụng NULL

| Trường hợp         | Ví dụ                            |
| ------------------ | -------------------------------- |
| **Not applicable** | `spouse_name` cho người độc thân |
| **Unknown**        | Chưa biết ngày sinh              |
| **Empty set**      | Chưa có địa chỉ                  |

### 4.3. Định nghĩa NOT NULL

```sql
-- Cho phép NULL (mặc định)
fname VARCHAR(20)

-- Không cho phép NULL
fname VARCHAR(20) NOT NULL
```

---

## 5. Populating and Modifying Tables (Thao tác dữ liệu)

### 5.1. AUTO_INCREMENT cho Primary Key

#### A. Cách tạo auto-increment

```sql
-- Cách 1: Khi tạo bảng
CREATE TABLE person (
  person_id SMALLINT UNSIGNED AUTO_INCREMENT,
  ...
  PRIMARY KEY (person_id)
);

-- Cách 2: Sửa bảng đã có
ALTER TABLE person MODIFY person_id SMALLINT UNSIGNED AUTO_INCREMENT;
```

#### B. Kiểm tra auto_increment

```sql
mysql> DESC person;
+-----------+---------------------+------+-----+---------+----------------+
| Field     | Type                | Null | Key | Default | Extra          |
+-----------+---------------------+------+-----+---------+----------------+
| person_id | smallint(5) unsigned| NO   | PRI | NULL    | auto_increment |
+-----------+---------------------+------+-----+---------+----------------+
```

### 5.2. INSERT Statement

#### A. Cú pháp cơ bản

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

#### B. Ví dụ INSERT

```sql
-- Insert với auto_increment: dùng NULL cho person_id
INSERT INTO person (person_id, fname, lname, eye_color, birth_date)
VALUES (NULL, 'William', 'Turner', 'BR', '1972-05-27');
-- Query OK, 1 row affected

-- Insert với đầy đủ thông tin
INSERT INTO person 
  (person_id, fname, lname, eye_color, birth_date, street, city, state, country, postal_code)
VALUES 
  (NULL, 'Susan', 'Smith', 'BL', '1975-11-02', '23 Maple St.', 'Arlington', 'VA', 'USA', '20220');
```

> 💡 **Lưu ý:**
> - Số lượng columns phải = số lượng values
> - Kiểu dữ liệu phải tương thích
> - Có thể bỏ qua columns cho phép NULL

#### C. INSERT vào bảng con (có Foreign Key)

```sql
-- Phải có person_id = 1 trong bảng person trước!
INSERT INTO favorite_food (person_id, food) VALUES (1, 'pizza');
INSERT INTO favorite_food (person_id, food) VALUES (1, 'cookies');
INSERT INTO favorite_food (person_id, food) VALUES (1, 'nachos');
```

### 5.3. SELECT Statement (Truy vấn cơ bản)

```sql
-- Lấy tất cả rows
SELECT person_id, fname, lname, birth_date FROM person;

-- Lấy row cụ thể
SELECT person_id, fname, lname, birth_date 
FROM person 
WHERE person_id = 1;

-- Tìm theo điều kiện
SELECT person_id, fname, lname, birth_date 
FROM person 
WHERE lname = 'Turner';

-- Sắp xếp kết quả
SELECT food FROM favorite_food 
WHERE person_id = 1 
ORDER BY food;

+---------+
| food    |
+---------+
| cookies |
| nachos  |
| pizza   |
+---------+
```

### 5.4. UPDATE Statement

#### A. Cú pháp

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

#### B. Ví dụ

```sql
-- Update một row
UPDATE person
SET street = '1225 Tremont St.',
    city = 'Boston',
    state = 'MA',
    country = 'USA',
    postal_code = '02138'
WHERE person_id = 1;
-- Rows matched: 1  Changed: 1  Warnings: 0

-- Update nhiều rows
UPDATE person
SET country = 'USA'
WHERE person_id < 10;
```

> ⚠️ **CẢNH BÁO:** Nếu bỏ WHERE clause, TẤT CẢ rows sẽ bị update!

### 5.5. DELETE Statement

#### A. Cú pháp

```sql
DELETE FROM table_name WHERE condition;
```

#### B. Ví dụ

```sql
-- Xóa một row
DELETE FROM person WHERE person_id = 2;
-- Query OK, 1 row affected
```

> ⚠️ **CẢNH BÁO:** Nếu bỏ WHERE clause, TẤT CẢ rows sẽ bị xóa!

---

## 6. When Good Statements Go Bad (Xử lý lỗi)

### 6.1. Duplicate Primary Key

```sql
INSERT INTO person (person_id, fname, lname, eye_color, birth_date)
VALUES (1, 'Charles', 'Fulton', 'GR', '1968-01-15');
-- ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
```

**Nguyên nhân:** Đã có row với `person_id = 1`

### 6.2. Foreign Key Violation

```sql
INSERT INTO favorite_food (person_id, food) VALUES (999, 'lasagna');
-- ERROR 1452 (23000): Cannot add or update a child row: 
-- a foreign key constraint fails
```

**Nguyên nhân:** Không có `person_id = 999` trong bảng `person`

> 💡 **Lưu ý:** Foreign key constraints chỉ được enforce với **InnoDB storage engine**.

### 6.3. Column Value Violations (ENUM)

```sql
UPDATE person SET eye_color = 'ZZ' WHERE person_id = 1;
-- ERROR 1265 (01000): Data truncated for column 'eye_color' at row 1
```

**Nguyên nhân:** 'ZZ' không nằm trong ENUM('BR','BL','GR')

### 6.4. Invalid Date Conversions

```sql
-- Sai format
UPDATE person SET birth_date = 'DEC-21-1980' WHERE person_id = 1;
-- ERROR 1292 (22007): Incorrect date value: 'DEC-21-1980'

-- Đúng cách: sử dụng STR_TO_DATE()
UPDATE person 
SET birth_date = STR_TO_DATE('DEC-21-1980', '%b-%d-%Y') 
WHERE person_id = 1;
-- Query OK
```

### 6.5. MySQL Date Format Specifiers

| Specifier | Ý nghĩa            | Ví dụ   |
| --------- | ------------------ | ------- |
| `%Y`      | Năm 4 chữ số       | 2020    |
| `%y`      | Năm 2 chữ số       | 20      |
| `%M`      | Tên tháng đầy đủ   | January |
| `%b`      | Tên tháng viết tắt | Jan     |
| `%m`      | Tháng số           | 01-12   |
| `%d`      | Ngày               | 01-31   |
| `%H`      | Giờ (24h)          | 00-23   |
| `%h`      | Giờ (12h)          | 01-12   |
| `%i`      | Phút               | 00-59   |
| `%s`      | Giây               | 00-59   |
| `%p`      | AM/PM              | AM, PM  |
| `%W`      | Tên thứ đầy đủ     | Sunday  |
| `%a`      | Tên thứ viết tắt   | Sun     |

---

## 7. The Sakila Database

### 7.1. Giới thiệu

- **Sakila** là sample database của MySQL
- Mô phỏng chuỗi cửa hàng **cho thuê DVD** (có thể tưởng tượng là video streaming)
- Được sử dụng cho các ví dụ trong sách

### 7.2. Các bảng chính

| Bảng         | Mô tả              |
| ------------ | ------------------ |
| `film`       | Phim có thể thuê   |
| `actor`      | Diễn viên          |
| `customer`   | Khách hàng         |
| `category`   | Thể loại phim      |
| `payment`    | Thanh toán         |
| `rental`     | Thông tin thuê     |
| `inventory`  | Kho (phim sẵn có)  |
| `language`   | Ngôn ngữ           |
| `film_actor` | Quan hệ film-actor |
| `store`      | Cửa hàng           |
| `staff`      | Nhân viên          |
| `address`    | Địa chỉ            |

### 7.3. Các lệnh quản lý

```sql
-- Xem tất cả bảng
SHOW TABLES;

-- Xem cấu trúc bảng customer
DESC customer;

-- Xóa bảng (nếu không cần)
DROP TABLE favorite_food;
DROP TABLE person;
```

### 7.4. XML Output (Bonus)

```bash
# Khởi động mysql với XML output
mysql -u lrngsql -p --xml sakila

# Kết quả trả về dạng XML
```

```sql
-- SQL Server sử dụng FOR XML
SELECT * FROM favorite_food FOR XML AUTO, ELEMENTS;
```

---

## ✅ Key Takeaways

### Kiểu dữ liệu

| Category                          | Sử dụng                            |
| --------------------------------- | ---------------------------------- |
| `CHAR` / `VARCHAR`                | Text có độ dài cố định / thay đổi  |
| `TEXT` types                      | Văn bản lớn (MEDIUMTEXT, LONGTEXT) |
| `INT` types                       | Số nguyên (TINYINT → BIGINT)       |
| `FLOAT` / `DOUBLE`                | Số thực                            |
| `DATE` / `DATETIME` / `TIMESTAMP` | Thời gian                          |
| `ENUM`                            | Giới hạn giá trị cho phép          |

### Constraints

| Constraint       | Mục đích                                  |
| ---------------- | ----------------------------------------- |
| `PRIMARY KEY`    | Định danh duy nhất                        |
| `FOREIGN KEY`    | Đảm bảo tham chiếu hợp lệ                 |
| `NOT NULL`       | Bắt buộc có giá trị                       |
| `ENUM`           | Giới hạn giá trị (thay CHECK trong MySQL) |
| `AUTO_INCREMENT` | Tự động tăng giá trị                      |

### CRUD Operations

| Operation  | Statement                       | Lưu ý                        |
| ---------- | ------------------------------- | ---------------------------- |
| **C**reate | `INSERT INTO ... VALUES ...`    | Dùng NULL cho auto_increment |
| **R**ead   | `SELECT ... FROM ... WHERE ...` | ORDER BY để sắp xếp          |
| **U**pdate | `UPDATE ... SET ... WHERE ...`  | ⚠️ Luôn có WHERE!             |
| **D**elete | `DELETE FROM ... WHERE ...`     | ⚠️ Luôn có WHERE!             |

### Lỗi thường gặp

1. **Duplicate Primary Key** - Trùng khóa chính
2. **Foreign Key Violation** - Tham chiếu không tồn tại
3. **ENUM Violation** - Giá trị không hợp lệ
4. **Invalid Date Format** - Sử dụng `STR_TO_DATE()` để convert

---

## 📚 Tiếp theo: Chapter 3 - Query Primer

Chương tiếp theo sẽ đi sâu vào câu lệnh SELECT với các clauses:
- `SELECT` - Chọn columns
- `FROM` - Chọn tables
- `WHERE` - Lọc rows
- `GROUP BY` / `HAVING` - Nhóm dữ liệu
- `ORDER BY` - Sắp xếp kết quả
