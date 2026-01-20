# Learning SQL - Tóm tắt Chapter 3

> **Sách:** Learning SQL, 3rd Edition - Alan Beaulieu (O'Reilly)
> **Ngày học:** 2026-01-20

---

## 📖 Chapter 3: Query Primer

Chương này giới thiệu về câu lệnh `SELECT` và các thành phần (clauses) của nó. Đây là nền tảng cho việc truy xuất, join, lọc, nhóm và sắp xếp dữ liệu.

---

## 1. Query Mechanics (Cơ chế truy vấn)

Khi một query được gửi đến server (ví dụ: MySQL), quy trình xử lý diễn ra như sau:

1.  **Permission Check:** Kiểm tra quyền thực thi lệnh và quyền truy cập dữ liệu của user.
2.  **Syntax Check:** Kiểm tra cú pháp SQL xem có hợp lệ không.
3.  **Query Optimizer:** Nếu 2 bước trên OK, Optimizer sẽ xác định cách thực thi hiệu quả nhất (dùng index nào, thứ tự join bảng...).
4.  **Execution:** Server thực thi query và trả về **Result Set** (một bảng chứa các rows và columns).

---

## 2. Các Query Clauses cơ bản

Một câu lệnh `SELECT` thường bao gồm các thành phần sau:

| Clause       | Mục đích                                                         |
| :----------- | :--------------------------------------------------------------- |
| **SELECT**   | Xác định các cột (columns) sẽ xuất hiện trong result set         |
| **FROM**     | Xác định các bảng (tables) để lấy dữ liệu và cách liên kết chúng |
| **WHERE**    | Lọc bỏ các dòng (rows) không mong muốn                           |
| **GROUP BY** | Nhóm các dòng lại theo giá trị chung                             |
| **HAVING**   | Lọc bỏ các nhóm không mong muốn (filter sau khi group)           |
| **ORDER BY** | Sắp xếp dữ liệu trong result set                                 |

> **Lưu ý:** Chỉ có `SELECT` clause là bắt buộc trong MySQL (ví dụ: `SELECT now();`).

### 2.1. Thứ tự thực thi (Logical Processing Order)

Mặc dù chúng ta viết SQL bắt đầu bằng `SELECT`, nhưng database server lại xử lý theo thứ tự khác để tối ưu hóa va xác định dữ liệu:

1.  **FROM / JOIN:**
    *   **Cartesian Product:** Tạo tích Đề-các của các bảng (về mặt logic).
    *   **ON Filter:** Lọc dòng dựa trên điều kiện `ON`.
    *   **Outer Join:** Thêm lại các dòng bị lọc nếu là `LEFT/RIGHT JOIN`.
    > ⚠️ **Lưu ý:** Đây là thứ tự **Logic** (cách hiểu của con người). Trong thực tế (**Physical**), DB Optimizer cực kỳ thông minh, nó sẽ **KHÔNG** chạy Cartesian Product thật (vì rất chậm) mà sẽ dùng các thuật toán như *Hash Join* hay *Nested Loop* để ghép đúng các dòng cần thiết ngay từ đầu.
2.  **WHERE:** Lọc dữ liệu thô (row-level) từ kết quả của bước trên.
3.  **GROUP BY:** Gom nhóm dữ liệu.
4.  **HAVING:** Lọc các nhóm (group-level).
5.  **SELECT:** Chọn các cột và biểu thức tính toán.
6.  **DISTINCT:** Loại bỏ trùng lặp.
7.  **ORDER BY:** Sắp xếp kết quả.
8.  **LIMIT / OFFSET:** Giới hạn số lượng dòng trả về.

> 💡 Hiểu thứ tự này giúp bạn hiểu tại sao không thể dùng *alias* của `SELECT` trong mệnh đề `WHERE` (vì `WHERE` chạy trước `SELECT`), nhưng lại dùng được trong `ORDER BY` (vì `ORDER BY` chạy sau `SELECT`).


---

## 3. The SELECT Clause

Clause này được server đánh giá **gần cuối cùng** (mặc dù viết đầu tiên), vì server cần biết dữ liệu từ đâu (FROM) trước khi quyết định lấy cột nào.

### 3.1. Chọn cột
- `SELECT *`: Lấy tất cả cột.
- `SELECT col1, col2`: Lấy cột cụ thể.
- Có thể bao gồm: Literals (số, chuỗi), Expressions (biểu thức), Built-in functions.

### 3.2. Column Aliases (Đặt tên giả cho cột)
Dùng để đặt tên dễ đọc hơn cho cột, đặc biệt là với các expression.

```sql
SELECT language_id,
       'COMMON' AS language_usage, 
       language_id * 3.14 AS lang_pi_value,
       upper(name) AS language_name
FROM language;
```

> 💡 Keyword `AS` là tùy chọn, nhưng nên dùng để code rõ ràng hơn.

### 3.3. Removing Duplicates (Loại bỏ trùng lặp)
Sử dụng `DISTINCT` để lấy danh sách duy nhất. `ALL` là mặc định (lấy hết).

```sql
-- Lấy tất cả actor_id (có thể trùng lặp)
SELECT actor_id FROM film_actor;

-- Chỉ lấy danh sách actor_id duy nhất tham gia phim
SELECT DISTINCT actor_id FROM film_actor;
```

> ⚠️ Sử dụng `DISTINCT` cần sort dữ liệu nên có thể tốn tài nguyên.

### 3.4. Thực tế: Tại sao JOIN hay đi kèm DISTINCT? (Ví dụ JPA/Hibernate)

Trong các ứng dụng sử dụng ORM (như Hibernate/JPA), bạn sẽ thường thấy `JOIN FETCH` đi kèm với `DISTINCT`.

**Lý do:**
Khi bạn `JOIN` một bảng cha (User) với bảng con (Roles) có quan hệ 1-N (một User có nhiều Roles).
- Nếu User A có 2 Roles (Admin, Member).
- Kết quả Query sẽ trả về 2 dòng:
  1. User A - Admin
  2. User A - Member
- Hibernate sẽ ánh xạ (map) 2 dòng này thành **2 đối tượng User A** trong List kết quả trả về.

=> Do đó, phải dùng `SELECT DISTINCT u` để báo cho Hibernate biết: "Chỉ lấy danh sách các User duy nhất, đừng lặp lại User A hai lần".

```java
// Ví dụ JPQL
@Query("SELECT DISTINCT u FROM User u JOIN FETCH u.roles") 
List<User> findAllusers();
```

---

## 4. The FROM Clause

Xác định nguồn dữ liệu và cách liên kết.

### 4.1. Các loại Tables
- **Permanent Tables:** Bảng lưu trữ vĩnh viễn trong DB (`CREATE TABLE`).
- **Derived Tables:** Bảng tạo ra từ subquery (nằm trong mệnh đề FROM).
- **Temporary Tables:** Bảng tạm thời trong bộ nhớ, biến mất khi hết session.
- **Virtual Tables (Views):** Query được lưu trong data dictionary, không chứa dữ liệu thật mà query động khi được gọi.

### 4.2. Table Links (Joins)
Chuẩn ANSI SQL quy định điều kiện join nên nằm trong mệnh đề `ON` thay vì `WHERE`.

```sql
SELECT c.first_name, c.last_name, r.rental_date
FROM customer c 
INNER JOIN rental r 
ON c.customer_id = r.customer_id
WHERE date(r.rental_date) = '2005-06-14';
```

**Tại sao lại nói vậy?**
Trước năm 1992 (chuẩn SQL-89), người ta thường viết điều kiện join ngay trong `WHERE`:
```sql
-- Cú pháp cũ (Legacy / SQL-89) - KHÔNG NÊN DÙNG
SELECT c.first_name, r.rental_date
FROM customer c, rental r
WHERE c.customer_id = r.customer_id -- Điều kiện nối bảng trộn lẫn với điều kiện lọc
AND date(r.rental_date) = '2005-06-14';
```
Cách viết mới (SQL-92) dùng `ON` giúp tách biệt rõ ràng:
1.  **ON:** Chỉ chứa logic "Nối bảng thế nào?" (Logic cấu trúc).
2.  **WHERE:** Chỉ chứa logic "Lọc dữ liệu nào?" (Logic nghiệp vụ).
-> Giúp query dễ đọc, dễ debug và tránh nhầm lẫn (đặc biệt khi dùng OUTER JOIN).

#### ⚠️ Quy tắc vàng cho LEFT JOIN (LEFT OUTER JOIN)
Khi dùng `LEFT JOIN`, vị trí đặt điều kiện cực kỳ quan trọng:

1.  **Lọc trên bảng LEFT (Bảng chính):**
    *   Đặt ở `WHERE` là chuẩn nhất.
    *   Ví dụ: "Lấy tất cả khách hàng *ở Việt Nam* và đơn hàng của họ (nếu có)".
    *   ```sql
        SELECT c.name, o.id 
        FROM Customer c LEFT JOIN Orders o ON c.id = o.c_id
        WHERE c.country = 'Vietnam'; -- ✅ OK: Lọc bảng trái, vẫn giữ tính chất Left Join.
        ```
    *   **Hỏi:** Chuyển `c.country = 'Vietnam'` lên mệnh đề `ON` được không?
    *   **Đáp:** **KHÔNG!** Nếu để ở `ON`, nó sẽ **không lọc bỏ** khách hàng nước khác. Nó sẽ vẫn giữ lại khách hàng Mỹ/Nhật..., nhưng cột `Order` của họ sẽ bị NULL (dù họ có đơn hàng).
    *   *Lý do:* `LEFT JOIN` luôn giữ lại tất cả dòng bảng trái dù điều kiện `ON` sai.

2.  **Lọc trên bảng RIGHT (Bảng phụ):**
    *   **Đặt ở `ON`:** Giữ nguyên các dòng bên Left, chỉ NULL bên Right nếu không khớp. (Đúng tính chất Left Join).
    *   **Đặt ở `WHERE`:** Biến câu lệnh thành `INNER JOIN` (Mất dữ liệu bên trái nếu bên phải không khớp). **CẨN THẬN!**
    *   ```sql
        -- Trượng hợp 1: Lấy khách hàng, và đơn hàng ngày hôm nay (nếu ko có đơn thì vẫn hiện khách)
        ... LEFT JOIN Orders o ON c.id = o.c_id AND o.date = 'Today' -- ✅ Đúng
        
        -- Trường hợp 2: Lấy khách hàng CÓ đơn hàng ngày hôm nay
        ... LEFT JOIN Orders o ON c.id = o.c_id
        WHERE o.date = 'Today' -- ⚠️ Sai (nó thành Inner Join), khách ko có đơn sẽ bị lọc bỏ.
        ```

### 4.3. Table Aliases
Giúp viết query ngắn gọn và tránh nhầm lẫn khi join nhiều bảng.

```sql
FROM customer AS c
INNER JOIN rental AS r
```

---

## 5. The WHERE Clause

Dùng để lọc dữ liệu (`Filter`). Chỉ những row thỏa mãn điều kiện mới được đưa vào result set.

### 5.1. Operators
- `AND`: Tất cả điều kiện phải đúng.
- `OR`: Chỉ cần một trong các điều kiện đúng.
- `NOT`: Phủ định điều kiện.

### 5.2. Grouping Conditions
Dùng dấu ngoặc đơn `()` để nhóm các điều kiện khi kết hợp `AND` và `OR` để đảm bảo thứ tự ưu tiên đúng.

```sql
-- Lọc phim G (>=7 ngày) HOẶC PG-13 (<4 ngày)
SELECT title, rating, rental_duration
FROM film
WHERE (rating = 'G' AND rental_duration >= 7)
   OR (rating = 'PG-13' AND rental_duration < 4);
```

---

## 6. GROUP BY and HAVING

Giới thiệu sơ lược (chi tiết ở Chapter 8).
- **GROUP BY:** Gom nhóm dữ liệu để tính toán (ví dụ: đếm số phim mỗi khách hàng thuê).
- **HAVING:** Lọc dữ liệu **sau khi đã gom nhóm** (ví dụ: chỉ lấy khách hàng thuê > 40 phim).

```sql
SELECT c.first_name, count(*) 
FROM customer c 
INNER JOIN rental r ON c.customer_id = r.customer_id
GROUP BY c.first_name
HAVING count(*) >= 40;
```

---

## 7. The ORDER BY Clause

Sắp xếp kết quả trả về.

### 7.1. Ascending vs Descending
- `ASC` (Mặc định): Tăng dần.
- `DESC`: Giảm dần.

```sql
ORDER BY amount DESC, date ASC;
```

### 7.2. Sorting via Numeric Placeholders
Có thể sort theo vị trí cột trong câu lệnh SELECT (tuy nhiên nên hạn chế dùng để tránh lỗi khi thay đổi schema/query).

```sql
SELECT first_name, last_name, salary
FROM employee
ORDER BY 3 DESC; -- Sort theo salary giảm dần
```

---

## ✅ Key Takeaways

1.  **Cấu trúc Query:** Nắm vững thứ tự `SELECT` -> `FROM` -> `WHERE` -> `GROUP BY` -> `HAVING` -> `ORDER BY`.
2.  **Filter vs Join:** Phân biệt rõ ràng điều kiện lọc (`WHERE`) và điều kiện liên kết bảng (`ON` trong JOIN).
3.  **Alias:** Sử dụng Alias cho cả Column và Table để code gọn và dễ đọc.
4.  **Distinct:** Dùng cẩn thận vì ảnh hưởng hiệu năng.
5.  **Logic:** Luôn dùng `()` khi kết hợp `AND`/`OR` phức tạp.

---

## 📚 Tiếp theo: Chapter 4 - Filtering

Chương tiếp theo sẽ đi sâu vào `WHERE` clause:
- Các loại điều kiện lồng nhau.
- Sử dụng `NOT`, `BETWEEN`, `IN`...
- Xử lý giá trị `NULL` trong điều kiện lọc.
