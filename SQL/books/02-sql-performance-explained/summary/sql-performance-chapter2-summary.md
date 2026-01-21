# SQL Performance Explained - Tóm tắt Chapter 2

> **Sách:** SQL Performance Explained - Markus Winand
> **Ngày học:** 2026-01-21

---

## 📖 Chapter 2: The Where Clause

Chương này tập trung vào thành phần quan trọng nhất ảnh hưởng đến hiệu năng truy vấn: **WHERE clause**. Mục tiêu là viết điều kiện tìm kiếm sao cho database có thể tận dụng Index hiệu quả nhất, tránh các lỗi phổ biến (anti-patterns).

---

## 1. The Equality Operator (`=`)

Toán tử bằng là phổ biến nhất nhưng vẫn dễ gây lỗi hiệu năng.

### 1.1. Primary Keys

**Unique Index (Mặc định):**

- Database tự tạo **Unique Index** cho PK.
- Dùng `INDEX UNIQUE SCAN`: đi xuống B-Tree, tìm thấy 1 entry → dừng ngay.
- Chi phí: O(log n), gần như không đổi dù bảng có triệu rows.

**Non-unique Index (Deferrable Constraints):**

- **Deferrable Constraint:** Hoãn kiểm tra uniqueness đến lúc COMMIT (thay vì kiểm tra ngay sau mỗi INSERT/UPDATE).
- Dùng cho: Circular foreign keys, swap PK values.
- Khi đó DB dùng `INDEX RANGE SCAN`, nhưng constraint vẫn đảm bảo tối đa 1 row → hiệu năng tương đương.

---

### 1.2. Concatenated Indexes (Composite Index)

**Nguyên tắc "Telephone Directory":**
Index `(A, B)` như danh bạ sắp theo Họ rồi Tên:

| Query                   | Dùng được Index?        |
| :---------------------- | :---------------------- |
| `WHERE A = ?`           | ✅ Có                    |
| `WHERE A = ? AND B = ?` | ✅ Có                    |
| `WHERE B = ?`           | ❌ Không (B nằm rải rác) |

**Leftmost Prefix Rule:**
Index `(A, B, C)` hỗ trợ: `A`, `A+B`, `A+B+C`. KHÔNG hỗ trợ: `B`, `C`, `B+C`.

**Ví dụ Company Merger:**

```
Cũ:  PK (employee_id)           → Unique
Mới: PK (subsidiary_id, employee_id) 
```

- Index `(employee_id, subsidiary_id)` → Query `subsidiary_id = ?` bị Full Scan ❌
- Index `(subsidiary_id, employee_id)` → Hỗ trợ cả 2 loại query ✅

> 💡 **Redundant Index Tip:**
> Nếu bạn đã có index `(A, B, C)`, bạn **CÓ THỂ XÓA** index cũ trên `(A)` hoặc `(A, B)`.
> Index lớn `(A, B, C)` đã bao gồm chức năng của các index tiền tố. Xóa index thừa giúp tiết kiệm disk và tăng tốc INSERT/UPDATE.

---

### 1.3. Slow Indexes

**3 bước Index Lookup:**

1. **Tree Traversal:** O(log n) - luôn nhanh ✅
2. **Leaf Node Chain:** Có thể chậm nếu phạm vi lớn ⚠️
3. **Table Access (Random I/O):** Điểm nghẽn chính! ⚠️

**Random I/O vs Sequential I/O:**

| Loại             | HDD            | SSD          | NVMe           |
| :--------------- | :------------- | :----------- | :------------- |
| Random I/O       | 5-20 ms/block  | 0.05 ms      | 0.02 ms        |
| Sequential I/O   | 0.05 ms/block  | 0.01 ms      | 0.005 ms       |
| Tỷ lệ Random/Seq | ~100x chậm hơn | ~5x chậm hơn | ~2-4x chậm hơn |

