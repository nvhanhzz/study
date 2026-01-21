# SQL Performance Explained - Tóm tắt Chapter 3

> **Sách:** SQL Performance Explained - Markus Winand
> **Ngày học:** 2026-01-21

---

## 📖 Chapter 3: Performance and Scalability

Chương này làm rõ khái niệm về **Scalability** (Khả năng mở rộng) và **Performance** (Hiệu năng). Tác giả nhấn mạnh rằng thêm phần cứng không phải là giải pháp vạn năng cho mọi vấn đề hiệu năng, và việc đánh Index đúng đắn đóng vai trò quan trọng như thế nào khi dữ liệu và tải hệ thống tăng lên.

---

## 1. Định nghĩa Scalability

Thường mọi người nghĩ Scalability là khả năng hệ thống hoạt động tốt khi thêm phần cứng ("scale-out", "scale-up"). Tuy nhiên, định nghĩa chính xác hơn trong ngữ cảnh này là:

> **Scalability là sự tác động đến hiệu năng khi các tham số môi trường thay đổi.**

Các tham số môi trường bao gồm:
-   **Data Volume:** Lượng dữ liệu trong database.
-   **System Load:** Số lượng request đồng thời.
-   **Hardware:** Cấu hình máy chủ.

---

## 2. Tác động của Data Volume (Lượng dữ liệu)

Khi dữ liệu tăng lên, câu truy vấn sẽ chậm đi. Tuy nhiên, tốc độ chậm đi như thế nào phụ thuộc vào Index.

### Ví dụ so sánh:
Cùng một query `SELECT count(*) FROM data WHERE section = ? AND id2 = ?`, nhưng với 2 cách đánh index khác nhau:
1.  **Fast Index:** Thứ tự cột đúng `(section, id2)`.
2.  **Slow Index:** Thứ tự cột sai `(section, id1, id2)` (cột `id1` chen giữa làm `id2` không thể dùng làm Access Predicate).

**Kết quả:**
-   Ở dữ liệu nhỏ: Cả hai đều nhanh (< 0.1s).
-   Ở dữ liệu lớn (gấp 100 lần):
    -   **Fast Index:** Chậm đi một chút (tuyến tính theo số dòng trả về).
    -   **Slow Index:** Chậm đi rất nhiều (tuyến tính theo kích thước Section quét qua).

### Bài học về Execution Plan:
Hai query trên có thể đều hiển thị là `INDEX RANGE SCAN` và Cost không chênh lệch quá nhiều trên tập dữ liệu nhỏ.

> ⚠️ **Key Indicator:** Hãy nhìn vào **Predicate Information**.

-   **Access Predicate:** Điều kiện dùng để đi tuần tự trên Index Tree (Start & Stop key). Đây là phần hiệu quả nhất.
-   **Filter Predicate:** Điều kiện dùng để lọc lại dữ liệu **sau khi** đã đọc từ Index/Table. Đây là "quả bom nổ chậm" (unexploded ordnance).
    -   Với `Slow Index`, `id2` trở thành Filter Predicate. DB phải đọc hết tất cả index entry của `section` rồi mới lọc `id2`. Khi `section` phình to, hiêu năng giảm thê thảm.

---

## 3. Tác động của System Load (Tải hệ thống)

Vấn đề của "Slow Index" sẽ trở nên tồi tệ hơn khi có nhiều User dùng đồng thời.

-   Query chạy chậm -> giữ tài nguyên lâu (CPU, I/O latch).
-   Khi có nhiều query chậm chạy cùng lúc -> tranh chấp tài nguyên -> hệ thống bị tắc nghẽn.
-   **Fast Index** xử lý nhanh gọn -> giải phóng tài nguyên sớm -> chịu tải tốt hơn nhiều.

> 💡 **Thực tế:** Môi trường Dev thường ít dữ liệu và ít user, nên những query "có vẻ ổn" ở Dev có thể giết chết hệ thống Production.

---

## 4. Response Time vs. Throughput

Có một ngộ nhận phổ biến: *"Máy chủ Production mạnh hơn nên query sẽ chạy nhanh hơn"*.

