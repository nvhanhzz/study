# SQL Performance Explained - Markus Winand

> **Tác giả:** Markus Winand  
> **Xuất bản:** 2012 (Self-published)  
> **Số trang:** ~200

---

## 📖 Giới thiệu

Sách tập trung vào **tối ưu hiệu năng SQL thông qua Indexing**. Kiến thức áp dụng cho:
- Oracle Database
- MySQL
- PostgreSQL
- SQL Server

**Triết lý:** Developer cần hiểu Index, không phải chỉ DBA.

---

## 📚 Mục lục các chương

| Chương         | Tên                         | Nội dung chính                                               |
| -------------- | --------------------------- | ------------------------------------------------------------ |
| **Preface**    | Developers Need to Index    | Tại sao dev cần học indexing, phân chia vai trò Dev/DBA      |
| **1**          | Anatomy of an Index         | B-tree, Doubly Linked List, Leaf nodes, Slow indexes         |
| **2**          | The Where Clause            | Equality, Functions, Ranges, LIKE, Partial Index, NULL       |
| **3**          | Performance and Scalability | Data volume impact, System load, Response time vs Throughput |
| **4**          | The Join Operation          | Nested Loops, Hash Join, Sort Merge                          |
| **5**          | Clustering Data             | Index Filter Predicates, Index-Only Scan, IOT                |
| **6**          | Sorting and Grouping        | Indexing ORDER BY, ASC/DESC, GROUP BY                        |
| **7**          | Partial Results             | Top-N queries, Pagination, Window functions                  |
| **8**          | Modifying Data              | Impact của Index lên INSERT, UPDATE, DELETE                  |
| **Appendix A** | Execution Plans             | Đọc execution plan cho Oracle, PostgreSQL, SQL Server, MySQL |

---

## 🎯 Đặc điểm sách

- ✅ Ngắn gọn (~200 trang)
- ✅ Cross-database (áp dụng cho nhiều DB)
- ✅ Tập trung vào 1 chủ đề: B-tree Index
- ✅ Có website bổ sung: use-the-index-luke.com

---

## 📝 Ghi chú học tập

- [sql-performance-chapter1-summary.md](./sql-performance-chapter1-summary.md) - Tóm tắt Preface & Chapter 1
