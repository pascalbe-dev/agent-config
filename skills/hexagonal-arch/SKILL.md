---
name: hexagonal-arch
description: Use when setting up the architecture for a new feature or domain using hexagonal architecture (ports & adapters). Triggers on phrases like "Setup Architektur für Feature X", "Erstelle die Struktur für Domain Y", "Wie soll Feature Z aufgebaut werden?". Also referenced from the building-features skill during Phase 4 (planning) and Phase 5 (execution).
---

# Hexagonal Architecture (Ports & Adapters)

## Overview

This project uses **hexagonal architecture** to keep domain logic independent from frameworks, databases, and external services.

**Core principle:** The domain and application layer know nothing about adapters (Spring, JPA, HTTP clients). Adapters depend on the application layer – never the other way around.

```
adapter → application → domain
```

---

## Package Structure (Layer-first)

```
de.pascalbe.springbootaitester/
├── domain/
│   └── <feature>/           ← Pure domain: Entities, Value Objects
├── application/
│   └── <feature>/
│       ├── port/
│       │   ├── in/          ← Driving Ports (interfaces the adapter calls)
│       │   └── out/         ← Driven Ports (interfaces the adapter implements)
│       └── <Feature>Service.java  ← Use Case implementation
└── adapter/
    ├── in/
    │   └── rest/
    │       └── <feature>/   ← REST Controllers
    └── out/
        ├── persistence/
        │   └── <feature>/   ← JPA Entities, Repositories, Mappers
        └── http/
            └── <feature>/   ← External HTTP API clients
```

**Example for feature `order`:**

```
domain/order/
├── Order.java
├── OrderId.java
└── OrderStatus.java

application/order/
├── port/in/CreateOrderUseCase.java
├── port/in/GetOrderUseCase.java
├── port/out/OrderRepository.java
├── port/out/PaymentGateway.java       ← only if external API involved
└── OrderService.java

adapter/in/rest/order/
├── OrderController.java
└── dto/
    ├── CreateOrderRequest.java
    └── OrderResponse.java

adapter/out/persistence/order/
├── OrderJpaEntity.java
├── OrderJpaRepository.java
├── OrderPersistenceAdapter.java
└── OrderEntityMapper.java

adapter/out/http/order/               ← only if external API involved
├── PaymentHttpAdapter.java
└── dto/
    ├── PaymentRequest.java
    └── PaymentResponse.java
```

---

## Key Patterns

### 1. Domain Entity (pure Java, no framework annotations)

```java
// domain/order/Order.java
public class Order {
    private final OrderId id;
    private final String customerId;
    private OrderStatus status;

    public static Order create(String customerId) {
        return new Order(OrderId.generate(), customerId, OrderStatus.PENDING);
    }

    public void confirm() {
        if (this.status != OrderStatus.PENDING) {
            throw new IllegalStateException("Only PENDING orders can be confirmed");
        }
        this.status = OrderStatus.CONFIRMED;
    }
    // constructor, getters (no Lombok in domain – plain Java)
}
```

```java
// domain/order/OrderId.java
public record OrderId(UUID value) {
    public static OrderId generate() {
        return new OrderId(UUID.randomUUID());
    }
}
```

### 2. Driving Port – what the inbound adapter calls (interface in application layer)

```java
// application/order/port/in/CreateOrderUseCase.java
public interface CreateOrderUseCase {
    Order createOrder(String customerId);
}
```

### 3. Driven Port – what the application needs from outside (interface in application layer)

```java
// application/order/port/out/OrderRepository.java
public interface OrderRepository {
    Order save(Order order);
    Optional<Order> findById(OrderId id);
}
```

### 4. Use Case (implements driving port, injects driven ports via constructor)

```java
// application/order/OrderService.java
@Service
@RequiredArgsConstructor
public class OrderService implements CreateOrderUseCase {

    private final OrderRepository orderRepository;  // driven port – injected by Spring

    @Override
    public Order createOrder(String customerId) {
        Order order = Order.create(customerId);
        return orderRepository.save(order);
    }
}
```

### 5. REST Controller – inbound adapter (calls driving port)

```java
// adapter/in/rest/order/OrderController.java
@RestController
@RequestMapping("/api/orders")
@RequiredArgsConstructor
public class OrderController {

    private final CreateOrderUseCase createOrderUseCase;

    @PostMapping
    public ResponseEntity<OrderResponse> createOrder(@RequestBody CreateOrderRequest request) {
        Order order = createOrderUseCase.createOrder(request.customerId());
        return ResponseEntity.status(HttpStatus.CREATED).body(OrderResponse.from(order));
    }
}
```

### 6. JPA Entity – only inside persistence adapter, never in domain

```java
// adapter/out/persistence/order/OrderJpaEntity.java
@Entity
@Table(name = "orders")
@Getter @Setter @NoArgsConstructor @AllArgsConstructor
class OrderJpaEntity {
    @Id
    private UUID id;
    private String customerId;
    @Enumerated(EnumType.STRING)
    private OrderStatus status;
}
```

