# Introduction
The **CloudBlue Connect** platform simplifies integration of different business flows between Providers/Distributors and Vendors systems. Namely, it handles the integration of complex business scenarios and provides unified place for communication. That's why APIs are very important part of our day-to-day work. In addition, our teams are focused on providing powerful and simple APIs for our integrators. Providers/Distributors usually use some eCommerce systems to sell services. In this task you are going to write a simple headless eCommerce system that could be integrated with the **Cloudblue Connect** platform. Since eCommerce systems automate a lot of business scenarios, your task is limited to only one flow: purchasing of new products (usually called the *Order Flow*). As a result, you should provide a Web Application that allows operating with ***Orders*** by making http calls to the application's API.

# Order Flow
First of all, it is important to understand what the term *Order* means in our eCommerce system and what business processes it involves:
1. ***Order*** is an entity that provides information about what customers want to buy.
2. One ***Order*** can contain different products with different quantity and different prices. The entity that includes this information in ***Order*** is called ***Order Detail***.
3. ***Order*** can be bound with an entity that represents the same ***Order*** in an external system. For example, such a system can be **Cloudblue Connect** itself. It is required to simplify tracking ***Orders*** in different systems.
4. ***Orders*** are created with the status *'new'*.
5. Users that process ***Orders*** can move them to the following statuses: *'accepted'* or *'rejected'*.
6. Users should not be able to delete *'accepted'* ***Orders***.

The ***Order*** flow can be described with a simple state diagram below:
![001 Order flow diagram](/assets/001_order_flow/001_order_flow_diagram.png)

# Requirements and Solution Description
After understanding the business area, let's dive into technical implementation details and technical requirements.
Your goal is to create a simple Web Application that should provide REST API with support of CRUD operations on ***Order*** entity.

## General Techical Requirements
1. Programming language: Python
2. Frameworks: Django + Django REST Framework
3. Web application should contain unit tests
4. Testing framework: unittest or pytest
3. Feel free to use any additional Django library
4. Use any SQL database you want

## General Solution Requirements
2. API should accept and return data only in the JSON format.
2. Collections List API should support Paging.
3. Collections List API should enable users to define 'limit' and 'offset' query parameters for Paging.
4. A response body of Collections List API should return only list of entities.
5. Collections List API should return paging information in a header 'Content-Range' via the following format: 'items <from-number>-<to-number>/<total>'.
3. There should be proper handling of exceptions in the API.
4. There is no need for Authentication and Authorization processes.
5. PUT should be implemented as a partial update.
6. API should be covered with unit tests.

## Solution Overview

### DB Structure
Database structure can be described with the following schema:

![001 Order DB schema](/assets/001_order_flow/001_order_flow_db_schema.png)

> Note: Use DecimalField to store price in OrderDetail

### API description
Find detailed API description here

| HTTP Method | URL                 | Filters             | Ordering               | Response Code |
| ----------- | ----------------    | ------------------- | ---------------------- | ------------- |
| GET         | /api/v1/orders      | external_id, status | id, status, created_at | 200 |
| GET         | /api/v1/orders/{id} | - | - | 200 |
| POST        | /api/v1/orders      | - | - | 201 |
| PUT         | /api/v1/orders/{id} | - | - | 200 |
| DELETE      | /api/v1/orders/{id} | - | - | 204 |

> Note: Users should not be able to delete *'accepted'* ***Orders***.

Furthermore, API should support custom nested actions to switch ***Order*** between statuses.

| HTTP Method | URL                 | Filters             | Ordering               | Response Code |
| ----------- | ----------------    | ------------------- | ---------------------- | ------------- |
| POST        | /api/v1/orders/{id}/accept      | - | - | 200 |
| POST        | /api/v1/orders/{id}/fail      | - | - | 200 |

The following provides detailed description of request/response bodies. In case there is no information for some API, consider request/response bodies as empty.

### GET /api/v1/orders Response Body
```json
[{
    "id": 1,
    "status": "new",
    "created_at": "2021-01-01T00:00:00",
    "external_id": "PR-123-321-123",
    "details": [{
        "id": 1,
        "product": {"id": 4, "name": "Dropbox"},
        "amount": 10,
        "price": "12.00"
    }, ...]
}, ...]
```

> Note: GET /api/v1/orders/{id} contains same fields in response but returns a particular ***Order*** instead of an ***Order*** list.

### POST /api/v1/orders Request Body
```json
{
    "external_id": "PR-123-321-123",
    "details": [{
        "product": {"id": 4},
        "amount": 10,
        "price": "12.00"
    }, ...]
}
```

### POST /api/v1/orders Response Body
Response body should only contain a created ***Order*** entity with order details information.
```json
{
    "id": 1,
    "status": "new",
    "created_at": "2021-01-01T00:00:00",
    "external_id": "PR-123-321-123",
    "details": [{
        "id": 1,
        "product": {"id": 4, "name": "Dropbox"},
        "amount": 10,
        "price": "12.00"
    }, ...]
}
```

### PUT /api/v1/orders/{id} Request Body
> Note: Users should only be able to update the *'external_id'* field for an ***Order***. Other fields should be ignored even if they were passed in request body.

> Note: It should be possible to update ***Orders*** only in the status '*new*'.

```json
{
    "id": 1,
    "status": "new",
    "created_at": "2021-01-01T00:00:00",
    "external_id": "PR-123-321-123-new",
    "details": [{
        "id": 1,
        "product": {"id": 4, "name": "Dropbox"},
        "amount": 10,
        "price": "12.00"
    }, ...]
}
```

### PUT /api/v1/orders/{id} Response Body
Response body is same as response bodies for GET, POST operations.

```json
{
    "id": 1,
    "status": "new",
    "created_at": "2021-01-01T00:00:00",
    "external_id": "PR-123-321-123",
    "details": [{
        "id": 1,
        "product": {"id": 4, "name": "Dropbox"},
        "amount": 10,
        "price": "12.00"
    }, ...]
}
```

# Optional Additional Requirements
Here is a list of additional requirements for this task. Note that these requirements are not mandatory, but it would be nice to implement them:

1. Prepare Docker container for running your solution in the local environment.
2. Prepare Docker Compose with application and DB containers to run your solution in the local environment.
3. Prepare Docker Compose to run your solution's tests in Docker.
    
