# Spring in Action 6th Edition - Overview

## 📖 Thông tin sách

| Thông tin          | Chi tiết                  |
| ------------------ | ------------------------- |
| **Tác giả**        | Craig Walls               |
| **Xuất bản**       | 2022 (6th Edition)        |
| **Nhà xuất bản**   | Manning                   |
| **Số trang**       | ~500 trang                |
| **Spring version** | Spring Boot 2.x, Spring 5 |

## 🎯 Mục đích

Cuốn sách này là **sách tham khảo chính thức** cho Spring Framework, dạy bạn cách xây dựng ứng dụng từ cơ bản đến cloud-native với đầy đủ các module của Spring ecosystem.

> "The definitive guide to building cloud native applications using Spring." - David Witherspoon

---

## 📚 Cấu trúc sách - 4 Parts, 18 Chapters

---

## PART 1: FOUNDATIONAL SPRING (Chapters 1-6)
*Nền tảng Spring - Từ zero đến web application*

### Chapter 1: Getting Started with Spring (Trang 3-28)

| Section | Nội dung                                                        |
| ------- | --------------------------------------------------------------- |
| 1.1     | What is Spring?                                                 |
| 1.2     | Initializing a Spring application (Spring Initializr)           |
| 1.3     | Writing a Spring application (Controller, View, Testing)        |
| 1.4     | Surveying the Spring landscape (Boot, Data, Security, Cloud...) |

**Key concepts**: Spring Boot, Spring Initializr, DevTools, Auto-configuration

---

### Chapter 2: Developing Web Applications (Trang 29-60)

| Section | Nội dung                                                    |
| ------- | ----------------------------------------------------------- |
| 2.1     | Displaying information (Domain, Controller, View)           |
| 2.2     | Processing form submission                                  |
| 2.3     | Validating form input (@Valid, @NotNull, @Size...)          |
| 2.4     | Working with view controllers                               |
| 2.5     | Choosing a view template library (Thymeleaf, FreeMarker...) |

**Key concepts**: Spring MVC, Thymeleaf, Form validation, @Controller, @GetMapping, @PostMapping

---

### Chapter 3: Working with Data (Trang 61-93)

| Section | Nội dung                                          |
| ------- | ------------------------------------------------- |
| 3.1     | Reading and writing data with JDBC (JdbcTemplate) |
| 3.2     | Working with Spring Data JDBC                     |
| 3.3     | Persisting data with Spring Data JPA              |

**Key concepts**: JdbcTemplate, Spring Data JDBC, JPA, @Repository, CrudRepository, Custom queries

---

### Chapter 4: Working with Nonrelational Data (Trang 94-112)

| Section | Nội dung                            |
| ------- | ----------------------------------- |
| 4.1     | Working with Cassandra repositories |
| 4.2     | Writing MongoDB repositories        |

**Key concepts**: NoSQL, Cassandra, MongoDB, Spring Data Cassandra, Spring Data MongoDB

---

### Chapter 5: Securing Spring (Trang 113-139)

| Section | Nội dung                                                   |
| ------- | ---------------------------------------------------------- |
| 5.1     | Enabling Spring Security                                   |
| 5.2     | Configuring authentication (In-memory, Custom UserDetails) |
| 5.3     | Securing web requests (URL authorization, Login page)      |
| 5.4     | Applying method-level security (@PreAuthorize, @Secured)   |
| 5.5     | Knowing your user (Principal, @AuthenticationPrincipal)    |

**Key concepts**: Spring Security, Authentication, Authorization, CSRF, OAuth2, UserDetailsService

---

### Chapter 6: Working with Configuration Properties (Trang 140-160)

| Section | Nội dung                                                              |
| ------- | --------------------------------------------------------------------- |
| 6.1     | Fine-tuning autoconfiguration (DataSource, Server, Logging)           |
| 6.2     | Creating your own configuration properties (@ConfigurationProperties) |
| 6.3     | Configuring with profiles (@Profile, application-{profile}.yml)       |

**Key concepts**: application.properties/yml, @ConfigurationProperties, Profiles, Environment abstraction

---

## PART 2: INTEGRATED SPRING (Chapters 7-10)
*Tích hợp Spring với các services khác*

### Chapter 7: Creating REST Services (Trang 163-185)

| Section | Nội dung                                                      |
| ------- | ------------------------------------------------------------- |
| 7.1     | Writing RESTful controllers (@RestController, ResponseEntity) |
| 7.2     | Enabling data-backed services (Spring Data REST)              |
| 7.3     | Consuming REST services (RestTemplate)                        |

