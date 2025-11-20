## What Are **Spring Beans**?

**Spring Bean = Any object that Spring creates, manages, and injects.**

A bean is simply:

> **A Java object stored inside the Spring IoC container.**

Spring Beans come from:

* `@Component`
* `@Service`
* `@Repository`
* `@Controller`
* `@RestController`
* `@Configuration`
* `@Bean` methods

Example:

```java
@Service
class OrderService { }
```


---

## 2. What Is the **IoC Container**?

The **IoC Container** is the *concept* of Spring managing:

* creation of beans
* dependency injection
* lifecycle
* scopes
* AOP proxying


The container itself has two concrete implementations:

1. **BeanFactory** (minimal version)
2. **ApplicationContext** (full-featured version)

We rarely use BeanFactory directly.

---

## What Is the **ApplicationContext**?

ApplicationContext is:

> **The full Spring IoC container + extra features.**

It **IS** the IoC container for 99% of Spring applications.

It adds:

* event publishing
* internationalization
* environment properties
* lifecycle callbacks
* AOP proxy creation
* MVC setup
* Security filter chain
* BeanPostProcessors
* Auto-configuration (Spring Boot)

So:

IoC Container → *concept*

ApplicationContext → *actual object implementing the container*

Spring uses **ApplicationContext** to *store all beans*.

---

## **Beans live inside the ApplicationContext**

This addresses your question:

> “If objects with annotations are already stored in IoC container, why do we need context?”

Because:

- The IoC container is NOT a separate place

- The IoC container **is** the ApplicationContext

- Beans don't exist anywhere else except inside the ApplicationContext

The **context** *IS* the storage, manager, and controller of all beans.

You need context because:

* It is how Spring *stores* the beans
* It is how Spring *retrieves* beans
* It is how Spring *injects* beans into other beans
* It is how the app *accesses* Spring-managed objects

Without ApplicationContext → no Spring.

---

**Beans** → live inside IoC Container
**IoC Container** → is implemented by ApplicationContext
**ApplicationContext** → the thing running your Spring Boot app

---

## What Is SpEL (Spring Expression Language)?

**SpEL = A small expression language used inside Spring annotations or config.**

You can use it to:

* read values from application.properties
* compute values
* inject environment variables
* access bean properties
* conditional logic in annotations

Example:

```java
@Value("#{2 + 3}")
private int sum; // 5
```

Read from properties:

```java
@Value("${server.port}")
private int port;
```

Use bean values:

```java
@Value("#{userService.defaultUserName}")
private String name;
```

Conditional beans:

```java
@ConditionalOnExpression("${feature_x_enabled} == true")
```


## COMPLETE PICTURE (Everything Together)

```
                      ApplicationContext
   ┌────────────────────────────────────────────────┐
   │                                                │
   │   (implements IoC container)                   │
   │                                                │
   │   - Creates beans                              │
   │   - Injects dependencies                       │
   │   - Stores bean instances                      │
   │   - Handles AOP, Security, MVC                 │
   │   - Uses SpEL for config, values               │
   │                                                │
   │   All Spring Beans live inside this container  │
   └────────────────────────────────────────────────┘

```




