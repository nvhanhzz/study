# SQL Performance Explained - Tóm tắt Preface & Chapter 1

> **Sách:** SQL Performance Explained - Markus Winand  
> **Ngày học:** 2026-01-05

---

## 📖 Preface: Developers Need to Index

### Ý chính
- **SQL không chậm** - vấn đề nằm ở cách sử dụng
- **Indexing là việc của Developer**, không phải DBA
- Developer có thông tin quan trọng nhất: **cách app query dữ liệu**

### Phân chia vai trò Dev vs DBA

| Công việc                | Developer |  DBA   |
| ------------------------ | :-------: | :----: |
| Quyết định cần index nào |     ✅     | Tư vấn |
| Viết query SQL           |     ✅     |   ❌    |
| Cấu hình database server |     ❌     |   ✅    |
| Backup/Recovery          |     ❌     |   ✅    |

---

## 📖 Chapter 1: Anatomy of an Index

### 1. Index là gì?
- Cấu trúc dữ liệu **RIÊNG BIỆT** trong database
- Chứa **bản sao** của cột được index + ROWID
- **Đã sắp xếp** (khác với table gốc)

### 2. Cấu trúc Index = B-Tree + Doubly Linked List

```
         B-TREE (để tìm nhanh)
              [Root]
               /  \
         [Branch] [Branch]
          /    \    /    \
        ↓      ↓  ↓      ↓
       [Leaf]←→[Leaf]←→[Leaf]  ← LINKED LIST (để duyệt tuần tự)
         ↓       ↓       ↓
       [Table Data via ROWID]
```

### 3. Các loại Node

| Node Type       | Chứa gì                         | Mục đích          |
| --------------- | ------------------------------- | ----------------- |
| **Branch Node** | Key + Pointer to child          | Dẫn đường         |
| **Leaf Node**   | Key + ROWID + Prev/Next pointer | Lưu dữ liệu index |

### 4. Leaf Node chi tiết
```
┌──────────────────┬───────────┐
│ INDEX COL VALUE  │   ROWID   │  → Trỏ đến row trong table
├──────────────────┼───────────┤
│ a@gmail.com      │   0x1A2B  │
│ b@gmail.com      │   0x3C4D  │
└──────────────────┴───────────┘
```

### 5. Composite Index (nhiều cột)
- Leaf node chứa **tất cả giá trị các cột** + ROWID
- **Thứ tự cột quan trọng**: Cột đầu tiên PHẢI có trong WHERE

### 6. Table gốc = HEAP
- **Không sắp xếp**, rows nằm theo thứ tự INSERT
- **Full Table Scan** khi không có index: O(n)

### 7. Tại sao Index có thể chậm?

3 bước của Index Lookup:
1. **Tree Traversal** - Luôn nhanh O(log n) ✅
2. **Follow Leaf Node Chain** - Có thể chậm nếu nhiều match ⚠️
3. **Table Access** - Có thể chậm nếu nhiều random I/O ⚠️

### 8. Index ảnh hưởng đến WRITE

| Thao tác | Không Index      | Có Index                       |
| -------- | ---------------- | ------------------------------ |
| INSERT   | Nhanh            | Chậm hơn (phải maintain index) |
| UPDATE   | Chậm (Full Scan) | Nhanh hơn (tìm nhanh)          |
| DELETE   | Chậm (Full Scan) | Nhanh hơn (tìm nhanh)          |

**Trade-off:** Index tăng tốc READ, làm chậm WRITE

### 9. Kích thước Index
- Index **nhỏ hơn** table gốc (chỉ chứa indexed columns)
- Cột càng lớn → Index càng lớn
- Nhiều index → Tốn nhiều disk space

---

## 💬 Các điểm thảo luận thêm

### Query Optimizer chọn Index như thế nào?
1. **Selectivity** - Ít rows match → dùng Index
2. **Có index trên cột WHERE không?**
3. **Có function trên cột không?** (function → không dùng được index)
4. **Statistics** - DB cần thống kê cập nhật

### Xem Query dùng Index nào?
```sql
-- PostgreSQL/MySQL
EXPLAIN SELECT * FROM users WHERE email = 'x@gmail.com';

-- Kết quả tốt: Index Scan
-- Kết quả xấu: Seq Scan (Full Table Scan)
```

---

## ✅ Key Takeaways

1. **Developer phải học Indexing** - Đây là development task
2. **Index = B-tree + Linked List** - Hiểu cấu trúc để tối ưu
3. **Không phải lúc nào Index cũng tốt** - Selectivity cao thì Full Scan tốt hơn
4. **Composite Index**: Thứ tự cột rất quan trọng
5. **Trade-off**: READ nhanh hơn ↔ WRITE chậm hơn
6. **Luôn dùng EXPLAIN** để kiểm tra query

---

## 📚 Tiếp theo: Chapter 2 - The Where Clause