> 💡 **Trên SSD/NVMe:** Lý thuyết vẫn đúng, chỉ ngưỡng thay đổi. Index vẫn quan trọng để giảm data processed (CPU bottleneck thay vì I/O).

**Cost Unit:**

- Đơn vị **trừu tượng** để so sánh các plan, không phải thời gian thực.
- Cost ≈ (Số I/O × Trọng số I/O) + (CPU ops × Trọng số CPU)
- Trên SSD, nên giảm `random_page_cost` (PostgreSQL) hoặc `optimizer_index_cost_adj` (Oracle).

---

### 1.4. Statistics & Optimizer

**Statistics bao gồm:**

| Loại       | Thông tin                                          |
| :--------- | :------------------------------------------------- |
| **Table**  | Row count, block count, avg row size               |
| **Column** | Distinct values, NULL count, Min/Max,**Histogram** |
| **Index**  | Tree depth, leaf blocks, clustering factor         |

**Histogram - Quan trọng nhất:**

- Mô tả phân bố dữ liệu (ví dụ: status 'ACTIVE' 95%, 'DONE' 5%).
- Không có histogram → Optimizer giả định phân bố đều → Chọn sai plan.

**Optimizer chọn Index thế nào?**

1. Liệt kê tất cả access paths (Full Scan, Index A, Index B...).
2. Tính Cost cho từng plan dựa trên Statistics.
3. Chọn plan có Cost thấp nhất.

---

### 1.5. Auto-Update Statistics

**Trigger dựa trên % thay đổi:**

| Database   | Ngưỡng mặc định             | Thời điểm chạy                           |
| :--------- | :-------------------------- | :--------------------------------------- |
| PostgreSQL | 10% rows thay đổi           | Autovacuum daemon (kiểm tra mỗi phút)    |
| MySQL      | 10% rows thay đổi           | Lazy, khi query tiếp theo truy cập table |
| SQL Server | 500 + 20% (hoặc sqrt-based) | Khi compile query                        |
| Oracle     | 10% stale                   | Maintenance window (đêm)                 |

**Vấn đề độ trễ:**

- Bulk insert 500K rows lúc 09:00 → Auto-analyze chạy 09:05.
- Trong 5 phút đó, Statistics sai → Query plan có thể tệ.

**Best Practice:**

```sql
-- Sau bulk operations, chạy thủ công:
ANALYZE table_name;              -- PostgreSQL/MySQL
EXEC DBMS_STATS.GATHER_TABLE_STATS(...);  -- Oracle
UPDATE STATISTICS table_name;    -- SQL Server
```

---

## 2. Functions & Expressions

Database không hiểu ý nghĩa của hàm, nó coi hàm là một **Black Box**.

### 2.1. Case-Insensitive Search

- Query: `WHERE UPPER(last_name) = 'WINAND'`
- Index: `last_name` -> **VÔ DỤNG**.
- **Giải pháp:**
  1. **Function-based Index (FBI):** Index trực tiếp biểu thức `UPPER(last_name)`.
  2. **Computed Column (SQL Server):** Tạo cột ảo tính toán trước và index cột đó.

### 2.2. User-Defined Functions (UDF)

- Có thể index UDF nếu nó là **DETERMINISTIC** (cùng input luôn ra cùng output).
- **Lưu ý:** Không thể index hàm phụ thuộc thời gian (`age()`, `sysdate`) hoặc ngẫu nhiên.

### 2.3. Over-Indexing

- Đừng tạo index cho mọi cột hoặc mọi biến thể hàm (`last_name`, `upper`, `lower`...).
- **Tip:** Thống nhất quy chuẩn query trong app (ví dụ luôn dùng `UPPER`) để chỉ cần duy trì 1 index.

---

## 3. Parameterized Queries (Bind Variables)

Bind parameters là cách truyền giá trị vào SQL mà không viết trực tiếp vào câu lệnh.

```sql
-- Không dùng bind parameter (literal)
SELECT * FROM employees WHERE subsidiary_id = 30;

-- Dùng bind parameter
SELECT * FROM employees WHERE subsidiary_id = ?;  -- ? là placeholder
```

