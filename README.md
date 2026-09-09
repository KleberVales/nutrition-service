# Nutrition Service

A Spring Boot microservice responsible for managing users' daily nutrition data and registering meals and their calorie intake.

This service is part of a **microservices-based nutrition application**, with a focus on clean separation of responsibilities, domain-driven design principles, and **Hexagonal Architecture (Ports and Adapters)**.

## 🚀 Overview

The Nutrition Service manages the user's daily nutritional information.

The current implementation provides an endpoint for registering a meal. When a meal is registered, the service:

1. Receives the user ID and meal calories.
2. Finds the user's nutrition record for the current day.
3. Adds the meal calories to the daily calorie total.
4. Persists the updated nutrition information.
5. Returns the updated result.

The application is designed to keep the business rules independent from frameworks and infrastructure.

## 🏗️ Architecture

The project follows **Hexagonal Architecture**, separating the business domain from external technologies such as HTTP and database persistence.

```text
                 ┌─────────────────────────┐
                 │      HTTP Client         │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │   NutritionController   │
                 │      Inbound Adapter    │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │   RegisterMealUseCase   │
                 │      Inbound Port       │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │   RegisterMealService   │
                 │   Application Service   │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │ DailyNutritionRepository│
                 │       Outbound Port     │
                 └────────────┬────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │ Persistence Adapter     │
                 │      JPA / PostgreSQL   │
                 └─────────────────────────┘
```

### Architecture layers

#### Domain

Contains the core business model and rules.

```text
domain/
└── DailyNutrition.java
```

`DailyNutrition` represents the user's daily nutritional state and contains domain behavior such as adding calories.

#### Application

Contains the application's use cases and ports.

```text
application/
├── port/
│   ├── in/
│   └── out/
└── service/
    └── RegisterMealService.java
```

The inbound port defines what the application can do, while outbound ports define the dependencies required by the application.

#### Adapters

Connect the application to external technologies.

```text
adapter/
├── in/
│   └── web/
└── out/
    ├── persistence/
    └── security/
```

The HTTP controller is an inbound adapter, while persistence and security implementations are outbound adapters.

## 🛠️ Technologies

| Technology        | Purpose                 |
| ----------------- | ----------------------- |
| Java 21           | Programming language    |
| Spring Boot 3.2.4 | Application framework   |
| Spring Web        | REST API                |
| Spring Data JPA   | Persistence abstraction |
| PostgreSQL        | Relational database     |
| Spring Security   | Application security    |
| JJWT              | JWT processing          |
| Lombok            | Boilerplate reduction   |
| Gradle            | Build automation        |
| JUnit 5           | Testing                 |

The project uses Java 21 through the Gradle Java toolchain and Spring Boot 3.2.4.

## 📂 Project Structure

```text
nutrition-service/
│
├── gradle/
│   └── wrapper/
│
├── src/
│   └── main/
│       ├── java/
│       │   └── com/
│       │       └── kvales/
│       │           └── nutrition/
│       │               │
│       │               ├── adapter/
│       │               │   ├── in/
│       │               │   │   └── web/
│       │               │   │       └── NutritionController.java
│       │               │   │
│       │               │   └── out/
│       │               │       ├── persistence/
│       │               │       └── security/
│       │               │
│       │               ├── application/
│       │               │   ├── port/
│       │               │   │   ├── in/
│       │               │   │   └── out/
│       │               │   │
│       │               │   └── service/
│       │               │       └── RegisterMealService.java
│       │               │
│       │               ├── domain/
│       │               │   └── DailyNutrition.java
│       │               │
│       │               └── NutritionMain.java
│       │
│       └── resources/
│
├── build.gradle
├── gradlew
├── gradlew.bat
└── settings.gradle
```

This structure reflects the repository's current separation into `adapter`, `application`, and `domain` packages.

## 🔌 API

### Register a meal

Registers the calories consumed by a user for the current day.

**Endpoint**

```http
POST /api/nutrition/meal
```

**Request**

```json
{
  "userId": 1,
  "calories": 650
}
```

### Request fields

| Field      | Type   | Description                   |
| ---------- | ------ | ----------------------------- |
| `userId`   | `Long` | User identifier               |
| `calories` | `int`  | Calories consumed in the meal |

The controller maps the request into a `RegisterMealCommand` and delegates the operation to the application use case.

## 🔄 Request Flow

A meal registration follows this flow:

```text
HTTP Request
     │
     ▼
NutritionController
     │
     ▼
RegisterMealUseCase
     │
     ▼
RegisterMealService
     │
     ▼
DailyNutritionRepository
     │
     ▼
Persistence Adapter
     │
     ▼
PostgreSQL
```

The application service retrieves the nutrition record for the current date, applies the domain operation to add calories, and persists the updated entity.

## 🧠 Domain Model

The main domain object is:

```text
DailyNutrition
```

It represents the nutritional state of a user for a specific day.

Instead of allowing application or infrastructure code to directly manipulate the calorie value, the domain exposes behavior such as:

```java
nutrition.addCalories(calories);
```

This keeps the business operation inside the domain model.

## 🔌 Ports and Adapters

The project uses ports to keep the core application independent of infrastructure.

### Inbound Port

Defines application use cases.

```text
application/port/in/
```

Example:

```text
RegisterMealUseCase
```

The controller depends on the use case rather than directly depending on the service implementation.