**Key concepts**: REST API, @RestController, @RequestBody, @PathVariable, HATEOAS, RestTemplate

---

### Chapter 8: Securing REST (Trang 186-209)

| Section | Nội dung                               |
| ------- | -------------------------------------- |
| 8.1     | Introducing OAuth 2                    |
| 8.2     | Creating an authorization server       |
| 8.3     | Securing an API with a resource server |
| 8.4     | Developing the client                  |

**Key concepts**: OAuth2, JWT, Authorization Server, Resource Server, Access Token, Refresh Token

---

### Chapter 9: Sending Messages Asynchronously (Trang 210-242)

| Section | Nội dung                                              |
| ------- | ----------------------------------------------------- |
| 9.1     | Sending messages with JMS (JmsTemplate, @JmsListener) |
| 9.2     | Working with RabbitMQ and AMQP                        |
| 9.3     | Messaging with Kafka (KafkaTemplate, @KafkaListener)  |

**Key concepts**: JMS, RabbitMQ, Apache Kafka, Asynchronous messaging, Message-driven architecture

---

### Chapter 10: Integrating Spring (Trang 243-276)

| Section | Nội dung                                                                              |
| ------- | ------------------------------------------------------------------------------------- |
| 10.1    | Declaring a simple integration flow                                                   |
| 10.2    | Surveying the Spring Integration landscape (Channels, Filters, Transformers, Routers) |
| 10.3    | Creating an email integration flow                                                    |

**Key concepts**: Spring Integration, Enterprise Integration Patterns, Message channels, Gateways

---

## PART 3: REACTIVE SPRING (Chapters 11-14)
*Lập trình reactive với Spring WebFlux*

### Chapter 11: Introducing Reactor (Trang 279-307)

| Section | Nội dung                                                           |
| ------- | ------------------------------------------------------------------ |
| 11.1    | Understanding reactive programming                                 |
| 11.2    | Getting started with Reactor (Mono, Flux)                          |
| 11.3    | Applying common reactive operations (map, flatMap, filter, zip...) |

**Key concepts**: Reactive Streams, Project Reactor, Mono, Flux, Backpressure, Non-blocking

---

### Chapter 12: Developing Reactive APIs (Trang 308-336)

| Section | Nội dung                                              |
| ------- | ----------------------------------------------------- |
| 12.1    | Working with Spring WebFlux                           |
| 12.2    | Defining functional request handlers (RouterFunction) |
| 12.3    | Testing reactive controllers (WebTestClient)          |
| 12.4    | Consuming REST APIs reactively (WebClient)            |
| 12.5    | Securing reactive web APIs                            |

**Key concepts**: WebFlux, WebClient, Functional endpoints, Reactive security

---

### Chapter 13: Persisting Data Reactively (Trang 337-368)

| Section | Nội dung                                                       |
| ------- | -------------------------------------------------------------- |
| 13.1    | Working with R2DBC (Reactive Relational Database Connectivity) |
| 13.2    | Persisting document data reactively with MongoDB               |
| 13.3    | Reactively persisting data in Cassandra                        |

**Key concepts**: R2DBC, Reactive MongoDB, Reactive Cassandra, ReactiveCrudRepository

---

### Chapter 14: Working with RSocket (Trang 369-384)

| Section | Nội dung                                    |
| ------- | ------------------------------------------- |
| 14.1    | Introducing RSocket                         |
| 14.2    | Creating a simple RSocket server and client |
| 14.3    | Transporting RSocket over WebSocket         |

**Key concepts**: RSocket, Request-Response, Request-Stream, Fire-and-Forget, Bidirectional

---

## PART 4: DEPLOYED SPRING (Chapters 15-18)
*Deploy và monitoring Spring applications*

### Chapter 15: Working with Spring Boot Actuator (Trang 387-422)

| Section | Nội dung                                                 |
| ------- | -------------------------------------------------------- |
| 15.1    | Introducing Actuator (Health, Info, Metrics endpoints)   |
| 15.2    | Consuming Actuator endpoints                             |
| 15.3    | Customizing Actuator (Custom health, metrics, endpoints) |
| 15.4    | Securing Actuator                                        |

**Key concepts**: Spring Boot Actuator, Health checks, Metrics, /actuator endpoints

---

### Chapter 16: Administering Spring (Trang 423-434)

