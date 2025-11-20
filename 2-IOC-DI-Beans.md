## **Inversion of Control (IoC)**

#### Definition

**Inversion of Control (IoC)** is a design principle where:

> **The control of object creation and wiring is shifted from your code to a container (Spring).**

Traditionally, you control:

```java
UserRepository repo = new UserRepository();
UserService service = new UserService(repo);
```

With IoC:

* Spring creates objects
* Spring supplies their dependencies
* Spring manages object lifecycles
* Spring controls how objects connect

**You only write business logic; Spring controls the flow.**

---

#### Why IoC? (Motivation)

1. Avoids writing `new` everywhere
2. Reduces tight coupling
3. Centralizes object management
4. Makes testing easier (mock dependencies)
5. Gives cleaner, maintainable code
6. Enables powerful add-ons (AOP, Security, Transactions)

---

#### ✔IoC in Spring

Spring implements IoC through:

* **ApplicationContext** (main container)
* **BeanFactory** (basic container, rarely used directly)
* **Component scanning** (`@Component`, `@Service`, etc.)
* **Bean definitions**
* **Dependency Injection** (DI)

**IoC = The idea**
**DI = The mechanism** used by Spring to achieve IoC.

---

## **Dependency Injection (DI)**

#### Definition

**Dependency Injection (DI)** is a technique where:

> **Dependencies required by an object are provided (injected) by the container instead of the object creating them itself.**

A *dependency* = something a class needs.

Example:

```java
class Car {
    Engine engine;  // engine is a dependency
}
```

---

#### Why DI?

1. No need for manual object creation (`new Engine()`)
2. Removes hardcoded connections between classes
3. Promotes loose coupling
4. Allows easy replacement of components
5. Great for unit testing (mocking)
6. Works smoothly with AOP, Security, Transactions

---

## **Types of DI in Spring**

#### 1. **Constructor Injection** — Recommended

Spring reads constructor parameters and injects dependencies.

**Example:**

```java
@Service
class OrderService {

    private final OrderRepository repo;

    public OrderService(OrderRepository repo) {
        this.repo = repo;
    }
}
```

Spring does:

1. Create `OrderRepository`
2. Call `new OrderService(repo_instance)`

**Why recommended?**

* Immutable dependencies
* Great for testing
* Ensures required dependencies are provided
* Works perfectly with Spring Boot

---

#### 2. **Setter Injection** — Optional/rarely used

```java
@Service
class NotificationService {

    private EmailSender sender;

    @Autowired
    public void setSender(EmailSender sender) {
        this.sender = sender;
    }
}
```

Used when:

* some dependencies are optional
* or need to be changed after object creation

---

#### 3. **Field Injection** — Not recommended (legacy)

```java
@Service
class UserService {

    @Autowired
    UserRepository repo;
}
```

Downsides:

* Hard to test
* No immutability
* Hidden dependencies

Still used in simple demos.

---

## How Spring Knows What to Inject (Dependency Resolution)

Spring follows this algorithm:

#### 1. Find the bean type needed

Example:

```java
public OrderService(OrderRepository repo) { ... }
```

Dependency type = `OrderRepository`.

#### 2. Find beans of that type

Spring scans for:

* `@Repository`
* `@Component`
* `@Service`
* `@Bean` methods

#### 3. If exactly one found → inject it

#### 4. If multiple found → use `@Qualifier`

#### 5. If none found → error (NoSuchBeanDefinitionException)

---

## Full Bean Creation Flow (Very Important)

When Spring starts:

#### Step 1 — ApplicationContext is created

`SpringApplication.run(...)`

#### Step 2 — Component scan

Spring finds all:

* `@Component`
* `@Service`
* `@Repository`
* `@Controller`
* `@Configuration`
* `@Bean` methods

#### Step 3 — Bean definitions created

Spring records:

* bean name
* bean type
* constructor
* required dependencies
* scope
* proxies needed (AOP)

#### Step 4 — Dependency resolution

Spring sees what beans depend on what.

#### Step 5 — Beans created in correct order

Dependencies first, dependents next.

#### Step 6 — Dependency Injection

Constructor/setter/field injection happens.

#### Step 7 — Bean post-processors

AOP, @Transactional, Security proxies are applied.

#### Step 8 — Beans stored in ApplicationContext

Ready for use.

---

## Complete Example (Step-by-Step)

#### Step A — Repository

```java
@Repository
class UserRepository {
    void save(User u) { }
}
```

#### Step B — Service depending on repository

```java
@Service
class UserService {

    private final UserRepository repo;

    public UserService(UserRepository repo) {
        this.repo = repo;
    }
}
```

#### Step C — Controller depending on service

```java
@RestController
class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }
}
```

#### Spring Boot Startup Flow:

1. Finds `UserRepository` → creates bean
2. Finds `UserService` → sees constructor → needs repo → injects repo
3. Finds `UserController` → needs `UserService` → injects service

Final dependency graph:

```
UserController
    ↓
UserService
    ↓
UserRepository
```

---

