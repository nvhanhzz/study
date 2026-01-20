# Learning SQL - Tóm tắt Chapter 4

> **Sách:** Learning SQL, 3rd Edition - Alan Beaulieu (O'Reilly)
> **Ngày học:** 2026-01-21

---

## 📖 Chapter 4: Filtering (Lọc dữ liệu)

Chương này tập trung vào các kỹ thuật lọc dữ liệu trong mệnh đề `WHERE` (và `HAVING`). Việc lọc dữ liệu giúp thu hẹp phạm vi thao tác (SELECT, UPDATE, DELETE), cải thiện hiệu năng và đảm bảo tính chính xác của kết quả.

---

## 1. Condition Evaluation (Đánh giá điều kiện)

Một mệnh đề `WHERE` có thể chứa một hoặc nhiều điều kiện, được kết nối bởi các toán tử logic `AND`, `OR` và `NOT`.

### 1.1. Logic Operators

| Toán tử     | Mô tả                            | Ví dụ                            |
| :------------ | :--------------------------------- | :--------------------------------- |
| **AND** | Tất cả điều kiện phải đúng | `age > 18 AND status = 'ACTIVE'` |
| **OR**  | Chỉ cần một điều kiện đúng | `city = 'Hanoi' OR city = 'HCM'` |
| **NOT** | Phủ định điều kiện           | `NOT (status = 'BANNED')`        |

### 1.2. Using Parentheses (Dấu ngoặc đơn)

Khi kết hợp `AND` và `OR`, thứ tự ưu tiên có thể gây nhầm lẫn (thông thường `AND` ưu tiên hơn `OR`).

> 💡 **Best Practice:** Luôn dùng dấu ngoặc `()` để nhóm các điều kiện rõ ràng, ngay cả khi không bắt buộc, để code dễ đọc và tránh lỗi logic.

```sql
-- Dễ gây nhầm lẫn
WHERE first_name = 'John' OR last_name = 'Doe' AND age > 20

-- Rõ ràng hơn
WHERE (first_name = 'John' OR last_name = 'Doe') AND age > 20
```

---

## 2. Condition Types (Các loại điều kiện)

### 2.1. Equality Conditions (So sánh bằng/không bằng)

Dùng để so sánh chính xác giá trị.

- Bằng: `=`
- Không bằng: `!=` hoặc `<>`

```sql
SELECT email FROM customer WHERE create_date = '2005-06-14';
SELECT email FROM customer WHERE create_date <> '2005-06-14';
```

### 2.2. Range Conditions (So sánh khoảng)

Dùng cho dữ liệu số hoặc ngày tháng.

- Các toán tử: `<`, `>`, `<=`, `>=`
- **BETWEEN:** Dùng để kiểm tra trong một khoảng (bao gồm cả 2 đầu mút - inclusive).

```sql
-- Dùng toán tử so sánh
WHERE rental_date >= '2005-06-14' AND rental_date <= '2005-06-16'

-- Dùng BETWEEN (Gọn hơn)
WHERE rental_date BETWEEN '2005-06-14' AND '2005-06-16'
```

> ⚠️ **Lưu ý:**
>
> 1. `BETWEEN` bao gồm cả giá trị đầu và cuối.
> 2. Luôn đặt cận dưới trước `AND` cận trên (`BETWEEN lower AND upper`). Nếu viết ngược sẽ không ra kết quả.

### 2.3. Membership Conditions (So sánh tập hợp)

Dùng để kiểm tra xem giá trị có nằm trong một tập hữu hạn hay không.

- **IN:** Nằm trong tập hợp.
- **NOT IN:** Không nằm trong tập hợp.

```sql
-- Thay vì dùng nhiều OR
WHERE rating = 'G' OR rating = 'PG' OR rating = 'PG-13'

-- Dùng IN (Gọn và tối ưu hơn)
WHERE rating IN ('G', 'PG', 'PG-13')
```

Có thể dùng Subquery trong `IN`:

```sql
WHERE rating IN (SELECT rating FROM film WHERE title LIKE '%PET%')
```

### 2.4. Matching Conditions (So sánh mẫu - Pattern Matching)

Dùng cho dữ liệu chuỗi (String) để tìm kiếm gần đúng.

#### Wildcards (Ký tự đại diện) với `LIKE`

| Ký tự                  | Ý nghĩa                                                         | Ví dụ                                                             |
| :----------------------- | :---------------------------------------------------------------- | :------------------------------------------------------------------ |
| **_** (Underscore) | Đại diện cho**đúng 1** ký tự bất kỳ                | `_A_T%S` (Ký tự thứ 2 là A, thứ 4 là T, kết thúc bằng S) |
| **%** (Percent)    | Đại diện cho**chuỗi ký tự bất kỳ** (0 hoặc nhiều) | `F%` (Bắt đầu bằng F), `%t` (Kết thúc bằng t)            |

```sql
-- Tìm khách hàng có họ bắt đầu bằng Q hoặc Y
SELECT last_name FROM customer 
WHERE last_name LIKE 'Q%' OR last_name LIKE 'Y%'
```

#### Regular Expressions (`REGEXP`)

Nếu `LIKE` không đủ mạnh, có thể dùng Regex (MySQL hỗ trợ `REGEXP`, Oracle dùng `REGEXP_LIKE`).

```sql
-- Tìm họ bắt đầu bằng Q hoặc Y (dùng Regex)
WHERE last_name REGEXP '^[QY]'
```

---

## 3. NULL: That Four-Letter Word

`NULL` đại diện cho việc **thiếu giá trị**, **chưa biết**, hoặc **không áp dụng**.

### 3.1. Đặc điểm quan trọng

- `NULL` không phải là số 0, cũng không phải chuỗi rỗng `''`.
- `NULL` không bằng chính nó (`NULL = NULL` là False/Unknown).
- Hai giá trị `NULL` không bao giờ bằng nhau.

### 3.2. Kiểm tra NULL

> ⚠️ **SAI:** `WHERE return_date = NULL` (Luôn trả về False/Empty set).

> ✅ **ĐÚNG:** Phải dùng toán tử `IS NULL` hoặc `IS NOT NULL`.

```sql
-- Tìm những lần thuê chưa trả đĩa
SELECT rental_id FROM rental WHERE return_date IS NULL;
```

### 3.3. Cạm bẫy với Non-Matching

Khi lọc dữ liệu với điều kiện `NOT` hoặc so sánh khác `!=`, các dòng có giá trị `NULL` sẽ bị **loại bỏ** (không được tính là đúng, cũng không tính là sai, mà là `UNKNOWN`).
Cần xử lý `NULL` riêng hoặc dùng `OR IS NULL` nếu muốn bao gồm chúng.

---

## ✅ Key Takeaways

1. **Parentheses:** Luôn dùng `()` khi kết hợp `AND/OR` để đảm bảo logic đúng.
2. **NULL:** Tuyệt đối không dùng `=` hoặc `!=` với `NULL`. Luôn dùng `IS NULL` / `IS NOT NULL`.
3. **Wildcards:** Dùng `LIKE` với `%` (nhiều ký tự) và `_` (một ký tự) để tìm kiếm chuỗi linh hoạt.
4. **BETWEEN:** Cẩn thận với các khoảng ngày giờ (đặc biệt khi cột chứa cả giờ phút giây), `BETWEEN` lấy cả 2 đầu mút.

---

## 📚 Tiếp theo: Chapter 5 - Querying Multiple Tables

Chương tiếp theo sẽ nói về **JOINs** - trái tim của SQL:

- Inner Joins.
- Joining 3+ tables.
- Self-joins.
