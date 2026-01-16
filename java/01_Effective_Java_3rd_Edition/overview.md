# Effective Java 3rd Edition - Overview

## 📖 Thông tin sách

| Thông tin        | Chi tiết           |
| ---------------- | ------------------ |
| **Tác giả**      | Joshua Bloch       |
| **Xuất bản**     | 2018 (3rd Edition) |
| **Số trang**     | ~400 trang         |
| **Số items**     | 90 items (rules)   |
| **Java version** | Java 7, 8, 9       |

## 🎯 Mục đích

Cuốn sách này dạy bạn **cách sử dụng Java một cách hiệu quả** - không phải cú pháp mà là **best practices**, **design patterns**, và **idioms** mà các lập trình viên giỏi nhất sử dụng.

> "This book is my attempt to share my experience with you so that you can imitate my successes while avoiding my failures." - Joshua Bloch

## 👤 Về tác giả

Joshua Bloch là:
- Cựu Java Platform Libraries Architect tại Sun Microsystems
- Người thiết kế nhiều Java APIs quan trọng (Collections Framework)
- Hiện làm việc tại Google
- Đồng tác giả Java Language Specification

---

## 📚 Cấu trúc sách - 12 Chapters, 90 Items

### Chapter 1: Introduction
Giới thiệu về sách và cách đọc.

---

### Chapter 2: Creating and Destroying Objects (Items 1-9)

| Item | Nội dung                      | Best Practice                           |
| ---- | ----------------------------- | --------------------------------------- |
| 1    | Static factory methods        | Ưu tiên hơn constructors                |
| 2    | Builder pattern               | Khi có nhiều constructor parameters     |
| 3    | Singleton                     | Dùng private constructor hoặc enum      |
| 4    | Noninstantiability            | Private constructor cho utility classes |
| 5    | Dependency Injection          | Ưu tiên hơn hardwiring resources        |
| 6    | Avoid unnecessary objects     | Reuse thay vì tạo mới                   |
| 7    | Eliminate obsolete references | Tránh memory leaks                      |
| 8    | Avoid finalizers and cleaners | Dùng try-with-resources                 |
| 9    | try-with-resources            | Ưu tiên hơn try-finally                 |

---

### Chapter 3: Methods Common to All Objects (Items 10-14)

| Item | Nội dung             | Best Practice               |
| ---- | -------------------- | --------------------------- |
| 10   | Override equals()    | Tuân thủ general contract   |
| 11   | Override hashCode()  | Luôn override cùng equals() |
| 12   | Override toString()  | Luôn override cho debugging |
| 13   | Override clone()     | Cẩn thận khi implement      |
| 14   | Implement Comparable | Cân nhắc cho value classes  |

---

### Chapter 4: Classes and Interfaces (Items 15-25)

| Item | Nội dung                        | Best Practice                  |
| ---- | ------------------------------- | ------------------------------ |
| 15   | Minimize accessibility          | Encapsulation                  |
| 16   | Accessor methods                | Không dùng public fields       |
| 17   | Minimize mutability             | Immutable classes              |
| 18   | Composition over inheritance    | Ưu tiên composition            |
| 19   | Design for inheritance          | Hoặc cấm nó                    |
| 20   | Interfaces vs abstract classes  | Ưu tiên interfaces             |
| 21   | Design interfaces for posterity | Default methods                |
| 22   | Interfaces only for types       | Không dùng constant interfaces |
| 23   | Class hierarchies               | Thay vì tagged classes         |
| 24   | Static member classes           | Hơn nonstatic                  |
| 25   | Single top-level class per file | Limit source files             |

---

### Chapter 5: Generics (Items 26-33)

| Item | Nội dung                          | Best Practice                          |
| ---- | --------------------------------- | -------------------------------------- |
| 26   | Don't use raw types               | `List<String>` không phải `List`       |
| 27   | Eliminate unchecked warnings      | @SuppressWarnings                      |
| 28   | Lists vs arrays                   | Ưu tiên lists                          |
| 29   | Generic types                     | Favor them                             |
| 30   | Generic methods                   | Favor them                             |
| 31   | Bounded wildcards                 | PECS: Producer-Extends, Consumer-Super |
| 32   | Generics + varargs                | @SafeVarargs                           |
| 33   | Typesafe heterogeneous containers | Type tokens                            |

---

### Chapter 6: Enums and Annotations (Items 34-41)

| Item | Nội dung               | Best Practice               |
| ---- | ---------------------- | --------------------------- |
| 34   | Enums vs int constants | Luôn dùng enums             |
| 35   | Instance fields        | Không dùng ordinals         |
| 36   | EnumSet                | Thay vì bit fields          |
| 37   | EnumMap                | Thay vì ordinal indexing    |
| 38   | Extensible enums       | Dùng interfaces             |
| 39   | Annotations            | Ưu tiên hơn naming patterns |
| 40   | @Override              | Luôn sử dụng                |
| 41   | Marker interfaces      | Định nghĩa types            |

---

### Chapter 7: Lambdas and Streams ⭐ (Items 42-48)

> **Đây là chapter mới trong 3rd Edition, cover Java 8 features**

| Item | Nội dung                         | Best Practice                           |
| ---- | -------------------------------- | --------------------------------------- |
| 42   | Lambdas                          | Ưu tiên hơn anonymous classes           |
| 43   | Method references                | Ưu tiên hơn lambdas                     |
| 44   | Standard functional interfaces   | Consumer, Function, Predicate, Supplier |
| 45   | Streams                          | Dùng đúng cách (judiciously)            |
| 46   | Side-effect-free functions       | Pure functions trong streams            |
| 47   | Collection vs Stream return type | Ưu tiên Collection                      |
| 48   | Parallel streams                 | Cẩn thận khi sử dụng                    |