---

### 3.1. Lợi ích của Bind Parameters

**A. Bảo mật - Chống SQL Injection:**

```java
// ❌ Nguy hiểm - SQL Injection
String sql = "SELECT * FROM users WHERE name = '" + userInput + "'";
// Nếu userInput = "'; DROP TABLE users; --" → THẢM HỌA!

// ✅ An toàn - Bind parameter
PreparedStatement stmt = conn.prepareStatement(
    "SELECT * FROM users WHERE name = ?");
stmt.setString(1, userInput);  // Giá trị được escape tự động
```

**C. Cơ chế bảo mật (Tại sao `?` an toàn?):**
Bind parameter tách biệt hoàn toàn **MÃ LỆNH** (Code) và **DỮ LIỆU** (Data).
- Bước 1: Gửi khung SQL `... WHERE name = ?` để DB biên dịch.
- Bước 2: Gửi data `Admin'; DROP TABLE...`.
- Kết quả: DB coi toàn bộ data chỉ là một chuỗi ký tự vô hại (literal string), không thể thực thi lệnh trong đó.

**B. Hiệu năng - Execution Plan Cache:**

```
Không dùng Bind Parameter:
  Query 1: "... WHERE id = 1"  → Parse → Optimize → Execute
  Query 2: "... WHERE id = 2"  → Parse → Optimize → Execute  (lặp lại!)
  Query 3: "... WHERE id = 3"  → Parse → Optimize → Execute  (lặp lại!)

Dùng Bind Parameter:
  Query 1: "... WHERE id = ?"  → Parse → Optimize → Cache plan
  Query 2: "... WHERE id = ?"  → Reuse cached plan → Execute
  Query 3: "... WHERE id = ?"  → Reuse cached plan → Execute
```

> 💡 **So sánh với compiler:** Literal values = constants (compile mỗi lần). Bind params = variables (compile 1 lần, chạy nhiều lần).

---

### 3.2. Vấn đề: Data Skew (Phân bố không đều)

**Tình huống:**

- Cột `status` có 2 giá trị: `ACTIVE` (99%) và `PENDING` (1%).
- Với `ACTIVE`: Full Table Scan nhanh hơn (đọc gần hết bảng).
- Với `PENDING`: Index Scan nhanh hơn (chỉ 1% rows).

**Vấn đề với Bind Parameter:**

```sql
SELECT * FROM orders WHERE status = ?;
```

- Optimizer **không thấy** giá trị `?` lúc compile.
- Không thể dùng Histogram để ước lượng.
- Phải chọn **1 plan dùng chung** cho cả 2 trường hợp → Một trong hai sẽ bị chậm!

---

### 3.3. Giải pháp của các Database

**Oracle - Bind Peeking (9i) + Adaptive Cursor Sharing (11g):**

| Tính năng                   | Cách hoạt động                                                                                      |
| :-------------------------- | :-------------------------------------------------------------------------------------------------- |
| **Bind Peeking**            | "Nhìn trộm" giá trị bind lần đầu để optimize. Vấn đề: Plan phụ thuộc vào giá trị đầu tiên!          |
| **Adaptive Cursor Sharing** | Lưu nhiều plan cho cùng 1 query. Chọn plan phù hợp dựa trên selectivity ước lượng của giá trị bind. |

**SQL Server - Parameter Sniffing:**

- Tương tự Bind Peeking của Oracle.
- Có thể dùng `OPTION(RECOMPILE)` để force optimize mỗi lần.
- `OPTIMIZE FOR` hint để fix plan cho giá trị cụ thể.

**PostgreSQL:**

- Plan cache chỉ trong phạm vi **PreparedStatement đang mở**.
- Sau 5 lần execute, mới bắt đầu cache plan.
- Ít bị ảnh hưởng bởi vấn đề này.

**MySQL:**

