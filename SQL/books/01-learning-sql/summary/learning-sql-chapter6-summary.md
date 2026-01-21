# Learning SQL - Tóm tắt Chapter 6

> **Sách:** Learning SQL, 3rd Edition - Alan Beaulieu (O'Reilly)
> **Ngày học:** 2026-01-21

---

## 📖 Chapter 6: Working with Sets (Làm việc với Tập hợp)

Chương này tập trung vào **Set Theory** (Lý thuyết tập hợp) và cách áp dụng nó trong SQL để kết hợp dữ liệu từ nhiều truy vấn khác nhau. Thay vì thao tác trên từng dòng, SQL thực chất hoạt động trên các tập hợp (sets).

---

## 1. Set Theory Primer (Cơ bản về Lý thuyết tập hợp)

Có ba thao tác cơ bản trên tập hợp:

1.  **Union (Hợp):** Kết hợp hai tập hợp (A + B).
2.  **Intersection (Giao):** Lấy phần chung giữa hai tập hợp (phần overlap).
3.  **Except (Trừ/Hiệu):** Lấy phần thuộc tập A nhưng không thuộc tập B (A - B).

---

## 2. Set Theory in Practice (Thực hành trong SQL)

Để thực hiện các phép toán tập hợp trên hai tập dữ liệu (kết quả của hai câu `SELECT`), ta cần tuân thủ các quy tắc sau:

1.  **Số lượng cột:** Hai tập dữ liệu phải có cùng số lượng cột.
2.  **Kiểu dữ liệu:** Các cột tương ứng phải có cùng kiểu dữ liệu (hoặc database có thể tự chuyển đổi).

Ví dụ: Bạn không thể UNION một tập có 3 cột với một tập có 4 cột, hoặc UNION cột số (numeric) với cột chuỗi (string) nếu database không hỗ trợ convert.

---

## 3. Set Operators (Các toán tử tập hợp)

### 3.1. The UNION Operator

-   Dùng để **gộp** kết quả của nhiều truy vấn.
-   **UNION:** Gộp và **loại bỏ các dòng trùng lặp** (duplicate rows), đồng thời sắp xếp lại kết quả.
-   **UNION ALL:** Gộp tất cả các dòng, **giữ nguyên dòng trùng lặp**.
    -   *Hiệu năng:* `UNION ALL` nhanh hơn vì server không cần kiểm tra trùng lặp hay sắp xếp lại.

**Ví dụ:**

```sql
SELECT c.first_name, c.last_name FROM customer c
UNION ALL
SELECT a.first_name, a.last_name FROM actor a;
```

### 3.2. The INTERSECT Operator

-   Trả về các dòng **xuất hiện ở cả hai** tập kết quả (phần giao).
-   **Lưu ý:** MySQL 8.0 (theo sách) chưa hỗ trợ toán tử `INTERSECT`. Các DB khác như Oracle, SQL Server đều hỗ trợ.
-   DB2 còn có `INTERSECT ALL` (không loại bỏ trùng lặp).

### 3.3. The EXCEPT Operator

-   Trả về các dòng **có trong truy vấn đầu tiên** nhưng **không có trong truy vấn thứ hai** (phép trừ).
-   Trong Oracle, toán tử này tên là `MINUS`.
-   **Lưu ý:** MySQL 8.0 chưa hỗ trợ `EXCEPT`.

---

## 4. Set Operation Rules (Quy tắc vận hành)

### 4.1. Sorting (Sắp xếp)

Nếu muốn sắp xếp kết quả của compound query (truy vấn phức hợp), bạn đặt mệnh đề `ORDER BY` **ở cuối cùng**.
-   Tên cột trong `ORDER BY` phải lấy theo tên cột (hoặc alias) của **truy vấn đầu tiên**.

```sql
SELECT a.first_name fname, a.last_name lname FROM actor a
UNION ALL
SELECT c.first_name, c.last_name FROM customer c
ORDER BY lname, fname; -- Dùng alias của query đầu tiên
```

### 4.2. Set Operation Precedence (Thứ tự ưu tiên)

Nếu compound query có nhiều toán tử khác nhau (ví dụ vừa có `UNION` vừa có `INTERSECT`):
1.  Theo chuẩn ANSI SQL, `INTERSECT` có độ ưu tiên cao hơn các toán tử khác.
2.  Tuy nhiên, thứ tự thực thi có thể ảnh hưởng lớn đến kết quả.
3.  **Best Practice:** Luôn dùng **dấu ngoặc đơn `()`** để nhóm các truy vấn, giúp chỉ định rõ thứ tự thực thi mong muốn và code dễ đọc hơn.

*(Lưu ý: MySQL cũ có thể không hỗ trợ dấu ngoặc trong compound query, nhưng các bản mới hoặc DB khác thì có).*

---

## ✅ Key Takeaways

1.  **UNION ALL** thường được dùng nhất vì nhanh (không check trùng). Dùng **UNION** nếu muốn loại bỏ trùng lặp.
2.  Các toán tử **INTERSECT** và **EXCEPT** rất hữu ích nhưng cần kiểm tra xem hệ quản trị CSDL của bạn có hỗ trợ hay không (MySQL hạn chế phần này).
3.  Khi gộp, phải đảm bảo sự tương thích về **số lượng cột** và **kiểu dữ liệu**.
4.  Dùng `ORDER BY` ở cuối để sắp xếp toàn bộ kết quả gộp.

---

## 📝 Exercises (Bài tập)

### Exercise 6-1
Cho tập A = {L, M, N, O, P} và tập B = {P, Q, R, S, T}. Kết quả là gì?
-   A union B: {L, M, N, O, P, Q, R, S, T}
-   A union all B: {L, M, N, O, P, P, Q, R, S, T} (Có 2 chữ P)
-   A intersect B: {P}
-   A except B: {L, M, N, O}

### Exercise 6-2
Viết query tìm tên (first_name, last_name) của tất cả Actors và Customers có `last_name` bắt đầu bằng chữ 'L'.

```sql
SELECT first_name, last_name FROM actor WHERE last_name LIKE 'L%'
UNION
SELECT first_name, last_name FROM customer WHERE last_name LIKE 'L%';
```

### Exercise 6-3
Sắp xếp kết quả bài 6-2 theo `last_name`.

```sql
SELECT first_name, last_name FROM actor WHERE last_name LIKE 'L%'
UNION
SELECT first_name, last_name FROM customer WHERE last_name LIKE 'L%'
ORDER BY last_name;
```
