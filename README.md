# Project Introduction



# Intro to Online Boutique App



This is a type of e-commerce platform, but unlike Amazon-type stores, it focuses on:



- **Niche or curated products**

- **Unique / limited collections**

- **Strong brand identity & style**



Think of it as a **digital version of a small, stylish fashion store**.



But from a **technical perspective**, modern boutique apps are **not built as a single application**.



They are built using **Microservices Architecture**.

[!TIP]
# What is Microservices?

**Microservices** is an architectural style where an application is broken into **small, independent services**, and each service:

- Handles a **specific business function**
- Runs independently
- Communicates via APIs

👉 Instead of one big application (monolith), you have **multiple small services working together**.

---

# Online Boutique = Microservices in Action

This online boutique app is made up of multiple services like:

### 🧾 Product Catalog Service

- Manages product list, categories, pricing

### 🛒 Cart Service

- Handles user cart (add/remove items)

### 💳 Payment Service

- Processes payments (UPI, cards)

### 📦 Order Service

- Manages order lifecycle

### 👤 Frontend Service

- Authentication & profiles

### 🚚 Shipping Service

- Delivery tracking & logistics

### Etc..

---

# How These Services Communicate

- REST APIs (HTTP)
- gRPC (faster internal communication)
- Message queues (Kafka / RabbitMQ)

👉 Example:

- Cart service → calls Product service
- Order service → calls Payment service

---

# Monolith vs Microservices

### Monolithic App ❌

- Everything in one codebase
- Hard to scale
- Single failure affects whole system

### Microservices App ✅

- Independent services
- Easy to scale
- Fault isolation

👉 That’s why modern apps (like boutique apps) >use microservices.





# **Architecture**



**Online Boutique** is composed of 11 microservices written in different languages that talk to each other over gRPC.



![Architecture01](images/Architecture01.png)



| **Service** | **Language** | **Description** |

| --- | --- | --- |

| [frontend](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/frontend) | Go | Exposes an HTTP server to serve the website. Handles user sessions and displays product catalog. |

| [cartservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/cartservice) | C# | Stores the items in the user's shopping cart in Redis. Manages add/remove/view cart operations. |

| [productcatalogservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/productcatalogservice) | Go | Provides the list of products from a JSON file. Returns product details, pricing, and inventory. |

| [currencyservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/currencyservice) | Node.js | Converts one money amount to another currency. Provides real-time exchange rate conversions. |

| [paymentservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/paymentservice) | Node.js | Charges the given credit card info (mock implementation). Simulates payment processing without real transactions. |

| [shippingservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/shippingservice) | Go | Gives shipping cost estimates based on shopping cart contents and destination. Calculates delivery logistics. |

| [emailservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/emailservice) | Python | Sends users an order confirmation email (mock implementation). Logs email delivery attempts. |

| [checkoutservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/checkoutservice) | Go | Retrieves user cart, prepares order and orchestrates payment, shipping and notification services. |

| [recommendationservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/recommendationservice) | Python | Recommends other products based on user browsing and shopping history. Provides personalized suggestions. |

| [adservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/adservice) | Java | Provides text ads based on given context words. Returns relevant advertisements for product pages. |

| [loadgenerator](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/loadgenerator) | Python/Locust | Continuously sends requests imitating realistic user behavior. Generates synthetic traffic for testing. |



Screenshots:

![image.png](images/webpage.png)



---



**Most services are stateless**, and **only the cart uses persistence (Redis)**. Let's break it down cleanly.



# How data works in `microservices`



This project is **designed** to:



- Demonstrate **microservice communication**

- Be **easy to deploy anywhere**

- Avoid complex database ops



So it uses **minimal persistence** on purpose.



---



## Service-by-Service Data Breakdown



### ✅ **cartservice** → ✔️ HAS persistence



**Storage used:**



- **Redis**



**What's stored:**



- User cart items

- Quantity, product IDs



**Why Redis?**



- Fast