| Section | Nội dung                   |
| ------- | -------------------------- |
| 16.1    | Using Spring Boot Admin    |
| 16.2    | Exploring the Admin server |
| 16.3    | Securing the Admin server  |

**Key concepts**: Spring Boot Admin, Application monitoring, Admin UI

---

### Chapter 17: Monitoring Spring with JMX (Trang 435-442)

| Section | Nội dung                     |
| ------- | ---------------------------- |
| 17.1    | Working with Actuator MBeans |
| 17.2    | Creating your own MBeans     |
| 17.3    | Sending notifications        |

**Key concepts**: JMX (Java Management Extensions), MBeans, JConsole

---

### Chapter 18: Deploying Spring (Trang 443-458)

| Section | Nội dung                                       |
| ------- | ---------------------------------------------- |
| 18.1    | Weighing deployment options                    |
| 18.2    | Building executable JAR files                  |
| 18.3    | Building container images (Docker, Kubernetes) |
| 18.4    | Building and deploying WAR files               |
| 18.5    | The end is where we begin                      |

**Key concepts**: JAR deployment, Docker, Kubernetes, WAR, Graceful shutdown, Liveness/Readiness probes

---

## 🔥 Nội dung quan trọng nhất

### Cho Backend Developer:
| Priority | Chapter  | Lý do                                 |
| -------- | -------- | ------------------------------------- |
| ⭐⭐⭐      | Ch 3     | Spring Data JPA - cần cho mọi dự án   |
| ⭐⭐⭐      | Ch 5     | Spring Security - bảo mật là bắt buộc |
| ⭐⭐⭐      | Ch 7     | REST APIs - API development           |
| ⭐⭐       | Ch 8     | OAuth2 - authentication hiện đại      |
| ⭐⭐       | Ch 9     | Messaging - Kafka rất quan trọng      |
| ⭐        | Ch 11-12 | Reactive - xu hướng mới               |

### Cho DevOps/Cloud:
| Priority | Chapter | Lý do                    |
| -------- | ------- | ------------------------ |
| ⭐⭐⭐      | Ch 15   | Actuator - monitoring    |
| ⭐⭐⭐      | Ch 18   | Deployment - Docker, K8s |
| ⭐⭐       | Ch 6    | Configuration - profiles |

---

## 📋 So sánh với các versions trước

| Feature     | 5th Edition           | 6th Edition                 |
| ----------- | --------------------- | --------------------------- |
| Spring Boot | 2.0                   | 2.x (latest)                |
| OAuth2      | Spring Security OAuth | Spring Authorization Server |
| Reactive    | Giới thiệu            | Mở rộng (R2DBC, RSocket)    |
| Container   | Docker                | Docker + Kubernetes         |
| Native      | Không có              | Đề cập Spring Native        |

---

## 💡 Nội dung bản chất (Deep Understanding)

### Cuốn sách này giải thích:

1. **Auto-configuration hoạt động như thế nào?**
   - @Conditional annotations
   - spring.factories file
   - Starter dependencies

2. **Dependency Injection trong Spring**
   - @Autowired vs Constructor injection
   - Bean lifecycle
   - ApplicationContext

3. **Spring Security flow**
   - Filter chain
   - Authentication vs Authorization
   - Security context

4. **Reactive programming model**
   - Non-blocking I/O
   - Backpressure
   - Event loop vs Thread-per-request

---

## 📝 Ghi chú học tập

> Phần này để ghi chú trong quá trình học

### Part 1: Foundational Spring
- [ ] Chapter 1: Getting Started with Spring
- [ ] Chapter 2: Developing Web Applications
- [ ] Chapter 3: Working with Data
- [ ] Chapter 4: Working with Nonrelational Data
- [ ] Chapter 5: Securing Spring
- [ ] Chapter 6: Working with Configuration Properties

### Part 2: Integrated Spring
- [ ] Chapter 7: Creating REST Services
- [ ] Chapter 8: Securing REST
- [ ] Chapter 9: Sending Messages Asynchronously
- [ ] Chapter 10: Integrating Spring

### Part 3: Reactive Spring
- [ ] Chapter 11: Introducing Reactor
- [ ] Chapter 12: Developing Reactive APIs
- [ ] Chapter 13: Persisting Data Reactively
- [ ] Chapter 14: Working with RSocket

### Part 4: Deployed Spring
- [ ] Chapter 15: Working with Spring Boot Actuator
- [ ] Chapter 16: Administering Spring
- [ ] Chapter 17: Monitoring Spring with JMX
- [ ] Chapter 18: Deploying Spring
