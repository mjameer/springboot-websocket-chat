# Spring Boot WebSocket Chat Application with DragonflyDB

This project demonstrates a **real-time chat application** built using **Spring Boot WebSockets** and **DragonflyDB** for the Publish/Subscribe (Pub/Sub) architecture. 
DragonflyDB serves as a high-performance, drop-in replacement for Redis.

---

## Features
- Real-time bidirectional communication using **WebSockets**.
- Scalable message distribution using **DragonflyDB**.
- Supports multi-instance architecture.
- Integration with Spring Boot for simplicity and efficiency.

---

## Technologies Used
- **Spring Boot**
- **WebSocket with STOMP Protocol**
- **DragonflyDB (compatible with Redis APIs)**
- **SockJS** (fallback for WebSocket)
- **Docker Compose** (for DragonflyDB setup)
- **Thymeleaf** (for simple UI)

---

## Architecture Overview
1. **Single Server**:
    - WebSocket facilitates direct real-time communication between clients and the server.
2. **Multiple Servers**:
    - DragonflyDB enables synchronization across multiple server instances using its Pub/Sub mechanism.

---

## Project Structure
### 1. **WebSocket Configuration (`WebSocketConfig`)**
Configures WebSocket communication and defines message broker settings.

- Registers WebSocket endpoints (`/chat-app`) for client connections.
- Enables simple message broker for broadcasting messages to `/topic`.

---

### 2. **Chat Controller (`ChatController`)**
Handles WebSocket message flow.

- **Endpoints**:
    - `/chat.send`: Broadcasts chat messages.
    - `/chat.addUser`: Registers users and sends "user joined" notifications.

---

### 3. **Redis Configuration (`RedisConfig`)**
Configures Redis/DragonflyDB as the Pub/Sub mechanism.

- **Beans**:
    - `redisTemplate`: Manages serialization and publishing of messages.
    - `channelTopic`: Defines the channel (`chat`) for message distribution.
    - `redisMessageListenerContainer`: Subscribes to the `chat` topic.
    - `messageListenerAdapter`: Adapts the `RedisMessageSubscriber` for message listening.

---

### 4. **Redis Message Subscriber (`RedisMessageSubscriber`)**
Listens for messages on the Redis/DragonflyDB topic and broadcasts them via WebSocket.

- **Method**:
    - `onMessage`: Handles incoming messages, deserializes them, and sends them to all connected clients.

---

### 5. **WebSocket Event Listener (`WebSocketEventListener`)**
Handles WebSocket lifecycle events.

- Listens for user disconnections and broadcasts "user left" notifications.

---

## Running Application

- Run two of the Chat application by running below command

```text
mvn spring-boot:run -Dspring-boot.run.profiles=instance1
mvn spring-boot:run -Dspring-boot.run.profiles=instance2
```

- Note DragonflyDB is started via docker as we placed spring-boot-docker-compose in pom.xml
- Open Multiple Browser windows, some with http://localhost:8080 and http://localhost:8081
- Enter your username and send messages, the chat messages will be broadcasted to all the users

## Reference

- https://www.youtube.com/watch?v=pnj3Jbho5Ck
- https://www.youtube.com/watch?v=Cj7qqq9L1kU&t=2557s
