# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. In the Observer pattern, a Subscriber is usually an interface (or trait) so different types can handle updates differently. But in BambangShop, we only have one type of Subscriber, so a single struct is enough. If we ever need multiple subscriber types (e.g., email and webhook subscribers), then using a trait would make sense.
2. Since both id and url must be unique, using a Vec (list) isn’t ideal because checking for duplicates would be slow. DashMap is better because it allows fast lookups and prevents duplicates efficiently. It also helps when multiple threads access the data at the same time.
3. We still need DashMap because it provides built-in thread safety for multiple readers and writers. The Singleton pattern would ensure a single instance of the subscriber list, but it would still require manual synchronization (like using Mutex or RwLock) to be thread-safe. Since DashMap already handles this efficiently, using a Singleton alone wouldn’t be enough.

#### Reflection Publisher-2
1. Separating the Service and Repository from the Model keeps the code organized and easier to manage.  The Repository is responsible for data access, while the Service handles business logic, ensuring that each layer has a clear role. This makes the code easier to update, test, and reuse without breaking other parts of the system.
2. If everything is done inside the Model, the code becomes more complex and harder to maintain. Since Program, Subscriber, and Notification interact with each other, merging storage, retrieval, and business logic into one layer would create tightly coupled code. This would make debugging, scaling, and extending features more difficult in the long run.
3. Postman has been incredibly useful for testing API requests efficiently. It allows me to send requests, inspect responses, and automate testing without having to manually interact with the application. Features like saved collections, environment variables, and request chaining make it a great tool for team-based projects.

#### Reflection Publisher-3
1. In this tutorial, we are using the Push model of the Observer Pattern because the publisher sends notifications to all subscribers when an event happens, like when a product is created or deleted. The subscribers don’t request updates, they just receive them automatically.
2. If we used the Pull model instead, subscribers would have to constantly check for updates instead of getting notified right away. The advantage of Pull is that it reduces unnecessary notifications, but the downside is that subscribers might miss updates if they don’t check often enough, and it adds more work for them to keep checking.
3. Without multi-threading, the notification process would become slow and block the entire server whenever a product update happens. Each subscriber update would have to wait for the previous one to finish, making the system much less efficient, especially if there are many subscribers.