```java
// adapter/out/persistence/order/OrderJpaRepository.java
interface OrderJpaRepository extends JpaRepository<OrderJpaEntity, UUID> {}
```

### 7. Persistence Adapter – implements driven port, uses JPA internally

```java
// adapter/out/persistence/order/OrderPersistenceAdapter.java
@Component
@RequiredArgsConstructor
class OrderPersistenceAdapter implements OrderRepository {

    private final OrderJpaRepository jpaRepository;
    private final OrderEntityMapper mapper;

    @Override
    public Order save(Order order) {
        return mapper.toDomain(jpaRepository.save(mapper.toJpa(order)));
    }

    @Override
    public Optional<Order> findById(OrderId id) {
        return jpaRepository.findById(id.value()).map(mapper::toDomain);
    }
}
```

```java
// adapter/out/persistence/order/OrderEntityMapper.java
@Component
class OrderEntityMapper {
    Order toDomain(OrderJpaEntity e) {
        return new Order(new OrderId(e.getId()), e.getCustomerId(), e.getStatus());
    }
    OrderJpaEntity toJpa(Order o) {
        return new OrderJpaEntity(o.getId().value(), o.getCustomerId(), o.getStatus());
    }
}
```

### 8. HTTP Adapter – implements driven port for external APIs

```java
// adapter/out/http/order/PaymentHttpAdapter.java
@Component
@RequiredArgsConstructor
class PaymentHttpAdapter implements PaymentGateway {

    private final RestClient restClient;

    @Override
    public PaymentResult charge(OrderId orderId, Money amount) {
        PaymentRequest request = new PaymentRequest(orderId.value(), amount.value());
        PaymentResponse response = restClient.post()
                .uri("/payments")
                .body(request)
                .retrieve()
                .body(PaymentResponse.class);
        return new PaymentResult(response.transactionId());
    }
}
```

---

## Database Migration

For every new domain entity with a DB table, create a Flyway migration:

```sql
-- src/main/resources/db/migration/V<next>__create_<feature>_table.sql
CREATE TABLE orders (
    id          UUID PRIMARY KEY,
    customer_id VARCHAR(255) NOT NULL,
    status      VARCHAR(50)  NOT NULL
);
```

Numbering: increment the last existing migration version by 1.

---

## Naming Conventions

| Concept              | Suffix / Prefix               | Example                       |
|----------------------|-------------------------------|-------------------------------|
| Domain Entity        | (none)                        | `Order`                       |
| Value Object         | (none)                        | `OrderId`, `Money`            |
| Driving Port (in)    | `UseCase`                     | `CreateOrderUseCase`          |
| Driven Port (out)    | `Repository` / `Gateway`      | `OrderRepository`, `PaymentGateway` |
| Use Case Impl        | `Service`                     | `OrderService`                |
| REST Controller      | `Controller`                  | `OrderController`             |
| Request DTO          | `Request`                     | `CreateOrderRequest`          |
| Response DTO         | `Response`                    | `OrderResponse`               |
| JPA Entity           | `JpaEntity`                   | `OrderJpaEntity`              |
| JPA Repository       | `JpaRepository`               | `OrderJpaRepository`          |
| Persistence Adapter  | `PersistenceAdapter`          | `OrderPersistenceAdapter`     |
| Entity Mapper        | `EntityMapper`                | `OrderEntityMapper`           |
| HTTP Adapter         | `HttpAdapter`                 | `PaymentHttpAdapter`          |

---

## Anti-Patterns – Never Do This

| Anti-Pattern                                              | Why                                          |
|-----------------------------------------------------------|----------------------------------------------|
| `@Entity` on a domain class                               | Couples domain to JPA framework              |
| `@Service` / `@Component` in `domain/`                   | Domain must be framework-free                |
| Controller directly calling `OrderJpaRepository`          | Bypasses use case and domain logic           |
| Business logic inside a Controller or PersistenceAdapter  | Adapters only translate – no business rules  |
| `application/` importing from `adapter/`                  | Violates the dependency direction            |

---

## Checklist – Setting Up a New Feature

When implementing a new feature, work through these steps in order:

1. [ ] Create domain entity in `domain/<feature>/`
2. [ ] Create Value Objects as needed (IDs, enums, etc.)
3. [ ] Define driving ports (in) in `application/<feature>/port/in/`
4. [ ] Define driven ports (out) in `application/<feature>/port/out/`
5. [ ] Implement use case in `application/<feature>/<Feature>Service.java`
6. [ ] Create REST Controller + DTOs in `adapter/in/rest/<feature>/`
7. [ ] Create JPA Entity, JpaRepository, PersistenceAdapter, EntityMapper in `adapter/out/persistence/<feature>/`
8. [ ] Create HTTP Adapter in `adapter/out/http/<feature>/` (only if external API needed)
9. [ ] Create Flyway migration for any new DB tables