---

### Chapter 8: Methods (Items 49-56)

| Item | Nội dung                  | Best Practice           |
| ---- | ------------------------- | ----------------------- |
| 49   | Check parameters validity | Fail fast               |
| 50   | Defensive copies          | Khi cần thiết           |
| 51   | Method signatures         | Design cẩn thận         |
| 52   | Overloading               | Dùng đúng cách          |
| 53   | Varargs                   | Dùng đúng cách          |
| 54   | Return empty collections  | Không return null       |
| 55   | Return optionals          | Optional<T> (Java 8)    |
| 56   | Write doc comments        | JavaDoc cho public APIs |

---

### Chapter 9: General Programming (Items 57-68)

| Item | Nội dung                 | Best Practice             |
| ---- | ------------------------ | ------------------------- |
| 57   | Local variables scope    | Minimize                  |
| 58   | for-each loops           | Ưu tiên hơn for loops     |
| 59   | Use libraries            | java.util, java.io        |
| 60   | Avoid float/double       | Cho exact answers         |
| 61   | Primitives vs boxed      | Ưu tiên primitives        |
| 62   | Avoid strings            | Khi có types tốt hơn      |
| 63   | String concatenation     | Dùng StringBuilder        |
| 64   | Refer by interfaces      | Không phải implementation |
| 65   | Interfaces vs reflection | Ưu tiên interfaces        |
| 66   | Native methods           | Dùng đúng cách            |
| 67   | Optimize judiciously     | Đừng optimize sớm         |
| 68   | Naming conventions       | Tuân thủ                  |

---

### Chapter 10: Exceptions (Items 69-77)

| Item | Nội dung                              | Best Practice                  |
| ---- | ------------------------------------- | ------------------------------ |
| 69   | Exceptions for exceptional conditions | Không dùng cho control flow    |
| 70   | Checked vs runtime exceptions         | Chọn đúng loại                 |
| 71   | Avoid checked exceptions              | Khi không cần thiết            |
| 72   | Standard exceptions                   | IllegalArgumentException, etc. |
| 73   | Appropriate abstraction               | Exception translation          |
| 74   | Document exceptions                   | JavaDoc                        |
| 75   | Include failure info                  | Detail messages                |
| 76   | Failure atomicity                     | Roll back on failure           |
| 77   | Don't ignore exceptions               | Xử lý hoặc log                 |

---

### Chapter 11: Concurrency (Items 78-84)

| Item | Nội dung                         | Best Practice          |
| ---- | -------------------------------- | ---------------------- |
| 78   | Synchronize shared mutable data  | volatile, synchronized |
| 79   | Avoid excessive synchronization  | Deadlock, performance  |
| 80   | Executors, tasks, streams        | Thay vì raw threads    |
| 81   | Concurrency utilities            | java.util.concurrent   |
| 82   | Document thread safety           | @ThreadSafe            |
| 83   | Lazy initialization              | Double-check locking   |
| 84   | Don't depend on thread scheduler | Portability            |

---

### Chapter 12: Serialization (Items 85-90)

| Item | Nội dung                         | Best Practice          |
| ---- | -------------------------------- | ---------------------- |
| 85   | Alternatives to serialization    | JSON, Protocol Buffers |
| 86   | Implement Serializable carefully | Great caution          |
| 87   | Custom serialized form           | Khi cần                |
| 88   | Defensive readObject             | Security               |
| 89   | Enum for instance control        | Thay vì readResolve    |
| 90   | Serialization proxies            | Pattern                |

---

## 🔥 Top 10 Items quan trọng nhất

1. **Item 1**: Static factory methods
2. **Item 2**: Builder pattern
3. **Item 17**: Minimize mutability
4. **Item 18**: Composition over inheritance
5. **Item 45**: Use streams judiciously
6. **Item 49**: Check parameters for validity
7. **Item 54**: Return empty collections, not nulls
8. **Item 55**: Return optionals judiciously
9. **Item 78**: Synchronize shared mutable data
10. **Item 85**: Prefer alternatives to Java serialization

---

## 📋 Java 8+ Features trong sách

| Feature                   | Items | Mô tả                      |
| ------------------------- | ----- | -------------------------- |
| **Lambdas**               | 42-44 | Anonymous functions        |
| **Streams**               | 45-48 | Functional data processing |
| **Optional**              | 55    | Null safety                |
| **Default methods**       | 21    | Interface evolution        |
| **Method references**     | 43    | `Class::method`            |
| **Functional interfaces** | 44    | @FunctionalInterface       |

---

## 💡 Cách đọc hiệu quả

1. **Không cần đọc từ đầu đến cuối** - Mỗi item độc lập
2. **Đọc theo nhu cầu** - Gặp vấn đề gì thì đọc item đó
3. **Thực hành ngay** - Áp dụng vào code hiện tại
4. **Đọc lại** - Mỗi lần đọc lại sẽ hiểu sâu hơn

---

## 📝 Ghi chú học tập

> Phần này để ghi chú trong quá trình học

- [ ] Chapter 2: Creating and Destroying Objects
- [ ] Chapter 3: Methods Common to All Objects
- [ ] Chapter 4: Classes and Interfaces
- [ ] Chapter 5: Generics
- [ ] Chapter 6: Enums and Annotations
- [ ] Chapter 7: Lambdas and Streams
- [ ] Chapter 8: Methods
- [ ] Chapter 9: General Programming
- [ ] Chapter 10: Exceptions
- [ ] Chapter 11: Concurrency
- [ ] Chapter 12: Serialization
