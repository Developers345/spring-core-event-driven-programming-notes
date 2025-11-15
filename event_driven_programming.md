### **1. Linear Programming Model**

Your idea refers more to **sequential programming**, not “Linear Programming” (which is a math optimization technique).

Correct term: **Sequential Programming Model**

Correct explanation:

* Instructions execute **top to bottom**, one after another.
* Procedural and object-oriented programming follow this sequential execution model by default.

---

### **2. Multi-Threaded Programming Model**

Corrections:

* “simientionusly” → “simultaneously”
* Multi-threading does **not always** guarantee parallel execution — parallelism depends on CPU cores.

Improved explanation:

* Multiple independent paths (threads) run **concurrently**.
* If hardware has multiple cores → threads may run **in parallel**.
* Multi-threaded programming is **synchronous only when threads depend on each other** (e.g., using `join`, locks).
  Otherwise, threads can run independently.

---

### **Blocking**

Corrections:

* “Calle” → “Callee”
* “Caller” vs “Callee” roles clarified

Correct explanation:

* In blocking execution, the **caller waits** until the **callee finishes**.
  (Opposite of what your text says.)

---

### **3. Asynchronous Programming**

Corrections:

* Asynchronous programming is **not always single-threaded**.
  → It depends on the language/platform.

Examples:

* JavaScript → asynchronous but single-threaded
* Java, .NET → asynchronous can use thread pools internally

Better explanation:

* Caller does **not wait** for callee to complete.
* Communication happens through **callbacks, events, promises, futures**.
* Asynchronous ≠ multithreaded
  (though async operations can use threads internally)

---

### **Distributed Asynchronous Behavior**

Correct explanation:

* If caller & callee are on different machines, async calls are usually **fire-and-forget** or callback-based.
* They do not maintain continuous connection unless explicitly designed (like gRPC streaming).

### **Pictorial Representation**

<img width="3163" height="2838" alt="EVENT-DRIVEN-PROGRAMMING" src="https://github.com/user-attachments/assets/c861968c-caed-48b0-9dee-4cbb0a64f82c" />


# Event-Driven Programming Model
--------------------------------

In an Event-Driven Programming Model, there are **4 main actors**:

---

## 1. Source
- The person or component that **originates** an operation/action in the application by publishing an event.
- Source is the **starting point** of the application.
- **Source cannot perform the operation**.

### Why Source cannot perform the operation?
- Due to **modularity**, the source may need to communicate with other components.
- We cannot write all methods in one class; hence we separate responsibilities into different modules.
- The source always **publishes or triggers an Event**.

---

## 2. Event
- An Event is an **object** encapsulated with:
  - Data  
  - The source from which it originates
- Neither the **Source** nor the **Event** should directly call the **Event Handler**.
- The Source can trigger **multiple events**, based on different operations.

---

## 3. Event Handler
- The Event Handler is **capable of performing the operation**.
- It contains **one method per event type**, where each method receives the event as input.

---

## 4. Listener
- The Listener sits **between the Source and the Event Handler**.
- The Event Handler registers with the Listener.
- The Listener calls the Event Handler whenever the **corresponding event occurs**.
- The Source registers with the Listener, and the Listener continuously listens for events.
- When the Source publishes an event, the Listener **invokes the appropriate Event Handler** based on event type.


## Pictorial Representation - Before Event Driven Programmming 

<img width="1408" height="746" alt="Interfaces problem" src="https://github.com/user-attachments/assets/1bc0dada-61ea-4fe5-9df7-42947e9a7ce0" />

## Pictorial Representation - After Event Driven Programmming - 1

<img width="3163" height="2838" alt="EVENT-PROGRAMMING-EXPLANATION" src="https://github.com/user-attachments/assets/c95b672f-0868-4316-83d3-5564f0176960" />

## Pictorial Representation - After Event Driven Programmming -2 

<img width="3163" height="2838" alt="EVENT_PROGRAMMING" src="https://github.com/user-attachments/assets/cf0efdc3-4a90-410e-83b8-a1d56d19add2" />


# Advantage of the Event-Driven Approach
---------------------------------------

