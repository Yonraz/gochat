# GoChat

## _Messaging app written in Go_

GoChat is a messaging application designed to explore Go as a backend language within a microservices architecture. This app is built with an **event-driven**, **microservices** architecture. All backend services are written in Go and utilize **RabbitMQ** for inter-service communication. The frontend is developed using **React** and **TypeScript**, with **Redux** for state management.

## Features

- JWT-based authentication and authorization, stored as cookies.
- View online users.
- Real-time WebSocket-based chat and notifications.
- Users, messages, and authentication data stored in PostgreSQL.
- Backend optimizations with Redis caching and pagination strategies.

## Technologies

- **Frontend:** React, TypeScript, Redux
- **Backend:** Go, Gin framework
- **Databases:** PostgreSQL
- **Microservices Communication:** RabbitMQ
- **Chat and Notifications:** Gorilla WebSocket
- **Caching:** Redis
- **Containerization and Orchestration:** Docker, Kubernetes, NGINX

### Notes

- Detailed implementation specifics for each microservice can be found in their respective `README.md` files.
- Diagrams specifying major features of the app are located in the **diagrams** folder.

## Architecture

The app is based on a microservices architecture, where each main feature has its own service and database. The microservices use a RabbitMQ messaging broker to maintain data consistency.

### RabbitMQ

I've worked with **Apache Kafka** for microservice communication before and enjoyed it. For this project, I chose RabbitMQ due to its scalability and lighter weight. Currently, the RabbitMQ broker holds two exchanges:

- **UserEventsExchange**
  - **Queues:**
    - UserLoggedIn
    - UserRegistered
    - UserSignedOut

- **MessageEventsExchange**
  - **Queues:**
    - MessageSent
    - MessageRead

These queues and exchanges enable separation of concerns between user events and chat events.

### Backend Services

#### Auth

- Responsible for signup/signin/signout operations by exposing endpoints for these actions.
- Encrypts passwords and sends back JWT as a cookie.
- Holds a PostgreSQL database with user data (email, username, and hashed password).
- Publishes login/logout/register events to RabbitMQ.
- Exposes a `currentUser` endpoint to help users validate themselves with their JWTs.



#### Users

- Manages user data and status (online/offline).
- Stores user data in a PostgreSQL database.
- Populates the database by consuming user events from RabbitMQ (published by Auth).
- Exposes a single endpoint to fetch existing users.
- Uses query caching with Redis for optimized query speed.

#### Auth/Users flow example:

![Online users flow](https://github.com/user-attachments/assets/03e028ef-9aa4-4b46-ac6b-149381c69d66)


#### Socket-Handler (Chat)

- Manages user-to-user chat messaging with the Gorilla WebSocket library.
- Handles message sending and status updates (message read).
- Publishes message events (message sent/updated).
- Facilitates user chat by creating chatrooms where only two users can send messages.

#### Notifications

- Pushes notifications to the client using a WebSocket connection (Gorilla).
- Listens for message events published by the socket-handler.
- Handles notifications regarding both messaging and user events.
- Connects to the client at the top level of the application, even if not signed in, to update the user list in real-time as users connect.
- Exposes endpoints to allow users to check notifications when they first connect and mark them as read.

#### Messages

- Holds message and conversation data in a PostgreSQL database.
- Handles message creation/update by consuming events published by the socket-handler.
- Exposes an endpoint to fetch messages by the client for a single conversation.
- _(Not yet implemented)_: Uses Redis caching for optimized message querying.

#### Message Send/Read flow Example:

![Message flow](https://github.com/user-attachments/assets/01ddaf9e-e022-4db3-836e-a32d49447b74)


## Frontend

- Developed with React and TypeScript.
- State management with Redux.
- UI built with TailwindCSS.
- Utilizes pagination and virtual scrolling for optimization.
- Handles two separate WebSocket connections as well as HTTP requests.

### State Management

Using Redux for state management is crucial for this app, as many state slices are important to different components. State is one of the most critical aspects of this app because the client needs to dynamically react to both socket events and HTTP requests. For example, the client connects to the notifications service and sends an `HTTP GET` request to the users service. It populates its users slice with the HTTP response but also listens for user events. This ensures data consistency between the client and the different microservices. The same flow applies to each chat conversation, as messages need to be queried from the messages service and updated by the ongoing conversation.