### Sự thật:
-   **Bigger Hardware ≈ Wider Highway (Đường rộng hơn):** Giúp nhiều xe đi cùng lúc hơn (**Throughput** tăng), nhưng không giúp một chiếc xe đi nhanh hơn (**Response Time** không giảm, thậm chí tăng do giới hạn tốc độ).
-   Tốc độ xử lý đơn nhân (Single-core speed) đã chững lại từ lâu. CPU ngày nay tăng số lượng Core, giúp xử lý song song (Throughput) chứ không làm một task đơn lẻ chạy nhanh hơn.

### Latency (Độ trễ):
Môi trường Production thường phức tạp hơn (Firewall, Network hops, SAN, Security layers...) -> **Latency** thường cao hơn môi trường Dev (Localhost).
-   Nếu query chưa tối ưu (quét nhiều data, nhiều round-trip), độ trễ tích lũy sẽ làm nó chậm hơn hẳn so với chạy ở Dev.

### SSD & Caching:
-   **SSD:** Giảm thời gian Random I/O đáng kể (seek time).
-   **Caching:** Giảm I/O đĩa bằng cách lưu dữ liệu trên RAM.
-   Tuy nhiên, chúng chỉ giảm thiểu tác động, không giải quyết gốc rễ vấn đề thuật toán truy xuất. **Proper Indexing** vẫn là cách tốt nhất để giảm Response Time.

---

## 5. Deep Dive: Thảo luận mở rộng

Các kiến thức bổ sung từ quá trình phân tích chi tiết cơ chế phần cứng:

### 5.1. Cơ chế thực thi của một Slow Index
Tại sao Index sai thứ tự cột `(section, id1, id2)` lại chậm khi tìm `section=? AND id2=?`?

1.  **Vẫn dùng Index (Tầng 1):** Database vẫn dùng B-Tree để **Seek** đến dòng đầu tiên của `section`.
2.  **Phải Scan toàn bộ (Tầng 2):** Do `id1` nằm giữa làm `id2` lộn xộn, DB không thể tìm `id2` bằng cây. Nó buộc phải **Scan** (đi bộ) qua toàn bộ dải `section` đó trong Index Leaf Nodes.
3.  **Tốn CPU khủng khiếp:** Với mỗi dòng trong dải `section` (có thể là hàng triệu dòng), CPU phải thực hiện phép so sánh `if (row.id2 == value)`. Việc này chiếm dụng Core và gây tắc nghẽn cục bộ.

> **Index Range Scan = Seek (Tìm - nhanh) + Scan (Duyệt - có thể chậm).**

### 5.2. Vai trò của các linh kiện (Mô hình Bếp ăn)
-   **CPU (Đầu bếp):** Chịu trách nhiệm Parsing, Optimization, và quan trọng nhất là **So sánh dữ liệu (logic)**. Slow query giết chết CPU vì bắt nó so sánh quá nhiều rác (Filter Predicate).
-   **RAM (Bàn bếp):** Nơi chứa dữ liệu để CPU làm việc. Tốc độ RAM nhanh hơn NVMe hàng nghìn lần. Database luôn cố gắng Cache mọi thứ vào RAM.
-   **Disk (Tủ lạnh):** Nơi lưu trữ bền vững. Chịu trách nhiệm I/O.
    -   **HDD:** Cơ học, sợ nhất là Random I/O (Seek time cao). Ưu tiên đọc tuần tự.
    -   **NVMe:** Điện tử, chịu tải song song cực tốt (64k queues vs 1 queue của HDD). Giúp giảm bớt nỗi lo về Random I/O nhưng **vẫn chậm hơn RAM rất nhiều**.

