# High-Performance Java Persistence

> **Tác giả:** Vlad Mihalcea
> **Năm xuất bản:** 2016 (First Edition) / 2021 (nếu là bản mới hơn, nhưng theo file PDF scan được là © 2015 - 2016)
> **Chủ đề:** Java, Database Performance, JDBC, Hibernate, JPA

---

## 📖 Giới thiệu

Cuốn sách này là tài liệu chuyên sâu về tối ưu hóa hiệu năng cho lớp Persistence trong các ứng dụng Java Enterprise. Khác với các tài liệu hướng dẫn cơ bản về JPA/Hibernate, cuốn sách này tập trung vào bài toán hiệu năng: làm sao để khai thác tối đa sức mạnh của Database, JDBC, và ORM Framework. Tác giả **Vlad Mihalcea** là một chuyên gia hàng đầu về Hibernate (Developer Advocate lại Hibernate project), nên các kiến thức được chia sẻ đều rất thực chiến và đi sâu vào bản chất (under the hood).

Cuốn sách phù hợp với:
- Java Developers đã có kiến thức cơ bản về JPA/Hibernate.
- Software Architects muốn thiết kế các hệ thống chịu tải cao (High Throughput, Low Latency).
- DBA muốn hiểu cách ứng dụng Java tương tác với Database.

---

## 📚 Mục lục (Table of Contents)

Sách được chia làm 3 phần chính:

### **Phần 1: Introduction**
Giới thiệu các khái niệm cơ bản về hiệu năng và khả năng mở rộng (Scaling).

| Chương | Tên | Nội dung chính |
| :--- | :--- | :--- |
| **1** | Preface | Giới thiệu vai trò của Persistence Layer. |
| **2** | Performance and Scaling | Response Time vs Throughput, Scaling Up vs Scaling Out, Master-Slave Replication, Sharding. |

### **Phần 2: JDBC and Database Essentials**
Đi sâu vào tầng thấp nhất là JDBC và các khái niệm cốt lõi của Database mà mọi Developer cần nắm vững (trước khi dùng ORM).

| Chương | Tên | Nội dung chính |
| :--- | :--- | :--- |
| **3** | JDBC Connection Management | Connection Pooling, Monitoring. |
| **4** | Batch Updates | Cơ chế Batching INSERT/UPDATE để tăng tốc độ ghi. |
| **5** | Statement Caching | Server-side & Client-side statement caching. |
| **6** | ResultSet Fetching | Tối ưu hóa việc lấy dữ liệu (Fetch Size, Pagination). |
| **7** | Transactions | ACID (Atomicity, Consistency, Isolation, Durability), Isolation Levels, Locking (Optimistic/Pessimistic). |

### **Phần 3: JPA and Hibernate**
Áp dụng các kiến thức JDBC vào Hibernate để tối ưu hóa.

| Chương | Tên | Nội dung chính |
| :--- | :--- | :--- |
| **8** | Why JPA and Hibernate matter | Sự khác biệt giữa JPA và Hibernate, Write-behind optimizations. |
| **9** | Connection Management and Monitoring | Quản lý connection trong Hibernate. |
| **10** | Mapping Types and Identifiers | Hiệu năng của các chiến lược sinh khóa chính (Identity, Sequence, Table, UUID). |
| **11** | Relationships | Tối ưu hóa các quan hệ @OneToMany, @ManyToOne, @ManyToMany (hạn chế N+1 problem). |
| **12** | Inheritance | Chiến lược mapping thừa kế (Single Table, Joined, Table-per-class) và ảnh hưởng hiệu năng. |
| **13** | Flushing | Cơ chế tự động đẩy dữ liệu xuống DB (Dirty Checking, Order). |
| **14** | Batching | Tự động batching với Hibernate. |
| **15** | Fetching | Fetching strategies (Eager vs Lazy, JOIN FETCH, EntityGraph). |
| **16** | Caching | First-level, Second-level cache, Query cache. |
| **17** | Concurrency Control | Xử lý tranh chấp dữ liệu mức ứng dụng (Optimistic Locking với @Version). |

*(Lưu ý: Mục lục chi tiết có thể dài hơn, đây là tóm tắt các chương chính dựa trên file content scan được).*

---

## 🎯 Đặc điểm nổi bật

- ✅ **Deep Dive into JDBC:** Không bỏ qua nền tảng JDBC như nhiều sách Hibernate khác.
- ✅ **Database Internals:** Giải thích rõ cơ chế của DB (Oracle, SQL Server, MySQL, PostgreSQL) ảnh hưởng thế nào đến Java code.
- ✅ **Practical Benchmarks:** Có các bài test hiệu năng so sánh giữa các phương án.
- ✅ **Best Practices:** Đưa ra lời khuyên cụ thể (ví dụ: nên dùng Sequence ID generator thay vì Identity, nên dùng Set hay List cho @OneToMany...).
