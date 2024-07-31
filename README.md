# GoChat

## _Messaging app written in golang_

GoChat is an app made to explore golang as a backend language in a microservices context.
This app is built with an **Event-driven**, **Microservices** architecture.
All backend services are written in go, and they all utilize **Rabbitmq** for microservices communication.
The frontend is written with **React** and **Typescript**, with **Redux** based state management.

## Features

- JWT based Authentication and authorization, stored as cookies.
- View online users.
- Real time websocket-based chat and notifications.
- Users, messages and auth data saved on Postgresql
- Backend optimizations with Redis caching and pagination strategies

## Technologies

- **Frontend:** **_React | Typescript | Redux_**
- **Backend:** **_Go | Gin framework_**
- **Databases:** **_PostgreSQL_**
- **Microservices Communication:** **_RabbitMQ_**
- **Chat and Notifications:** **_Goriila websocket_**
- **Caching:** **_Redis_**
- **Containerization and Orchestration:** ***Docker | Kubernetes | Nginx***

_Detailed implementation specifics exist for each microservice is in their README.md files_
