# Introduction
**Cloudble Connect** simplifies integration of different business flows between Providers/Distributors and Vendors systems. As a platform it handles the integration of complex business scenarios and provides unified place of communication. That's why API's are very important part of our day-to-day work and our teams are focused on providing powerfull and simple API's for our integrators. Providers/Distributors usually use some eCommerce systems to sell services. In this task you are going to write a simple headless eCommerce system, that possibly can be integrated with **Cloudblue Connect** platform. eCommerce systems automate a lot of business scenarios. Your task is limited with only one flow: purchasing of new products (what is usually called order flow). As a result you should provide a Web Application, that allows to operate with ***Orders*** by making http calls to application's API.

# Order Flow
Let's first understand what term *Order* means in our eCommerce system and what business process it has.
1. ***Order*** is an entity that provides information about what user want to buy
2. One ***Order*** can contain different products with different quantity and different prices. The entity, that includes this information in ***Order*** is called ***Order Detail***
3. ***Order*** can be bound with entity, that represents same ***Order*** in some external system. For example it can be **Cloudblue Connect**. It is needed for simplification of tracking ***Order*** in different systems
4. ***Order*** is created in status *'new'*
5. User, who process ***Orders***, can move them to status *'accepted'* or *'rejected'*
6. User cannot delete *'accepted'* ***Orders***

***Order*** flow can be described with simple state diagram below:
![001 Order flow diagram](/assets/001_order_flow/001_order_flow_diagram.png)

# Requirements and Solution Description
After understanding the business area, let's dive into technical implementation details and technical requirements.
Your goal is to create a simple Web Application, that should provide REST API with support of CRUD operations on ***Order*** entity

## General Techical Requirements
1. Programming language: python
2. Frameworks: Django + Django REST Framework
3. Web application should contain unit tests
4. Testing framework: unittest or pytest
3. Feel free to use any additional Django library
4. Use any SQL database you want

## General Solution Requirements
1. Web Application should be implemented as Open System - means that any additional unknown input field should be ignored and request should not fail
2. API should accept and return data only in JSON format
2. Collections List API should support paging
3. For Collections List API user should be able to define 'limit' and 'offset' query parameters for paging
4. Collections List API as a response body should return only list of entities
5. Collections List API should return paging information in header 'Content-Range' in format 'items <from-number>-<to-number>/<total>'
3. There should be proper handling of exceptions in API
4. There is no need for Authentication and Authorization processes
5. PUT should be implemented as a partial update
6. API should be covered with unit tests

## Solution Overview

### DB Structure
Database structure can be described with following schema:

![001 Order DB schema](/assets/001_order_flow/001_order_flow_db_schema.png)

> Note: Use DecimalField to store price in OrderDetail

### API description
Find detailed API description here

| HTTP Method | URL                 | Filters             | Ordering               | Response Code |
| ----------- | ----------------    | ------------------- | ---------------------- | ------------- |
| GET         | /api/v1/orders      | external_id, status | id, status, created_at | 200 |
| GET         | /api/v1/orders/<id> | - | - | 200 |
| POST        | /api/v1/orders      | - | - | 201 |
| PUT         | /api/v1/orders/<id> | - | - | 200 |
| DELETE      | /api/v1/orders/<id> | - | - | 204 |

> Note: User cannot delete *'accepted'* ***Orders***

Also to switch ***Order*** between statuses API should support custom nested actions
| HTTP Method | URL                 | Filters             | Ordering               | Response Code |
| ----------- | ----------------    | ------------------- | ---------------------- | ------------- |
| POST         | /api/v1/orders/<id>/accept      | - | - | 200 |
| POST         | /api/v1/orders/<id>/fail      | - | - | 200 |

Find detailed description of request/response bodies below. If there is no information for some API's treat it as request/response bodies are empty in that case

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

> Note: GET /api/v1/orders/<id> contains same fields in response, but returns particular ***Order*** instead list of ***Orders***

### POST /api/v1/orders Request Body
```json
[{
    "external_id": "PR-123-321-123",
    "details": [{
        "product": {"id": 4},
        "amount": 10,
        "price": "12.00"
    }, ...]
}, ...]
```

### POST /api/v1/orders Response Body
Response body should contain just created ***Order*** entity with order details information
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

### PUT /api/v1/orders/<id> Request Body
> Note: for ***Order*** User is able only to update *'external_id'* field. Other fields should be ignored even if they were passed in request body

> Note: Only ***Orders*** in status '*new*' are able to be updated

```json
[{
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
}, ...]
```

### PUT /api/v1/orders/<id> Response Body
Response body is same as response bodies for GET, POST operations

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
Here is a list of additional requirements for the this task. There will be nice to have them, but not required at all

1. Prepare Docker container for running your solution in local environment
2. Prepare Docker Compose with application and DB containers to run your solution in local environment
3. Prepare Docker Compose to run your solution's tests in Docker
