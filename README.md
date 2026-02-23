# Spring Boot Microservices with Kubernetes

## Overview

This project contains:

* **Auth Service** (Port 8081)
* **User Service** (Port 8082)
* **MySQL Service** (Port 3306)
* Kubernetes deployment files

Authentication is implemented using **Spring Security + Basic Auth + MySQL (JPA)**.

---

# Architecture

```
Client (Insomnia)
        |
        |-- Auth Service (8081)
        |-- User Service (8082)
                |
                ↓
           MySQL Service
```

---

# Services

## 1. Auth Service

* Register new user
* Login using Basic Auth
* View logged-in user
* Admin can view all users

### Endpoints

| Method | Endpoint            | Description                |
| ------ | ------------------- | -------------------------- |
| POST   | `/auth/register`    | Register new user          |
| GET    | `/auth/me`          | Get logged-in user         |
| GET    | `/auth/admin/users` | Get all users (ADMIN only) |

---

## 2. User Service

* Fetch user details
* Requires authentication

### Endpoints

| Method | Endpoint    | Description                |
| ------ | ----------- | -------------------------- |
| GET    | `/users/me` | Get logged-in user details |

---

# Default Admin

Created automatically at startup:

```
username: admin
password: admin123
role: ROLE_ADMIN
```

---

# Local Run (Without Kubernetes)

### Build

```
mvn clean package
```

### Run Auth Service

```
mvn spring-boot:run
```

### Run User Service

```
mvn spring-boot:run
```

---

# Docker Build

From project root:

```
docker build -t auth-service:latest ./auth-service
docker build -t user-service:latest ./user-service
```

---

# Kubernetes Deployment

## Apply Config

```
kubectl apply -f k8s/
```

## Verify Pods

```
kubectl get pods
```

Expected:

```
mysql              Running
auth-service       Running
user-service       Running
```

---

# Access Services

## Port Forward

```
kubectl port-forward svc/auth-service 8081:8081
kubectl port-forward svc/user-service 8082:8082
kubectl port-forward svc/mysql-service 3306:3306
```

---

# Test With Insomnia

### Login

Basic Auth:

```
username: admin
password: admin123
```

### Example Request

```
GET http://localhost:8081/auth/me
```

---

# Database Access (Inside Kubernetes)

```
kubectl exec -it <mysql-pod-name> -- mysql -u root -p
```

Password:

```
root
```

---

# Environment Variables (Kubernetes)

```
SPRING_DATASOURCE_URL=jdbc:mysql://mysql-service:3306/microservices_db
SPRING_DATASOURCE_USERNAME=root
SPRING_DATASOURCE_PASSWORD=root
```

---

# Common Issues

| Problem           | Cause                    | Fix                                  |
| ----------------- | ------------------------ | ------------------------------------ |
| ImagePullBackOff  | Local image not found    | Add `imagePullPolicy: Never`         |
| 401 Unauthorized  | In-memory auth active    | Configure `CustomUserDetailsService` |
| Cannot connect DB | Using `localhost` in K8s | Use `mysql-service`                  |

---

# Stack

* Java 25
* Spring Boot 4
* Spring Security 6+
* MySQL 8
* Docker
* Kubernetes

---

# Status

Fully working:

* DB authentication
* Role-based access
* Kubernetes deployment
* Dockerized services
