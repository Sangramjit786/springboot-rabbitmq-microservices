# Spring Boot RabbitMQ Microservices

This project demonstrates an **event-driven microservices architecture** using **Spring Boot** and **RabbitMQ** as the message broker.  
It consists of **three microservices** working together to process orders asynchronously:  
- **OrderService** → Accepts orders & publishes events  
- **StockService** → Consumes order events & validates stock  
- **EmailService** → Consumes email events & simulates notifications  

---

## Event-Driven Microservices Project Architecture Flow:
<img width="997" height="506" alt="image" src="https://github.com/user-attachments/assets/c5848f43-8e4c-4cad-9bae-4a82d8edd083" />

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
```java
rabbitTemplate.convertAndSend(exchange, routingKey, message);
```

**Best Practice:**
Producer logic is decoupled from controller → easier testing & refactoring.

---

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

---

### 6) StockService – Configure RabbitMQ

**Objective:**
Consume messages from order.queue.

**Configuration:**
- Same RabbitMQ connection settings.
- Defined beans for Queue, Exchange, and Binding.

**Key Point:**
Consumers should be independently configurable.

---

### 7) StockService – Create RabbitMQ Consumer

**Objective:**
Validate stock based on received order events.

**Implementation:**
- Annotated method with:
```java
@RabbitListener(queues = "order.queue")
```
- Parsed JSON/POJO message.
- Simulated stock check logic.

**Professional Emphasis:**
Stress idempotency, error handling, and retry mechanisms during interviews.

---

### 8) OrderService – Configure Email Queue and Binding

**Objective:**
Allow OrderService to emit events to email.queue.

**Implementation:**
- Defined a second queue email.queue.
- Added binding with routing key (e.g., email.routing.key).
- Bound to exchange (same or new).

**Rationale:**
Supports multi-routing → one event can trigger multiple services.

---

### 9) OrderService – Send Event to Email Queue

**Objective:**
Emit a notification event when order is placed.

**Steps:**
- Updated producer to send messages to:
  1. order.queue
  2. email.queue

**Professional Insight:**
Handles cross-cutting concerns like notifications in an asynchronous way.

---

### 10) EmailService – Configure RabbitMQ and Create Consumer

**Objective:**
Consume email messages and simulate notification sending.

**Implementation:**
- Configured email.queue in properties.
- Added @RabbitListener(queues = "email.queue").
- Mocked logic → sendEmail(order.getEmail()).

**Best Practice:**
Messaging concerns stay modular, keeping side-effects (emails) isolated.

---

### 11) Run 3 Microservices and Have a Demo

**Objective:**
Show the end-to-end workflow.

**Demo Flow:**
  1. Start RabbitMQ broker.
  2. Start OrderService, StockService, EmailService.
  3. Send POST /api/orders request to OrderService.
  4. Verify flow:
      - StockService consumes → stock validated.
      - EmailService consumes → email logged/sent.

**Result:**
Validates a working event-driven microservice architecture.

## 🛠️ How to Run

**1. Start RabbitMQ locally (via Docker):**
```bash
docker run -d --hostname my-rabbit --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```

**2. Clone repo:**
```bash
git clone https://github.com/Sangramjit786/springboot-rabbitmq-microservices.git
cd springboot-rabbitmq-microservices
```

**3. Run each microservice:**
```bash
cd OrderService && mvn spring-boot:run
cd StockService && mvn spring-boot:run
cd EmailService && mvn spring-boot:run
```

**4. Test:**
```http
POST http://localhost:8080/api/orders
Content-Type: application/json

{
  "orderId": "101",
  "product": "Laptop",
  "quantity": 1,
  "email": "customer@example.com"
}
```

## ✅ Summary

This project illustrates a production-ready microservice architecture with:

- Event-driven design using RabbitMQ
- Spring Boot for microservices
- REST APIs + Messaging integration
- Decoupling, scalability, and reliability

It demonstrates real-world proficiency in RabbitMQ configuration, message publishing/consuming, and distributed system design.

## 🔗 References

- Spring AMQP Documentation
- RabbitMQ Official Docs
- Spring Boot Documentation