- **Không có** execution plan cache (optimizer đơn giản, compile nhanh).
- Mỗi query đều optimize lại → Không bị vấn đề này nhưng có overhead nhỏ.

---

### 3.4. Khi nào KHÔNG nên dùng Bind Parameter?

| Tình huống                                         | Lý do                                                           |
| :------------------------------------------------- | :-------------------------------------------------------------- |
| **Status flags lệch** (`ACTIVE` 99%, `PENDING` 1%) | Cần plan khác nhau cho mỗi giá trị                              |
| **LIKE với leading wildcard** (`LIKE '%term'`)     | Optimizer cần biết có wildcard ở đầu hay không                  |
| **Partition pruning**                              | Optimizer cần biết giá trị để loại bỏ partition không cần thiết |

**Giải pháp:** Dùng literal value **có kiểm soát** (không phải user input trực tiếp).

```java
// Status là enum cố định, không phải user input → an toàn
String sql = "SELECT * FROM orders WHERE status = '" + Status.PENDING.name() + "'";
```

---

### 3.5. Code Examples

**Java:**

```java
PreparedStatement stmt = conn.prepareStatement(
    "SELECT * FROM employees WHERE subsidiary_id = ?");
stmt.setInt(1, subsidiaryId);
ResultSet rs = stmt.executeQuery();
```

**Python (psycopg2):**

```python
cursor.execute(
    "SELECT * FROM employees WHERE subsidiary_id = %s",
    (subsidiary_id,)
)
```

**C# (ADO.NET):**

```csharp
var cmd = new SqlCommand(
    "SELECT * FROM employees WHERE subsidiary_id = @sub_id", conn);
cmd.Parameters.AddWithValue("@sub_id", subsidiaryId);
```

**Node.js (pg):**

```javascript
const result = await pool.query(
    'SELECT * FROM employees WHERE subsidiary_id = $1',
    [subsidiaryId]
);
```

---

### 3.6. Tóm tắt

|                   | Bind Parameter         | Literal Value                 |
| :---------------- | :--------------------- | :---------------------------- |
| **SQL Injection** | ✅ An toàn              | ❌ Nguy hiểm                   |
| **Plan Cache**    | ✅ Reuse được           | ❌ Compile mỗi lần             |
| **Data Skew**     | ⚠️ Có thể chọn sai plan | ✅ Optimizer thấy giá trị thực |
| **Khuyến nghị**   | Mặc định luôn dùng     | Chỉ dùng khi cần control plan |

---

## 4. Searching for Ranges (`>`, `<`, `BETWEEN`, `LIKE`)

### 4.1. WHERE Clause Order: Không quan trọng!

Thứ tự điều kiện trong câu lệnh `WHERE` **KHÔNG** ảnh hưởng hiệu năng.

```sql
-- Hai câu này có Execution Plan giống hệt nhau
SELECT * FROM table WHERE A = 1 AND B = 2;
SELECT * FROM table WHERE B = 2 AND A = 1;
```

- **Optimizer** tự quyết định thứ tự thực thi dựa trên Statistics.
- **Lưu ý:** Thứ tự cột trong `CREATE INDEX(A, B)` mới là thứ quan trọng!

### 4.2. Quy tắc vàng Indexing

> **"Index for equality first — then for ranges."**
> (Ưu tiên cột so sánh bằng trước, cột so sánh dải sau)

**Ví dụ:** `WHERE dob BETWEEN ? AND ? AND sub_id = ?`

- Index `(dob, sub_id)`: **Tệ**. Scan phạm vi `dob`, sau đó lọc `sub_id`.
- Index `(sub_id, dob)`: **Tốt**. Jump đến `sub_id`, sau đó scan phạm vi `dob`.

### 4.3. LIKE Filters (Chi tiết)

Index B-Tree chạy từ trái sang phải, nên vị trí wildcard `%` quyết định tất cả.

