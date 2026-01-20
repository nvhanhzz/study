# 🔑 SQL Key Takeaways (Đúc kết quan trọng)

Tài liệu này tổng hợp những kiến thức cốt lõi và quan trọng nhất được đúc kết trong quá trình học SQL.

---

## 1. Xử lý NULL (NULL Handling)

NULL đại diện cho sự thiếu vắng giá trị (unknown/missing), không phải là 0 hay chuỗi rỗng.

-   **Cạm bẫy so sánh:** Mọi phép so sánh trực tiếp với NULL (ví dụ: `col = NULL`, `col != 10`) đều trả về `UNKNOWN` (không True, không False).
-   **Hệ quả:**
    -   `WHERE col != 'A'` sẽ loại bỏ luôn cả những dòng mà `col` là NULL (đôi khi đây là hành vi không mong muốn).
    -   `COUNT(col)` sẽ không đếm các giá trị NULL.
-   **Giải pháp:** Luôn sử dụng `IS NULL` hoặc `IS NOT NULL` để kiểm tra. Sử dụng `COALESCE` nếu cần giá trị mặc định.

> 📚 **Tham khảo chi tiết:**
> - [Learning SQL - Chapter 4: Filtering](file:///home/hanhnv/IdeaProjects/Study/SQL/books/01-learning-sql/summary/learning-sql-chapter4-summary.md) (Mục 3: NULL)

---

## 2. Bản chất của Index (Indexing Basics)

Index giúp tăng tốc độ truy vấn đọc nhưng có chi phí đi kèm.

-   **Cấu trúc dữ liệu:** Hầu hết các Database sử dụng cấu trúc **B-Tree** (Balanced Tree).
    -   Cây được cân bằng để độ sâu là đồng nhất (truy xuất nhanh).
    -   **Leaf Nodes** (Lá): Chứa dữ liệu thực tế (hoặc trỏ tới bảng chính) và được liên kết với nhau bằng một **Danh sách liên kết đôi (Doubly Linked List)**. Điều này giúp việc duyệt range (ví dụ: `WHERE id > 10 AND id < 100`) cực kỳ hiệu quả.
-   **Trade-off (Đánh đổi):**
    -   Tăng tốc SELECT (`WHERE`, `ORDER BY`).
    -   Làm chậm INSERT/UPDATE/DELETE (vì phải cập nhật cả cây Index).
    -   Tốn tài nguyên bộ nhớ và đĩa để lưu trữ cây Index.

> 📚 **Tham khảo chi tiết:**
> - **SQL Performance Explained - Chapter 1: Anatomy of an Index** (Tham khảo cấu trúc B-Tree và Leaf Nodes traversal).
