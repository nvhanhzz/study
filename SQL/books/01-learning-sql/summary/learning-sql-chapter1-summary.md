# Learning SQL - Tóm tắt Preface & Chapter 1

> **Sách:** Learning SQL, 3rd Edition - Alan Beaulieu (O'Reilly)  
> **Ngày học:** 2026-01-10

---

## 📖 Preface: Why Learn SQL?

### Ý chính
- **SQL có từ những năm 1970** - một trong những ngôn ngữ lâu đời nhất còn được sử dụng
- SQL dùng để **tạo, thao tác, truy xuất dữ liệu** từ CSDL quan hệ
- Học SQL giúp hiểu **cấu trúc dữ liệu** của tổ chức

### Hai loại SQL statements chính

| Loại                      | Mục đích         | Ví dụ                                  |
| ------------------------- | ---------------- | -------------------------------------- |
| **SQL Schema Statements** | Tạo đối tượng DB | `CREATE TABLE`, `ALTER TABLE`          |
| **SQL Data Statements**   | Thao tác dữ liệu | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |

### Sách này tập trung vào
- **SQL Data Statements** (lập trình)
- Chạy trên **MySQL 8.0**, nhưng áp dụng được cho Oracle, SQL Server, PostgreSQL

---

## 📖 Chapter 1: A Little Background

### 1. Các hệ thống CSDL phi quan hệ (lịch sử)

| Loại                | Đặc điểm                         | Hạn chế                           |
| ------------------- | -------------------------------- | --------------------------------- |
| **Hierarchical DB** | Dữ liệu dạng cây (tree)          | Chỉ có 1 cha, khó query linh hoạt |
| **Network DB**      | Dữ liệu dạng mạng (multi-parent) | Phức tạp, khó bảo trì             |

### 2. Mô hình quan hệ (Relational Model)

- **Dr. E. F. Codd (IBM)** đề xuất năm **1970**
- Dữ liệu lưu trong **bảng (tables)**
- Dùng **redundant data** (foreign keys) để liên kết thay vì con trỏ
- **Normalization**: Mỗi thông tin chỉ lưu ở **một nơi duy nhất**

### 3. Thuật ngữ quan trọng

| Thuật ngữ       | Định nghĩa                                 |
| --------------- | ------------------------------------------ |
| **Entity**      | Đối tượng cần lưu (customers, products...) |
| **Column**      | Một cột dữ liệu trong bảng                 |
| **Row**         | Một bản ghi (= record)                     |
| **Table**       | Tập hợp các rows                           |
| **Result Set**  | Bảng tạm - kết quả của query               |
| **Primary Key** | Khóa chính - xác định duy nhất mỗi row     |
| **Foreign Key** | Khóa ngoại - liên kết đến bảng khác        |

### 4. SQL là gì?

```
DSL/Alpha → SQUARE → SEQUEL → SQL
```

- Chuẩn ANSI SQL: 1986, 1989, 1992, 1999, 2003, 2006, 2008, 2011, 2016
- Kết quả của SQL query là **table (result set)**

### 5. SQL Statement Classes

| Loại                       | Mục đích            | Ví dụ                                  |
| -------------------------- | ------------------- | -------------------------------------- |
| **Schema Statements**      | Định nghĩa cấu trúc | `CREATE TABLE`                         |
| **Data Statements**        | Thao tác dữ liệu    | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **Transaction Statements** | Quản lý giao dịch   | `BEGIN`, `COMMIT`, `ROLLBACK`          |

### 6. SQL: Ngôn ngữ phi thủ tục (Nonprocedural)

| Loại ngôn ngữ           | Đặc điểm                                                  |
| ----------------------- | --------------------------------------------------------- |
| **Procedural**          | Định nghĩa kết quả + cách thực hiện                       |
| **Nonprocedural (SQL)** | Chỉ định nghĩa kết quả, **Optimizer** quyết định cách làm |

> 💡 Bạn nói cho DB **CÁI GÌ** bạn muốn, không phải **LÀM SAO** để lấy nó.

### 7. Tích hợp SQL với ngôn ngữ lập trình

| Ngôn ngữ   | Toolkit      |
| ---------- | ------------ |
| **Java**   | JDBC         |
| **C#**     | ADO.NET      |
| **Python** | Python DB    |
| **Go**     | database/sql |
| **Ruby**   | Ruby DBI     |

### 8. Cấu trúc cơ bản của Query

```sql
SELECT /* những gì cần lấy */
FROM   /* từ bảng nào */
WHERE  /* điều kiện lọc */
```

### 9. Ví dụ các thao tác CRUD

```sql
-- CREATE (Insert)
INSERT INTO product (product_cd, name)
VALUES ('CD', 'Certificate of Deposit');

-- READ (Select)
SELECT cust_id, fname
FROM individual
WHERE lname = 'Smith';

-- UPDATE
UPDATE product
SET name = 'Certificate of Deposit'
WHERE product_cd = 'CD';

-- DELETE
DELETE FROM product WHERE product_cd = 'CD';
```

### 10. MySQL vs Các CSDL khác

| CSDL thương mại | CSDL mã nguồn mở      |
| --------------- | --------------------- |
| Oracle Database | **MySQL** ← Sách dùng |
| SQL Server      | PostgreSQL            |
| DB2             |                       |

### 11. SQL Unplugged - Xu hướng mới

- Công nghệ mới: **Hadoop, Spark, NoSQL, NewSQL**
- **Apache Drill**: Query dữ liệu từ nhiều nguồn (Oracle, Hadoop, JSON, CSV...)
- SQL vẫn là **ngôn ngữ chung** để truy vấn dữ liệu

---

## ✅ Key Takeaways

1. **SQL ra đời từ 1970** - Một trong những ngôn ngữ lập trình lâu đời nhất
2. **Relational Model** - Dữ liệu lưu trong bảng, liên kết qua Foreign Key
3. **SQL là Nonprocedural** - Optimizer quyết định cách thực thi
4. **Cấu trúc cơ bản**: `SELECT ... FROM ... WHERE ...`
5. **4 thao tác chính**: SELECT, INSERT, UPDATE, DELETE
6. **SQL là portable** - Chạy được trên nhiều CSDL với ít sửa đổi

---

## 📚 Tiếp theo: Chapter 2 - Creating and Populating a Database