| Pattern        | Index B-Tree? | Tại sao?                               | Giải pháp tối ưu                                                          |
| :------------- | :------------ | :------------------------------------- | :------------------------------------------------------------------------ |
| `LIKE 'ABC%'`  | ✅**Tốt**      | Biết điểm bắt đầu, dùng Range Scan.    | Index thường là đủ.                                                       |
| `LIKE '%ABC'`  | ❌**Không**    | Không biết bắt đầu từ đâu → Full Scan. | **Reverse Index**: Lưu ngược (`CBA`), tìm `LIKE 'CBA%'`.                  |
| `LIKE '%ABC%'` | ❌**Không**    | Không biết đầu/cuối → Full Scan.       | **Full-Text Search** (ElasticSearch) hoặc **Trigram Index** (PostgreSQL). |

> 💡 **Lưu ý:** Với `%ABC%`, đừng cố optimize bằng B-Tree thường. Hãy đổi công nghệ (Full-Text Search).

---

## 5. Index Merge & Partial Indexes

### 5.1. Index Merge

- **Câu hỏi:** Nên tạo 1 index nhiều cột hay nhiều index đơn lẻ?
- **Trả lời:** **1 Index nhiều cột** thường tốt hơn.
- Việc merge kết quả từ 2 index tốn CPU/RAM. Bitmap Index merge tốt nhưng không hợp với OLTP (do locking issue).

### 5.2. Partial Index (Filtered Index)

- Chỉ index một phần dữ liệu thỏa mãn điều kiện `WHERE`.
- **Ví dụ:** Queue system, chỉ cần query message chưa xử lý (`processed = 'N'`).
- `CREATE INDEX ... WHERE processed = 'N'` -> Index rất nhỏ, nhanh, tiết kiệm disk.

---

## 6. NULL Nuances

## 6. NULL Nuances (Oracle Specific)

### 6.1. Oracle NULL vs Empty String

- SQL Standard: `NULL` là unknown, `''` (empty string) là chuỗi rỗng có độ dài 0.
- **Oracle:** Coi `''` chính là `NULL`.
- **Hệ quả:** `VARCHAR2` không bao giờ lưu được chuỗi rỗng thực sự.

### 6.2. NULL trong Index

Quy tắc quan trọng nhất của Oracle Index:

> **"Oracle KHÔNG index những dòng mà TẤT CẢ các cột trong index đều là NULL."**

Ví dụ index `(date_of_birth)`:

- Row 1: `date_of_birth = '2000-01-01'` → Có trong index ✅
- Row 2: `date_of_birth = NULL` → **KHÔNG** có trong index ❌

### 6.3. Vấn đề với Query `IS NULL`

Do NULL không có trong index, query `WHERE col IS NULL` sẽ dẫn đến **Full Table Scan**.

```sql
SELECT * FROM employees WHERE date_of_birth IS NULL;
-- Index (date_of_birth) bị vô hiệu hóa vì thiếu dữ liệu NULL.
-- DB buộc phải quét cả bảng để tìm những row bị "bỏ rơi" này.
```

### 6.4. Giải pháp (Workarounds)

**Cách 1: Thêm cột NOT NULL vào Index (Composite Index)**
Thêm một cột chắc chắn có dữ liệu (vd: `subsidiary_id` hoặc PK) vào index.

- Index: `(date_of_birth, subsidiary_id)`
- Vì `subsidiary_id` luôn có giá trị, dòng có `date_of_birth` NULL vẫn được index (vì key không hoàn toàn NULL).
- Query `IS NULL` dùng được index ✅.

**Cách 2: Sử dụng Constant (Function-based Index)**
Nếu không có cột nào phù hợp để ghép cặp, bạn có thể ghép với hằng số:

```sql
CREATE INDEX idx_dob_null ON employees (date_of_birth, 1);
```

- Số `1` không bao giờ NULL → Mọi dòng đều được index.

**Cách 3: Emulating Partial Indexes**
Dùng đặc tính này để tạo "Partial Index" (như PostgreSQL) trên Oracle:

- Bạn chỉ muốn index những dòng ACTIVE?
- Tạo hàm trả về ID nếu ACTIVE, trả về NULL nếu INACTIVE.
- Index trên hàm đó. Những dòng INACTIVE (NULL) sẽ tự động bị loại khỏi index -> Index nhỏ gọn!

```sql
-- PostgreSQL: CREATE INDEX ... WHERE status = 'ACTIVE'
-- Oracle:     CREATE INDEX ... ON (CASE WHEN status='ACTIVE' THEN id END)
```

---

## 7. Obfuscated Conditions (Anti-Patterns)

### 7.1. Date Truncation (Cắt gọt ngày)

- **Sai:** `TRUNC(sale_date) = TRUNC(sysdate)`
- **Tại sao:** Hàm `TRUNC` bao bọc cột index `sale_date` → Index bị vô hiệu.
- **Sửa:** Dùng so sánh dải (Range).
  ```sql
  sale_date >= TRUNC(sysdate) 
  AND sale_date < TRUNC(sysdate + 1)
  ```

### 7.2. Numeric Strings (Chuỗi số)

- **Sai:** `to_number(numeric_string) = 42`
- **Tại sao:** Hàm chuyển đổi kiểu ẩn hoặc hiện trên cột.
- **Sửa:** So sánh đúng kiểu dữ liệu.
  ```sql
  numeric_string = '42'
  ```

### 7.3. Math Operations (Phép toán)

- **Sai:** `salary + 1000 > 5000`
- **Tại sao:** Biểu thức toán học trên cột index.
- **Sửa:** Chuyển hằng số sang vế phải.
  ```sql
  salary > 5000 - 1000
  ```

### 7.4. Combining Columns (Gộp cột)

- **Sai:** `col_a || col_b = 'AB'`
- **Tại sao:** Mất thứ tự sắp xếp của index riêng lẻ.
- **Sửa:** Tách điều kiện hoặc dùng Computed Column.
  ```sql
  col_a = 'A' AND col_b = 'B'
  ```

### 7.5. Smart Logic (Query "vạn năng")

- **Sai:** `(:val IS NULL OR col = :val)`
- **Tại sao:** Optimizer bối rối, thường chọn Full Scan để an toàn cho case tệ nhất (:val is NULL).
- **Sửa:** Dùng **Dynamic SQL** (MyBatis `if`, Hibernate Criteria) để build query chỉ chứa điều kiện cần thiết.

---

## ✅ Key Takeaways

1.  **Primary Key & Equality:** PK thường nhanh nhất (Unique Scan). Đừng quên `INDEX RANGE SCAN` vẫn rất hiệu quả cho non-unique index.
2.  **Index Columns Order:** Quan trọng hơn cả thứ tự WHERE. Luôn nhớ quy tắc **Leftmost Prefix** (như danh bạ điện thoại).
3.  **Redundant Index:** Index `(A, B)` bao trùm `(A)`. Hãy xóa `(A)` để tiết kiệm disk và tăng tốc ghi.
4.  **Functions & Expressions:** Index chết khi cột bị bọc trong hàm (vd: `UPPER(col)`). Giải pháp: FBI hoặc Computed Column.
5.  **Bind Parameters (`?`):**
    -   Bảo mật (Chống SQL Injection).
    -   Hiệu năng (Plan Cache), nhưng coi chừng Data Skew.
6.  **Range Scan Rule:** "Equality first, Range last". Luôn đặt cột so sánh bằng (`=`) lên trước trong Index.
7.  **LIKE Filters:**
    -   `LIKE 'ABC%'`: Tốt.
    -   `LIKE '%ABC'`: Dùng Reverse Index.
    -   `LIKE '%ABC%'`: Cân nhắc Full-Text Search.
8.  **Smart Logic:** Tránh `OR IS NULL`. Hãy dùng Dynamic SQL để tối ưu từng case.
9.  **Oracle NULL:** Oracle không index toàn bộ NULL. Cần workaround cho `IS NULL` query.