## 1. Loose Coupling in Application Development
- The Source does not directly talk to the Event Handler.  
- Even if the Event Handler changes, the Source is **not affected** because it does not know who the handler is.

---

## 2. Multiple Event Handlers Supported
- Multiple Event Handlers can **listen for the same event**.  
- This allows flexible and extensible application behavior.

---

## 3. No Class-to-Class Direct References
- None of the classes hold direct references to each other.
- Communication happens **only through events**, making the application completely **loosely coupled**.

---

## Additional Notes

### Event-Driven Programming can be:
- **Synchronous**
- **Asynchronous**

---

### Blocking / Synchronous Event-Driven Programming Model
- The Source publishes an event → Listener receives → Listener invokes the Event Handler.
- Until the Event Handler method completes execution, the Source remains **blocked** (assuming single-threaded execution).
- By default, this model is **single-threaded**, so the Source waits until handling finishes.

---

### Multi-Threaded Event-Driven Implementations
Some vendors implement Event-Driven Programming on **multi-threaded models**, e.g.:

- **Spring Framework**
  1. Single-Threaded Event-Driven Programming  
  2. Multi-Threaded Event-Driven Programming

---

### Distributed Event-Driven Programming
- Common in distributed systems such as **JMS**, **Kafka**, etc.
- The Source runs on one machine, the Event Handler runs on another machine, and the Listener is part of the **infrastructure** (e.g., a server or message broker).
- This results in:
  - Completely loosely-coupled communication  
  - Asynchronous behavior  
  - Disconnected application development  

  ```md
# Example for Event-Driven Programming Model
-------------------------------------------

## Servlet Container
--------------------

- When we deploy (place) a web application in a **Servlet Container**, the application becomes ready for access when the Application Server starts.

- During startup, the Application Server identifies the application, reads, and validates the **web.xml** file.  
  If `web.xml` is valid, the web application gets **registered** with the Servlet Container and becomes available for access.

- After deployment, when the web application is successfully loaded in the Servlet Container, some components may need to perform initialization operations.

- The Servlet Container calls our application classes to perform such operations because the application is registered with the container.  
  To identify which classes must be invoked, the container looks into:
  - The deployment descriptor (**web.xml**)
  - Implemented interfaces (like `HttpServlet`)

- Suppose there are 300 classes in an application. Not every class needs to perform operations during startup.  
  Therefore, it is not mandatory for the Servlet Container to invoke all classes while starting the application.

- To solve this problem, the Servlet Container **implements the Event-Driven Programming Model**.

### Pictorial Respresentation - Servlet container example without event driven model

<img width="623" height="424" alt="Servlet container example without event driven model" src="https://github.com/user-attachments/assets/7ec52145-1221-4606-8d4e-8b208bd3d1e6" />

# Servlet Container implements the Event-Driven Programming Model
-----------------------------------------------------------------

- Upon deployment of the web application, the Servlet Container **publishes an event**.

- Any class in the application that wants to perform an operation during startup must write an **Event Handler** and register it with the container through `web.xml`.

- The Servlet Container publishes the startup event, and whichever application classes have corresponding event handlers will be **automatically invoked**.

- Now:
  - The Servlet Container does **not need to call each application class/method manually**.
  - The container does **not know which classes** are processing the event.
  - The classes do **not know who is calling** them.
  - This achieves **complete loose coupling**.

### Examples of such classes:
- `ServletContextListener`
- `HttpSessionListener`
- `HttpSessionAttributeListener`

These listener classes act as **Event Handlers** in the Servlet Container's Event-Driven Programming Model.

### Pictorial Respresentation - Servlet Container implement event driven programming model - 1

<img width="581" height="486" alt="Servlet Container implement event driven programming model" src="https://github.com/user-attachments/assets/67d4d6b4-0ff3-4f67-a2ac-4dbbb1add728" />

### Pictorial Respresentation - Servlet Container implement event driven programming model - 2

<img width="1919" height="629" alt="Screenshot 2025-11-15 164848" src="https://github.com/user-attachments/assets/cab91672-3c36-4f5a-aca4-7ae1eed0090e" />

