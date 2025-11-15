# How to Write Listener Class
-----------------------------

```java
class CancelOrderEventListener implements ApplicationListener<CancelOrderEvent> {
    // Handler Method
    @Override
    public void onApplicationEvent(CancelOrderEvent event) {

    }
}
````

* We cannot write any random class and call it a Listener with handler methods.
* The **IOC Container acts as the Listener dispatcher**, so it must know which class is a listener.
* To identify a Listener, the class must **implement `ApplicationListener<CancelOrderEvent>`** and override the `onApplicationEvent()` method.
* Based on the **generic type**, the IOC container identifies the listener and calls the handler method whenever a matching event occurs.

---

# How to Write Event Class

---

```java
class ShippedEvent {
    int orderNo;
    Date shippedDate;
    Object source;

    public ShippedEvent(Object source) {
        this.source = source;
    }
}

class CancelledEvent {
    int orderNo;
    Date cancelledDate;
    String reason;
    double refundAmount;
    Object source;

    public CancelledEvent(Object source) {
        this.source = source;
    }
}
```

* In the above code, each event class has a **source** field.
* It is **mandatory**, but repeating this code in every event class is duplicate work.
* Therefore, Spring provides a base class called **`ApplicationEvent`**.

---

# After Extending ApplicationEvent

---

```java
class ApplicationEvent {
    Object source;

    public ApplicationEvent(Object source) {
        this.source = source;
    }
}
```

```java
class ShippedEvent extends ApplicationEvent {
    int orderNo;
    Date shippedDate;

    public ShippedEvent(Object source) {
        /* Super class has parameterized constructor.
           Without calling super(source), the child object will not be created. */
        super(source);
    }
}
```

```java
class CancelledEvent extends ApplicationEvent {
    int orderNo;
    Date cancelledDate;
    String reason;
    double refundAmount;

    public CancelledEvent(Object source) {
        /* Super class has parameterized constructor.
           Without calling super(source), the child object will not be created. */
        super(source);
    }
}
```

* Our event classes must **extend `ApplicationEvent`**.
* They must call the **parameterized constructor** of `ApplicationEvent`.
* Only then Spring IOC treats them as valid event classes and can identify them properly.

---

# How to Write Source Class

---

```java
class OrderManagementService implements ApplicationEventPublisherAware {

    private ApplicationEventPublisher applicationEventPublisher;

    public void updateOrder(int orderNo, String status) {

        // Update the order info in the database

        if (status.equals("shipped")) {
            ShippedEvent shippedEvent = new ShippedEvent(this); // 'this' is the source
            shippedEvent.setOrderNo(orderNo);
            shippedEvent.setShippedDate(new Date());
            applicationEventPublisher.publishEvent(shippedEvent);
        }
    }

    @Override
    public void setApplicationEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
        this.applicationEventPublisher = applicationEventPublisher;
    }
}
```

* The **Source** is capable of publishing events.
* To publish events in Spring, we use the **`ApplicationEventPublisher`** interface.
* `ApplicationEventPublisher` is an **internal object of the IOC container**.
* Through **Aware Injection**, when we implement `ApplicationEventPublisherAware`,
  Spring automatically provides the `ApplicationEventPublisher` instance when the IOC container is created.


# Full Example for Event-Driven Programming in Spring Core
-----------------------------------------------------------

## RefundManagerListner.java (Listener class with handler methods)
---------------------------------------------------------------

```java
public class RefundManagerListner implements ApplicationListener<CancelOrderEvent> {

    @Override
    public void onApplicationEvent(CancelOrderEvent event) {
        // Call the bank gateway and initiate the refund process
        System.out.println(
            "The refund initiated with orderNo " +
            event.getOrderNo() + " and amount is " + event.getRefundAmount()
        );
    }
}
````

---

## CancelOrderEvent.java (Event)

---

```java
public class CancelOrderEvent extends ApplicationEvent {

    private Integer orderNo;
    private String reason;
    private double refundAmount;

    public CancelOrderEvent(Object source) {
        super(source);
    }

    public Integer getOrderNo() {
        return orderNo;
    }

    public String getReason() {
        return reason;
    }

    public double getRefundAmount() {
        return refundAmount;
    }

    public void setOrderNo(Integer orderNo) {
        this.orderNo = orderNo;
    }

    public void setReason(String reason) {
        this.reason = reason;
    }

    public void setRefundAmount(double refundAmount) {
        this.refundAmount = refundAmount;
    }
}
```

---

## OderManagementService.java (Source)

---

```java
public class OderManagementService implements ApplicationEventPublisherAware {

    private ApplicationEventPublisher applicationEventPublisher;

    public void updateStatus(Integer orderNo, String status) {

        if (status != null) {
            // Update the status in DB

            if (status.equals("cancel")) {

                CancelOrderEvent cancelOrderEvent = new CancelOrderEvent(this);
                cancelOrderEvent.setOrderNo(orderNo);
                cancelOrderEvent.setReason("Price is heavy and delivery takes lot of time");
                cancelOrderEvent.setRefundAmount(4000.00);

                applicationEventPublisher.publishEvent(cancelOrderEvent);
            }
        }
    }

    @Override
    public void setApplicationEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
        this.applicationEventPublisher = applicationEventPublisher;
    }
}
```

---

## application-context.xml

---

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="orderManagementService" class="com.ep.source.OderManagementService"/>
    <bean class="com.ep.listner.RefundManagerListner"/>
<!---For Listner we cannot provide the Id because IOC will run it. If we can use then we provide the Id->

</beans>
```

---

## EventProgrammingTest.java (Test)

---

```java
public class EventProgrammingTest {

    public static void main(String[] args) {

        // BeanFactory doesn't support event-driven programming

        ApplicationContext context =
                new ClassPathXmlApplicationContext("application-context.xml");

        OderManagementService orderManagementService =
                context.getBean("orderManagementService", OderManagementService.class);

        orderManagementService.updateStatus(2, "cancel");
    }
}
```

---

## Output

---

```
The refund intiated with orderNo 2 and amountis 4000.0
```
