# Learning SQL - Tóm tắt Chapter 7

> **Sách:** Learning SQL, 3rd Edition - Alan Beaulieu (O'Reilly)
> **Ngày học:** 2026-01-21

---

## 📖 Chapter 7: Data Generation, Manipulation, and Conversion

Chương này đi sâu vào việc xử lý ba kiểu dữ liệu chính: **String** (chuỗi), **Numeric** (số), và **Temporal** (thời gian). SQL cung cấp nhiều built-in functions để tạo, chuyển đổi và thao tác với các loại dữ liệu này.

---

## 1. Working with String Data (Dữ liệu Chuỗi)

### 1.1. Các kiểu dữ liệu String phổ biến
-   **CHAR:** Độ dài cố định (fixed-length), tự động thêm khoảng trắng (blank-padded).
-   **VARCHAR:** Độ dài thay đổi (variable-length).
-   **TEXT / CLOB:** Dùng cho văn bản lớn (documents).

### 1.2. String Generation (Tạo chuỗi)
-   Dùng dấu nháy đơn `'` để bao quanh chuỗi.
-   **Xử lý dấu nháy đơn trong chuỗi:** Dùng 2 dấu nháy đơn liên tiếp `''` hoặc dấu gạch chéo `\'` (MySQL/Oracle).
-   **Kí tự đặc biệt:** Dùng hàm `CHAR()` (MySQL/SQL Server) hoặc `CHR()` (Oracle) để tạo kí tự từ mã ASCII.
    -   Ví dụ: `CHAR(97)` trả về 'a'.

### 1.3. String Manipulation (Thao tác chuỗi)
-   **LENGTH():** Trả về độ dài chuỗi (SQL Server dùng `LEN()`).
-   **POSITION() / LOCATE() / INSTR():** Tìm vị trí của chuỗi con. (Lưu ý: MySQL dùng `LOCATE`, Oracle dùng `INSTR`, SQL Server dùng `CHARINDEX`).
-   **CONCAT():** Nối chuỗi. (Oracle dùng `||`, SQL Server dùng `+`).
-   **INSERT() / REPLACE():** Chèn hoặc thay thế chuỗi con.
-   **SUBSTRING():** Trích xuất chuỗi con (Oracle dùng `SUBSTR`).

---

## 2. Working with Numeric Data (Dữ liệu Số)

### 2.1. Arithmetic Functions (Hàm toán học)
-   Các toán tử cơ bản: `+`, `-`, `*`, `/`.
-   **MOD(a, b):** Chia lấy dư (SQL Server dùng `%`).
-   **POW(a, b) / POWER(a, b):** Lũy thừa.

### 2.2. Controlling Number Precision (Kiểm soát độ chính xác)
-   **CEIL() / CEILING():** Làm tròn lên số nguyên gần nhất.
-   **FLOOR():** Làm tròn xuống số nguyên gần nhất.
-   **ROUND(number, decimals):** Làm tròn đến số chữ số thập phân chỉ định (quy tắc 0.5 làm tròn lên).
-   **TRUNCATE() / TRUNC(number, decimals):** Cắt bỏ phần thập phân mà không làm tròn.

### 2.3. Handling Signed Data (Số có dấu)
-   **SIGN():** Trả về -1 (số âm), 0 (số 0), hoặc 1 (số dương).
-   **ABS():** Giá trị tuyệt đối.

---

## 3. Working with Temporal Data (Dữ liệu Thời gian)

### 3.1. Time Zones (Múi giờ)
-   Database thường dùng múi giờ của server hệ thống.
-   Quốc tế dùng **UTC** (Coordinated Universal Time).
-   MySQL có global time zone và session time zone.

### 3.2. Generating Temporal Data (Tạo dữ liệu thời gian)
-   **Format mặc định:**
    -   Date: `YYYY-MM-DD`
    -   Datetime/Timestamp: `YYYY-MM-DD HH:MI:SS`
-   **CAST(string AS type):** Chuyển đổi chuỗi sang kiểu date/time (theo format chuẩn).
-   **STR_TO_DATE(string, format):** (MySQL) Chuyển chuỗi bất kỳ sang date theo định dạng format string (ví dụ `%d`, `%M`, `%Y`). Oracle dùng `TO_DATE`.
-   **Hàm lấy giờ hiện tại:** `CURRENT_DATE()`, `CURRENT_TIME()`, `CURRENT_TIMESTAMP()`.

### 3.3. Manipulating Temporal Data (Thao tác thời gian)
-   **DATE_ADD(date, INTERVAL value unit):** Cộng thêm thời gian.
    -   Ví dụ: `DATE_ADD(CURRENT_DATE(), INTERVAL 5 DAY)`.
-   **LAST_DAY(date):** Trả về ngày cuối cùng của tháng.
-   **EXTRACT(unit FROM date):** Lấy thành phần (năm, tháng, ngày...) từ date. (SQL Server dùng `DATEPART`).
-   **DATEDIFF(date1, date2):** Tính khoảng cách (số ngày) giữa 2 mốc thời gian.

---

## 4. Conversion Functions (Hàm chuyển đổi)

-   **CAST(expression AS type):** Hàm chuẩn ANSI SQL để chuyển đổi giữa các kiểu dữ liệu (String sang Number, String sang Date...).
-   Nếu convert thất bại (ví dụ string chứa kí tự lạ), conversion có thể bị dừng hoặc trả về lỗi tùy DB.

---

## ✅ Key Takeaways

1.  SQL cung cấp bộ hàm phong phú để xử lý String, Number, và Date/Time, nhưng tên hàm có thể khác nhau giữa MySQL, Oracle, và SQL Server.
2.  **String:** `CONCAT`, `SUBSTRING`, `LENGTH`, `REPLACE` là những hàm hay dùng nhất.
3.  **Numeric:** Cần phân biệt `ROUND` (làm tròn) và `TRUNCATE` (cắt bỏ).
4.  **Temporal:** Làm việc với ngày tháng rất phức tạp do format và time zone. Nên dùng `CAST` hoặc các hàm parse date (`STR_TO_DATE`) thay vì hardcode chuỗi.
5.  **Conversion:** `CAST` là hàm chuẩn nên ưu tiên sử dụng để code dễ port qua các DB khác.

---

## 📝 Exercises (Bài tập)

### Exercise 7-1
Lấy kí tự thứ 17 đến 25 của chuỗi: 'Please find the substring in this string'.

```sql
SELECT SUBSTRING('Please find the substring in this string', 17, 9);
-- Lưu ý: SQL index bắt đầu từ 1. Độ dài = 25 - 17 + 1 = 9
```

### Exercise 7-2
Lấy giá trị tuyệt đối (ABS), dấu (SIGN) của số -25.76823. Làm tròn (ROUND) đến 2 chữ số thập phân.

```sql
SELECT ABS(-25.76823), SIGN(-25.76823), ROUND(-25.76823, 2);
-- Kết quả: 25.76823, -1, -25.77
```

### Exercise 7-3
Lấy phần tháng (month) của ngày hiện tại.

```sql
SELECT EXTRACT(MONTH FROM CURRENT_DATE());
-- Hoặc: SELECT MONTH(CURRENT_DATE()); (MySQL specific)
```
