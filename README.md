# Spring Boot RabbitMQ Microservices

This project demonstrates an **event-driven microservices architecture** using **Spring Boot** and **RabbitMQ** as the message broker.  
It consists of **three microservices** working together to process orders asynchronously:  
- **OrderService** → Accepts orders & publishes events  
- **StockService** → Consumes order events & validates stock  
- **EmailService** → Consumes email events & simulates notifications  

---

## 📂 Microservices Overview

### 1) Create 3 Microservices – OrderService, StockService, and EmailService
**Objective:**  
Design and implement an event-driven system for order processing using RabbitMQ.  

- **OrderService:** Accepts REST requests and produces RabbitMQ events.  
- **StockService:** Listens to order events and validates stock availability.  
- **EmailService:** Sends email notifications after an order is placed or fails.  

**Professional Note:**  
Each service follows **Separation of Concerns**, enabling **independent scalability** and **resilience**.

---

### 2) Import and Setup 3 Microservices in IntelliJ IDEA
**Objective:**  
Enable parallel development and debugging by importing all microservices in one IDE workspace.  

**Steps Taken:**  
- Created three independent **Spring Boot projects** with **Maven**.  
- Added dependencies:  
  - `spring-boot-starter-web`  
  - `spring-boot-starter-amqp`  
  - `spring-boot-devtools`  
- Imported all as **Maven modules** in IntelliJ IDEA.  
- Configured **application.properties** and unique **ports** for each service.  

**Interview Tip:**  
Highlight modular setup → independent lifecycles improve maintainability.

---

### 3) OrderService – Configure RabbitMQ
**Objective:**  
Connect OrderService with RabbitMQ.  

**Configuration (`application.properties`):**
```properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```

**Defined Beans:**

- DirectExchange
- Queue (e.g., order.queue)
- Binding (queue ↔ exchange with routing key)

**Professional Insight:**
Demonstrates broker configuration & message routing using Spring AMQP.

---

### 4) OrderService – Create RabbitMQ Producer

**Objective:**
Publish order messages to RabbitMQ.

**Implementation:**
- Used RabbitTemplate.
- Created OrderEventProducer with:
```
rabbitTemplate.convertAndSend(exchange, routingKey, message);
```

**Best Practice:**
Producer logic is decoupled from controller → easier testing & refactoring.

### 5) OrderService – Create REST API to Send Message

**Objective:**
Expose an endpoint to accept orders and trigger RabbitMQ events.

**Steps:**
- Created @RestController with POST /api/orders.
- Accepted JSON order request.
- Passed it to producer.
- Tested with Postman.

**Technical Insight:**
Synchronous REST → Asynchronous messaging → better decoupling.

### 6) StockService – Configure RabbitMQ

**Objective:**
Consume messages from order.queue.

**Configuration:**
- Same RabbitMQ connection settings.
- Defined beans for Queue, Exchange, and Binding.

**Key Point:**
Consumers should be independently configurable.
