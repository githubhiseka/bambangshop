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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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

1. *In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or `trait` in Rust) in this BambangShop case, or a single Model `struct` is enough?*

    For the current case, I believe there is no need to use an interface (`trait`), and a single Model `struct` is sufficient because there is only one observer, which is the `Subscriber` class. Using an interface would be beneficial if we had multiple observers of different types.

2. *`id` in `Program` and `url` in `Subscriber` is intended to be unique. Explain based on your understanding, is using `Vec` (list) sufficient or using `DashMap` (map/dictionary) like we currently use is necessary for this case?*

    I think `DashMap` is the better option here. If we were to use a `Vec`, we would need two separate lists to store `id`s and `url`s, and searching for a matching pair would require iterating through the list manually. In contrast, `DashMap` allows us to store both in a single structure, making lookups more efficient. Furthermore, `DashMap` supports concurrent access, which makes it a more scalable and thread-safe choice if the application needs to handle multiple threads in the future.

3. *When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (`SUBSCRIBERS`) static variable, we used the `DashMap` external library for **thread safe** `HashMap`. Explain based on your understanding of design patterns, do we still need `DashMap` or we can implement Singleton pattern instead?*

    Since `BambangShop` operates in a multi-threaded environment, `DashMap` is the preferred choice over the Singleton pattern. The key reason is that `DashMap` is a thread-safe `HashMap` that allows multiple threads to access and modify data concurrently without manual synchronization. This ensures efficient and safe management of the `SUBSCRIBERS` list.

    On the other hand, implementing a Singleton would mean maintaining a single instance throughout the application’s lifecycle. However, in a multi-threaded scenario, we would need to introduce locking mechanisms to prevent data races, which could lead to performance bottlenecks and potential deadlocks. Given these concerns, `DashMap` provides a more efficient and scalable approach for handling concurrent data access.

---

#### Reflection Publisher-2

1. *In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?*

    Dividing Service and Repository from the Model helps create a more structured, scalable, and maintainable application. Following the Single Responsibility Principle (SRP), each layer handles a distinct concern—business logic is managed in the Service layer, while data persistence is handled by the Repository layer. This separation improves code organization, making it easier to test, modify, and debug without tightly coupling different functionalities. It also enhances flexibility, allowing changes to one layer without directly affecting the others.

2. *What happens if we only use the Model? Explain your imagination on how the interactions between each model (`Program`, `Subscriber`, `Notification`) affect the code complexity for each model?*

    If we only use the Model without separating concerns, the codebase will become difficult to manage and modify. The interdependencies between `Program`, `Subscriber`, and `Notification` will create strong coupling, meaning that any change in one part of the system could trigger a chain reaction of modifications elsewhere. This lack of separation between business logic and data access will make the system harder to scale, test, and debug, ultimately reducing flexibility and maintainability.

3. *Have you explored more about **Postman**? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.*

    Postman is a powerful tool for API testing, allowing me to send requests and verify if the responses are correct. One of its standout features is Collections, which helps in structuring requests into organized groups. Another useful feature is Environments, which lets me store reusable variables, making testing more efficient and minimizing manual effort. These features greatly enhance the testing workflow, ensuring a smoother development process.

---

#### Reflection Publisher-3
