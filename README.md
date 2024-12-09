## WebSockets Explained


---

## What is WebSocket?
WebSocket is a protocol designed for **real-time, bi-directional communication** between clients (e.g., web browsers) and servers. It uses a single **TCP connection** to enable both parties to send and receive data simultaneously, making it efficient for interactive and real-time applications.

<img width="560" alt="image" src="https://github.com/user-attachments/assets/9d07a329-fd35-4cf6-8d00-44df63e36583">

---

## Protocols Comparison

### Standard HTTP
- A client sends a request to the server.
- The server processes the request and sends back a response.
- The connection is closed after every transaction.
- **Use Case**: Fetching static or infrequent data.

### Polling
- The client sends repeated requests at regular intervals to check for updates.
- If no new data is available, the server sends empty responses.
- **Drawback**: High resource usage and HTTP overhead due to frequent connections.

### HTTP Long Polling
- The server holds the client’s request until new data is available or a timeout occurs.
- Reduces empty responses but still involves periodic reconnections.
- **Use Case**: Slightly better for near-real-time updates but resource-intensive.

### WebSockets
- A single persistent connection allows bi-directional communication.
- Both client and server can send and receive data independently and simultaneously.
- Eliminates the latency and overhead of repeated connections in long polling.
- **Use Case**: Real-time applications with continuous data exchange.

---

## How WebSockets Work

1. **Handshake**:
    - The client initiates a WebSocket handshake using the HTTP protocol.
    - If the server supports WebSockets, it responds with an appropriate header to upgrade the connection.

2. **Persistent Connection**:
    - Once the handshake is complete, the connection remains open.
    - Both parties can stream messages to each other over the same TCP connection.

3. **Messaging**:
    - Messages are sent asynchronously in a full-duplex manner.
    - A WebSocket handler manages active connections for lightweight and efficient communication.

---

## Applications of WebSockets

### Real-Time Applications
- **Stock Trading Platforms**: Continuously display fluctuating stock prices.
- **Chat Applications**: Enable message exchange over a single persistent connection.
- **Gaming**: Provide seamless real-time interactions for multiplayer games.

### When Not to Use WebSockets
- For fetching static, historical, or one-time data.
- In scenarios where frequent updates are unnecessary; use HTTP requests instead.

---

## Advantages of WebSockets
- **Low Latency**: Instantaneous communication without the overhead of frequent reconnections.
- **Resource Efficiency**: Maintains a single connection for continuous communication.
- **Real-Time Updates**: Ideal for applications requiring live data streaming.

---

## Limitations of WebSockets
- Overkill for static or infrequently updated data.
- Not suitable for all use cases where HTTP suffices.

---

## Conclusion

WebSockets provide a powerful solution for real-time communication, reducing latency and overhead compared to traditional HTTP and long polling methods. However, they should be used judiciously based on the specific application requirements.

---

## Spring Boot WebSocket Chat Application with DragonflyDB

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
- **Docker Compose** (for DragonflyDB setup)- **Thymeleaf** (for simple UI)

---

## Architecture Overview
1. **Single Server**:
    - WebSocket facilitates direct real-time communication between clients and the server.
2. **Multiple Servers**:
    - DragonflyDB enables synchronization across multiple server instances using its Pub/Sub mechanism.
  
<img width="782" alt="image" src="https://github.com/user-attachments/assets/fef4ddfa-fcd7-4bd3-a0aa-6f1aff93a347">

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