- Simple

- Easy to reset

- No schema complexity



📌 In Kubernetes:



- Redis runs as a pod (or StatefulSet)

- Cart data is lost if Redis is deleted (by default)



---



### ❌ **orders / checkout** → NO real database



There is **NO dedicated "orders database"**.



**checkoutservice:**



- Aggregates data from:

    - cartservice

    - paymentservice

    - shippingservice

    - emailservice

- Simulates order placement

- Does **not persist orders**



👉 This is **by design**, to keep the demo lightweight.



---



### ❌ **productcatalogservice**



**Storage:**



- Static JSON file

- Loaded into memory at startup



**No DB**



- Products reset on restart



---



### ❌ **recommendationservice**



**Storage:**



- Stateless

- Generates recommendations dynamically



---



### ❌ **paymentservice**



**Storage:**



- None

- Fake payment processor



---



### ❌ **shippingservice**



**Storage:**



- None

- Simulated shipping cost logic



---



### ❌ **emailservice**



**Storage:**



- None

- Just logs "email sent"



---



### ❌ **adservice**



**Storage:**



- In-memory ad data

- No persistence



---



### ❌ **frontend**



**Storage:**



- Stateless

- Just UI + API calls



---



### ❌ **currencyservice**



**Storage:**



- Static exchange rates

- In-memory only



---



## SUMMARY TABLE



| **Service** | **Persistent Storage** | **Type** | **Language** |

| --- | --- | --- | --- |

| [cartservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/cartservice) | ✅ Yes | Redis | C# |

| [checkoutservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/checkoutservice) | ❌ No | Stateless | Go |

| [productcatalogservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/productcatalogservice) | ❌ No | In-memory JSON | Go |

| [recommendationservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/recommendationservice) | ❌ No | Stateless | Python |

| [paymentservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/paymentservice) | ❌ No | Fake | Node.js |

| [shippingservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/shippingservice) | ❌ No | Fake | Go |

| [emailservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/emailservice) | ❌ No | Fake | Python |

| [adservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/adservice) | ❌ No | In-memory | Java |

| [frontend](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/frontend) | ❌ No | Stateless | Go |

| [currencyservice](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/currencyservice) | ❌ No | In-memory | Node.js |

| [loadgenerator](https://github.com/vinaypo/GitOps-Driven-Microservices/tree/main/src/loadgenerator) | ❌ No | Stateless | Python/Locust |



---



"The demo intentionally keeps most services stateless to simplify deployment and focus on platform concerns like CI/CD, observability, scaling, and networking."
 



---



# Project Architecture



![Gitops_Project](images/Gitops_Project.gif)
![Gitops_Project](images/Gitops_Project.png)


---

# Building and Pushing the docker images to GHCR (GitHub Container Registry)

<details>

<summary>Click to get the steps</summary>
### Step-1: Create the token
Create a PAT classic token with the below permissions.

Give permissions:

```
Packages → Read&Write
```

If private repo add the below as well:

```
Contents →Read
```

### Step-2: Login to GHCR to push the docker images

```bash
echo <TOKEN> | docker login ghcr.io -u USERNAME --password-stdin
```

### Step-3: Build the docker image by using the dockerfile in the particular folder

```
docker build -t ghcr.io/vinaypo/microservices/adservice:v0.10.4 adservice/
```

### step-4: Push the docker image to the ghcr repository

```
docker push ghcr.io/vinaypo/microservices/adservice:v0.10.4
```

## Once we have the images in the github packages, connect them to the repository.

Click on the profile, go to the packages --> package settings --> add the repository & give the role:"admin or write or read".

- read: Can download the package and read package metadata
- write: Can download the package and do both read and write package metadata
- admin: Can upload, download, manage, read, write package metadata and delete, restore packages.

Manage Actions access to which you want to give the package access
![image.png](images/pck2.png)

Select the repository and give the right Role.
![image.png](images/pck1.png)
![image.png](images/pck3.png)

---

