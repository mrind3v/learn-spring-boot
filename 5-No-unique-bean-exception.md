## **What Is NoUniqueBeanDefinitionException?**

It is a Spring error that means:

> **Spring found more than one bean of the same type, and it doesn't know which one to inject.**

Spring tries to autowire a bean **by type** first.

But if multiple beans of the same type exist → **confusion** → exception.

---

## **WHY DOES IT HAPPEN?**

Because Spring’s IoC container has **multiple beans of the same class**.

Example:

```java
@Component
class Dog {}

@Component
class Dog {}
```

Or:

```java
@Bean
Dog dog1() { return new Dog(); }

@Bean
Dog dog2() { return new Dog(); }
```

Spring says:

> “You told me to inject a Dog… but which Dog? dog1 or dog2?”

So it throws:

```
NoUniqueBeanDefinitionException: expected single matching bean but found 2
```

---

## **Very Simple Example That Causes the Error**

#### Scenario

You have two implementations of an interface.

```java
public interface PaymentService {
    void pay();
}

@Service
class UpiPayment implements PaymentService {
    public void pay() { … }
}

@Service
class CardPayment implements PaymentService {
    public void pay() { … }
}
```

Now you try to autowire:

```java
@Autowired
PaymentService service;
```

Spring sees:

* UpiPayment → PaymentService
* CardPayment → PaymentService

So which one should it inject?

It doesn’t know → **NoUniqueBeanException**.

---

## How to Solve It (3 Ways)

#### 1. **Use @Primary** (default bean)

Make one bean the default.

```java
@Service
@Primary
class UpiPayment implements PaymentService { }
```

Now Spring will choose **UpiPayment** automatically.



#### 2. **Use @Qualifier** (specify exactly which bean)

```java
@Autowired
@Qualifier("cardPayment")
PaymentService service;
```

Or full version:

```java
@Service("cardPayment")
class CardPayment implements PaymentService { }
```

This removes ambiguity → Spring injects the bean you named.



#### 3️. **Inject by Bean Name (Constructor Injection)**

```java
public OrderService(@Qualifier("upiPayment") PaymentService p) {
    this.paymentService = p;
}
```

This is the clearest and safest method.

---

## **Another Example: Same Type, Different Purpose**

Consider:

```java
@Service
class MyService {
    @Autowired
    private RestTemplate restTemplate;
}
```

But you have two RestTemplates:

```java
@Bean
RestTemplate rest1() { return new RestTemplate(); }

@Bean
RestTemplate rest2() { return new RestTemplate(); }
```

→ **NoUniqueBeanException**

Fix using `@Qualifier`:

```java
@Autowired
@Qualifier("rest2")
RestTemplate restTemplate;
```

---
