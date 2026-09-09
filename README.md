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