### 5.3. Throughput vs Latency trong phần cứng
| Linh kiện    | Response Time (Độ trễ/Tốc độ đơn) | Throughput (Thông lượng/Sức tải)          |
| :----------- | :-------------------------------- | :---------------------------------------- |
| **CPU**      | Xung nhịp (GHz) - Tốc độ 1 nhân   | Số nhân (Cores) - Làm nhiều việc cùng lúc |
| **Disk**     | Seek Time (ms) - Thời gian tìm    | Tốc độ đọc/ghi (MB/s) - Độ rộng ống nước  |
| **Network**  | Ping (ms) - Tốc độ phản hồi       | Bandwidth (Mbps) - Dung lượng tải         |
| **Đường xá** | Tốc độ tối đa (km/h)              | Số làn xe (Lanes)                         |

> **Kết luận:** Nâng cấp phần cứng (Thêm Core, thêm RAM, SSD xịn) là mở rộng con đường (Tăng Throughput) để phục vụ nhiều người hơn (xử lý tình trạng "kẹt xe"), chứ không làm cho một chiếc xe (Query tồi) chạy nhanh hơn giới hạn vật lý của nó (Response Time).

### 5.4. Phân vai: Ai làm gì trong Seek và Scan?
Sự phối hợp giữa Đĩa và CPU thay đổi tùy theo từng bước:

| Hành động                                        | Đĩa (Disk) làm gì?                                                                    | CPU làm gì?                                                                                      | Bottleneck ở đâu?             |
| :----------------------------------------------- | :------------------------------------------------------------------------------------ | :----------------------------------------------------------------------------------------------- | :---------------------------- |
| **1. Tìm (Seek)** <br>*(Nhảy cóc)*               | **Random I/O**: Nhảy từ Root -> Branch -> Leaf. <br> HDD rất sợ bước này (Seek time). | **Chỉ đường**: So sánh logic để biết đi nhánh nào. (Rất nhẹ).                                    | **Độ trễ Đĩa (Disk Latency)** |
| **2. Duyệt (Scan) có Filter** <br>*(Slow Index)* | **Sequential I/O**: Bơm liên tục hàng triệu dòng dữ liệu lên RAM.                     | **Lọc rác**: Phải kiểm tra từng dòng, so sánh điều kiện `WHERE` để loại bỏ dòng sai. (Rất nặng). | **Sức mạnh CPU**              |
| **3. Duyệt (Scan) chuẩn** <br>*(Fast Index)*     | **Sequential I/O**: Chỉ bơm đúng (hoặc rất ít) dữ liệu cần thiết.                     | **Nhận hàng**: Gần như không phải xử lý gì, nhận xong trả về Client luôn. (Rất nhẹ).             | **Không có! (Tối ưu)**        |

> **Ví dụ thực tế:**
> - **Slow Index:** Vào kho lấy 1 triệu hộp hàng, mở từng hộp ra xem có phải iPhone không. (Tốn công mở hộp - CPU).
> - **Fast Index:** Đến đúng kệ số 5, bốc 1 hộp hàng đưa cho khách. (Không tốn công kiểm tra - CPU rảnh tay).

---

## ✅ Key Takeaways

1.  **Scalability:** Là cách hiệu năng hệ thống thay đổi khi dữ liệu hoặc tải tăng lên. Mục tiêu là giữ độ dốc của đồ thị hiệu năng càng thấp càng tốt.
2.  **Filter Predicate là kẻ thù:** Trong Index Range Scan, hãy đảm bảo các cột quan trọng nằm trong **Access Predicate**. Nếu thấy Filter Predicate trên cột lẽ ra phải nằm trong index, hãy kiểm tra lại thứ tự cột trong Index.
3.  **Hardware không cứu được Bad Query:**
    -   Phần cứng mạnh giúp tăng **Thông lượng (Throughput)** - phục vụ nhiều user hơn.
    -   Để giảm **Thời gian phản hồi (Response Time)** cho một query, bạn cần **Indexing** đúng.
4.  **Dev vs Prod:** Đừng tin vào benchmark "thấy chạy nhanh" ở máy local. Hãy tin vào **Execution Plan** và phân tích độ phức tạp của nó (Access vs Filter).
5.  **Storage Hierarchy:** CPU > RAM >>> NVMe > HDD. Tối ưu Query là giúp CPU tìm thấy dữ liệu ngay trên RAM (Cache Hit) và hạn chế phải xuống Disk.
