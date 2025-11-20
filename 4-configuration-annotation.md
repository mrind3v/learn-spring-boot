## What Is a Configuration Bean in Spring?

A **configuration bean** refers to:

1. A class annotated with **`@Configuration`**
2. Methods inside it annotated with **`@Bean`**

Such methods **manually create and return objects** that the Spring IoC container should manage.

---

## Why Do We Need This?

Spring usually auto-detects beans using annotations like:

* `@Component`
* `@Service`
* `@Repository`
* `@Controller`

**BUT**
Sometimes you need to create a bean **manually**, because:

* You need to configure or customize it
* You need to pass constructor parameters
* It is a third-party class **that you cannot annotate**
* It should not be scanned as a component

That’s where **`@Configuration + @Bean`** comes in.

---

## The @Configuration Class

```java
@Configuration
public class AppConfig {

    @Bean
    public DataSource dataSource() {
        return new DataSource("localhost", 3306);
    }

    @Bean
    public UserService userService() {
        return new UserService(dataSource());
    }
}
```

#### What happens here?

* Spring will instantiate this `AppConfig` class.
* Spring will run every `@Bean` method inside it.
* Whatever those methods return → **becomes a Spring-managed bean** stored inside the IoC container.
* Spring also manages dependencies between beans (`userService()` depends on `dataSource()`).

---

## How Spring Uses @Configuration + @Bean

Consider:

```java
@Bean
public DataSource ds() {
    return new DataSource();
}
```

Spring:

1. Sees this method
2. Executes it **once**
3. Takes the returned object
4. Stores it in the **IoC container**
5. Makes it available for injection using `@Autowired`

---

## Injecting a @Bean into another class

```java
@Service
public class OrderService {
    @Autowired
    private DataSource ds;
}
```

`ds` is injected automatically even though `DataSource` isn't annotated with `@Component`.
It came from:

```java
@Bean
DataSource ds()
```

---

##  Why @Configuration Is Important

Without `@Configuration`, Spring **won’t apply special proxying**.

Example:

```java
@Component
class BadConfig {
    @Bean
    MyService myService() {
        return new MyService();
    }
}
```

Here:

* Spring calls the method
* But calling `myService()` inside the class **may create a new object** (NOT singleton)

With `@Configuration`:

* Spring ensures `myService()` always returns the **same object**.

So:
`@Configuration` guarantees **correct, singleton-managed bean instances**.


