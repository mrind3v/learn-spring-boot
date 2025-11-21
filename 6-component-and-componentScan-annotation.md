## 1. What is **@Component**?

`@Component` is a **marker** that tells Spring:

> “Please create an object of this class and put it inside the IoC container.”

Example:

```java
@Component
public class Vehicle {
    private String name = "Audi";
}
```

When Spring sees this, it will create a bean:

```
Bean name: vehicle  
Bean type: Vehicle
```

BUT…

Spring **only sees it if** you have enabled scanning.

Otherwise, Spring does not even look at your class.

---

## 2. Problem: @Component alone does NOTHING

Just annotating a class with `@Component` does **not** make Spring find it.

Spring has no idea your class exists.

You MUST tell Spring **where to search** for components.

That’s why you need…

---

## 3. What is **@ComponentScan**?

`@ComponentScan` tells Spring:

> “Search these packages for any classes annotated with @Component (and @Service, @Repository, @Controller).”

Example:

```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
}
```

Now Spring will search:

* com.example
* com.example.service
* com.example.repository
* com.example.model
* etc.

and find classes like:

```java
@Component
class Vehicle { }
```

and register them as beans.

---

## Example Where @ComponentScan Is REQUIRED

Folder structure:

```
com.app.main
    AppConfig.java

com.app.vehicles
    Vehicle.java
```

Vehicle class:

```java
@Component
public class Vehicle {}
```

AppConfig:

```java
@Configuration
public class AppConfig {}
```

You run the app…
Spring creates **zero beans**.

**Why?**
Because Spring never scanned `com.app.vehicles`.

Solution:

```java
@Configuration
@ComponentScan("com.app")
public class AppConfig {}
```

Now Spring finds:

* Vehicle
* Any other @Component in com.app.*

---

## Why do we need @ComponentScan even if the @Configuration class defines @Bean methods?


Beans from @Bean methods and beans from @Component are NOT related.

Example:

```java
@Configuration
public class AppConfig {

    @Bean
    public Engine engine() {
        return new Engine();
    }
}
```

This bean is registered **because Spring processes @Configuration classes**.

But POJO classes annotated with @Component in other packages, like:

```java
@Component
public class Vehicle {}
```

are NOT registered unless you use:

```java
@ComponentScan
```

---
