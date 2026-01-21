# Learning SQL - Tóm tắt Chapter 5

> **Sách:** Learning SQL, 3rd Edition - Alan Beaulieu (O'Reilly)
> **Ngày học:** 2026-01-21

---

## 📖 Chapter 5: Querying Multiple Tables

Chương này tập trung vào **JOIN** - cơ chế kết nối nhiều bảng lại với nhau để truy vấn dữ liệu từ các bảng đã được chuẩn hóa (normalized). Chương này giới thiệu **Inner Join**, còn các loại join khác sẽ được trình bày ở Chapter 10.

---

## 1. What Is a Join? (Join là gì?)

### 1.1. Tại sao cần Join?

Do quá trình **normalization** (chuẩn hóa), dữ liệu được tách thành nhiều bảng riêng biệt để tránh trùng lặp. Ví dụ:
- Bảng `customer`: Lưu thông tin khách hàng (tên, email, `address_id`).
- Bảng `address`: Lưu địa chỉ chi tiết (đường phố, thành phố, mã bưu điện).

Nếu muốn lấy **tên khách hàng + địa chỉ** trong cùng một báo cáo, ta cần **join** hai bảng này lại.

### 1.2. Foreign Key (Khóa ngoại)

- **Foreign Key** là cột trong bảng này trỏ đến **Primary Key** của bảng khác.
- Ví dụ: `customer.address_id` là foreign key trỏ đến `address.address_id`.
- **Lưu ý:** Không bắt buộc phải có **Foreign Key Constraint** (ràng buộc khóa ngoại) để thực hiện join, nhưng nó giúp đảm bảo tính toàn vẹn dữ liệu.

---

## 2. Cartesian Product (Tích Đề-các)

### 2.1. Khái niệm

Nếu bạn join hai bảng **mà không chỉ định điều kiện nối**, database sẽ tạo ra **Cartesian Product** - tất cả các hoán vị (permutations) giữa hai bảng.

```sql
SELECT c.first_name, c.last_name, a.address
FROM customer c JOIN address a;
```

**Kết quả:**
- Nếu `customer` có 599 dòng, `address` có 603 dòng.
- Kết quả sẽ có: **599 × 603 = 361,197 dòng** (mỗi khách hàng ghép với mọi địa chỉ).

### 2.2. Cross Join

Cartesian Product còn được gọi là **Cross Join** - một loại join hiếm khi được sử dụng (trừ khi có mục đích đặc biệt).

> ⚠️ **Cảnh báo:** Nếu bạn quên điều kiện join, kết quả sẽ bùng nổ (explosion) và rất chậm!

---

## 3. Inner Joins (Nối trong)

### 3.1. Định nghĩa

**Inner Join** chỉ trả về các dòng có **giá trị khớp** ở cả hai bảng. Nếu một dòng ở bảng A không tìm thấy dòng tương ứng ở bảng B (hoặc ngược lại), dòng đó sẽ **bị loại bỏ** khỏi kết quả.

### 3.2. Cú pháp

```sql
SELECT c.first_name, c.last_name, a.address
FROM customer c INNER JOIN address a
ON c.address_id = a.address_id;
```

**Giải thích:**
- `INNER JOIN`: Chỉ định loại join (nên luôn ghi rõ để code dễ đọc).
- `ON c.address_id = a.address_id`: Điều kiện nối - chỉ lấy các dòng có `address_id` khớp nhau.

**Kết quả:** 599 dòng (mỗi khách hàng có đúng 1 địa chỉ tương ứng).

### 3.3. USING Clause (Mệnh đề USING)

Nếu tên cột dùng để join **giống nhau** ở cả hai bảng, bạn có thể dùng `USING` thay cho `ON`:

```sql
SELECT c.first_name, c.last_name, a.address
FROM customer c INNER JOIN address a
USING (address_id);
```

> 💡 **Khuyến nghị:** Nên dùng `ON` để tránh nhầm lẫn và dễ đọc hơn.

---

## 4. The ANSI Join Syntax (Cú pháp Join chuẩn ANSI)

### 4.1. Cú pháp cũ (SQL-89) - KHÔNG NÊN DÙNG

Trước SQL-92, người ta viết join như sau:

```sql
SELECT c.first_name, c.last_name, a.address
FROM customer c, address a
WHERE c.address_id = a.address_id
  AND a.postal_code = 52137;
```

**Vấn đề:**
- Điều kiện join (`c.address_id = a.address_id`) và điều kiện lọc (`a.postal_code = 52137`) **trộn lẫn** trong `WHERE`.
- Khó phân biệt đâu là join, đâu là filter.
- Dễ quên điều kiện join → tạo ra Cartesian Product.

### 4.2. Cú pháp mới (SQL-92) - NÊN DÙNG

```sql
SELECT c.first_name, c.last_name, a.address
FROM customer c INNER JOIN address a
ON c.address_id = a.address_id
WHERE a.postal_code = 52137;
```

**Ưu điểm:**
1. **Tách biệt rõ ràng:**
   - `ON`: Điều kiện nối bảng (cấu trúc).
   - `WHERE`: Điều kiện lọc dữ liệu (nghiệp vụ).
2. **Dễ đọc, dễ bảo trì:** Mỗi cặp bảng có `ON` riêng, giảm nguy cơ quên điều kiện.
3. **Tính di động cao:** Chuẩn SQL-92 được hỗ trợ bởi tất cả các database lớn (MySQL, PostgreSQL, Oracle, SQL Server...).

---

## 5. Joining Three or More Tables (Join 3 bảng trở lên)

### 5.1. Cú pháp

Khi join 3 bảng, bạn cần:
- 3 bảng trong `FROM`.
- 2 lần `INNER JOIN`.
- 2 mệnh đề `ON`.

**Ví dụ:** Lấy tên khách hàng + tên thành phố (cần join 3 bảng: `customer` → `address` → `city`).

```sql
SELECT c.first_name, c.last_name, ct.city
FROM customer c
INNER JOIN address a
  ON c.address_id = a.address_id
INNER JOIN city ct
  ON a.city_id = ct.city_id;
```

**Giải thích:**
1. Join `customer` với `address` qua `address_id`.
2. Join `address` với `city` qua `city_id`.

### 5.2. Thứ tự bảng có quan trọng không?

**Không!** SQL là ngôn ngữ **phi thủ tục** (nonprocedural). Bạn chỉ mô tả **muốn gì**, database server sẽ tự quyết định:
- Bảng nào làm **driving table** (bảng xuất phát).
- Thứ tự join tối ưu dựa trên **statistics** (thống kê).

**Ba cách viết sau đều cho kết quả giống nhau:**

```sql
-- Cách 1: customer → address → city
FROM customer c
INNER JOIN address a ON c.address_id = a.address_id
INNER JOIN city ct ON a.city_id = ct.city_id;

-- Cách 2: city → address → customer
FROM city ct
INNER JOIN address a ON a.city_id = ct.city_id
INNER JOIN customer c ON c.address_id = a.address_id;

-- Cách 3: address → city → customer
FROM address a
INNER JOIN city ct ON a.city_id = ct.city_id
INNER JOIN customer c ON c.address_id = a.address_id;
```

> 💡 **Lưu ý:** Nếu bạn muốn **ép buộc thứ tự join**, có thể dùng:
> - MySQL: `STRAIGHT_JOIN`
> - SQL Server: `FORCE ORDER`
> - Oracle: `ORDERED` hoặc `LEADING` hint

---

## 6. Using Subqueries As Tables (Dùng Subquery làm bảng)

Bạn có thể join một bảng thật với một **subquery** (truy vấn con) trong mệnh đề `FROM`.

**Ví dụ:** Lấy khách hàng ở California.

```sql
SELECT c.first_name, c.last_name, addr.address, addr.city
FROM customer c
INNER JOIN
  (SELECT a.address_id, a.address, ct.city
   FROM address a
   INNER JOIN city ct ON a.city_id = ct.city_id
   WHERE a.district = 'California'
  ) addr
ON c.address_id = addr.address_id;
```

**Giải thích:**
- Subquery `addr` lọc trước các địa chỉ ở California.
- Query ngoài join `customer` với kết quả subquery.

**Ưu điểm:**
- Tăng tính **dễ đọc** (chia nhỏ logic).
- Đôi khi cải thiện **hiệu năng** (tùy database optimizer).

---

## 7. Using the Same Table Twice (Dùng cùng một bảng nhiều lần)

### 7.1. Tình huống

Đôi khi bạn cần join cùng một bảng **nhiều lần** trong cùng một query.

**Ví dụ:** Tìm phim có **cả hai diễn viên** Cate McQueen VÀ Cuba Birch.

### 7.2. Giải pháp: Dùng Table Alias khác nhau

```sql
SELECT f.title
FROM film f
INNER JOIN film_actor fa1 ON f.film_id = fa1.film_id
INNER JOIN actor a1 ON fa1.actor_id = a1.actor_id
INNER JOIN film_actor fa2 ON f.film_id = fa2.film_id
INNER JOIN actor a2 ON fa2.actor_id = a2.actor_id
WHERE (a1.first_name = 'CATE' AND a1.last_name = 'MCQUEEN')
  AND (a2.first_name = 'CUBA' AND a2.last_name = 'BIRCH');
```

**Giải thích:**
- `fa1`, `a1`: Tìm phim có Cate McQueen.
- `fa2`, `a2`: Tìm phim có Cuba Birch.
- Chỉ lấy phim thỏa mãn **cả hai điều kiện** (AND).

**Kết quả:** 2 phim (trong khi mỗi diễn viên riêng lẻ có tổng cộng 52 phim).

---

## 8. Self-Joins (Tự join)

### 8.1. Định nghĩa

**Self-Join** là join một bảng với chính nó. Điều này hữu ích khi bảng có **self-referencing foreign key** (khóa ngoại tự tham chiếu).

### 8.2. Ví dụ

Giả sử bảng `film` có cột `prequel_film_id` trỏ đến phim gốc (ví dụ: "Fiddler Lost II" trỏ đến "Fiddler Lost").

```sql
SELECT f.title, f_prnt.title AS prequel
FROM film f
INNER JOIN film f_prnt
  ON f_prnt.film_id = f.prequel_film_id
WHERE f.prequel_film_id IS NOT NULL;
```

**Kết quả:**

| title           | prequel      |
|-----------------|--------------|
| FIDDLER LOST II | FIDDLER LOST |

**Giải thích:**
- `f`: Bảng phim (phần tiếp theo).
- `f_prnt`: Cũng là bảng phim, nhưng đại diện cho phim gốc (prequel).
- Join qua `prequel_film_id`.

---

## ✅ Key Takeaways

1. **Join là cầu nối:** Kết nối các bảng đã được chuẩn hóa để lấy dữ liệu liên quan.
2. **Cartesian Product:** Luôn chỉ định điều kiện join (`ON`) để tránh bùng nổ dữ liệu.
3. **Inner Join:** Chỉ trả về dòng có giá trị khớp ở cả hai bảng.
4. **ANSI SQL-92:** Dùng `ON` để tách biệt điều kiện join và filter (`WHERE`).
5. **Join nhiều bảng:** Cần N-1 mệnh đề `ON` cho N bảng. Thứ tự bảng không quan trọng (database tự tối ưu).
6. **Subquery trong FROM:** Có thể join với kết quả của subquery.
7. **Table Alias:** Bắt buộc khi dùng cùng một bảng nhiều lần (hoặc self-join).
8. **Self-Join:** Join bảng với chính nó để xử lý quan hệ phân cấp (hierarchical).

---

## 📝 Exercises (Bài tập)

### Exercise 5-1
Điền vào chỗ trống (`<1>`, `<2>`) để query trả về khách hàng ở California:

```sql
SELECT c.first_name, c.last_name, a.address, ct.city
FROM customer c
INNER JOIN address <1>
  ON c.address_id = a.address_id
INNER JOIN city ct
  ON a.city_id = <2>
WHERE a.district = 'California';
```

**Đáp án:**
- `<1>`: `a` (alias cho bảng `address`)
- `<2>`: `ct.city_id`

### Exercise 5-2
Viết query trả về tên tất cả các phim có diễn viên tên JOHN.

**Gợi ý:** Join `film` → `film_actor` → `actor`, filter `a.first_name = 'JOHN'`.

### Exercise 5-3
Viết query tìm tất cả các cặp địa chỉ **trong cùng một thành phố** (self-join bảng `address`).

**Gợi ý:**
```sql
SELECT a1.address, a2.address, a1.city_id
FROM address a1
INNER JOIN address a2
  ON a1.city_id = a2.city_id
  AND a1.address_id <> a2.address_id;
```

---

## 📚 Tiếp theo: Chapter 6 - Working with Sets

Chương tiếp theo sẽ tìm hiểu về **Set Operators** (toán tử tập hợp):
- `UNION` / `UNION ALL`: Hợp hai tập kết quả.
- `INTERSECT`: Giao hai tập kết quả.
- `EXCEPT`: Hiệu hai tập kết quả.
