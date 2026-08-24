# Smart Inventory Manager — API Reference

## 1. Overview

Smart Inventory Manager exposes a REST API for managing products stored in MongoDB.

The API is implemented using Express.js.

The current API focuses on product CRUD operations.

---

## 2. Base URL

For local development:

```text
http://localhost:5000/api
```

Therefore, the product collection endpoint is:

```text
http://localhost:5000/api/products
```

---

## 3. Authentication

The current API does not require authentication.

Requests can be made without:

```text
Authorization
```

headers.

There is currently no implemented:

- JWT authentication
- User login
- User registration
- Role-based authorization
- Permission system

Authentication can be introduced as a future extension.

---

## 4. Response Format

The API returns JSON responses.

Successful product responses contain product data.

Error responses generally contain an `error` field.

Example:

```json
{
  "error": "Product not found"
}
```

---

# Products API

## 5. Get All Products

### Endpoint

```http
GET /products
```

### Description

Returns all products currently stored in the inventory.

### Authentication

Not required.

### Request

```bash
curl http://localhost:5000/api/products
```

### Successful Response

```http
200 OK
```

Example:

```json
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "name": "Laptop",
    "quantity": 5,
    "category": "Electronics",
    "price": 50000,
    "createdAt": "2026-01-25T10:30:00.000Z",
    "updatedAt": "2026-01-25T10:30:00.000Z"
  },
  {
    "_id": "507f1f77bcf86cd799439012",
    "name": "Mouse",
    "quantity": 50,
    "category": "Accessories",
    "price": 500,
    "createdAt": "2026-01-25T10:35:00.000Z",
    "updatedAt": "2026-01-25T10:35:00.000Z"
  }
]
```

### Error Response

```http
500 Internal Server Error
```

Example:

```json
{
  "error": "Server error message"
}
```

---

## 6. Create a Product

### Endpoint

```http
POST /products
```

### Description

Creates a new product in the inventory.

### Authentication

Not required.

### Request Headers

```http
Content-Type: application/json
```

### Request Body

```json
{
  "name": "Keyboard",
  "quantity": 20,
  "category": "Accessories",
  "price": 2000
}
```

### Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | String | Yes | Product name |
| `quantity` | Number | Yes | Initial stock quantity |
| `category` | String | Yes | Product category |
| `price` | Number | Yes | Product price |

### Request Example

```bash
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Keyboard",
    "quantity": 20,
    "category": "Accessories",
    "price": 2000
  }'
```

### Successful Response

```http
201 Created
```

Example:

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "name": "Keyboard",
  "quantity": 20,
  "category": "Accessories",
  "price": 2000,
  "createdAt": "2026-01-25T10:30:00.000Z",
  "updatedAt": "2026-01-25T10:30:00.000Z"
}
```

### Validation Errors

```http
400 Bad Request
```

Example:

```json
{
  "error": "Product validation failed: quantity must be >= 0"
}
```

---

## 7. Get a Product by ID

### Endpoint

```http
GET /products/:id
```

### Description

Returns a single product using its MongoDB ObjectId.

### Authentication

Not required.

### Path Parameter

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | String/ObjectId | Yes | MongoDB product identifier |

### Request Example

```bash
curl http://localhost:5000/api/products/507f1f77bcf86cd799439011
```

### Successful Response

```http
200 OK
```

Example:

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "name": "Laptop",
  "quantity": 10,
  "category": "Electronics",
  "price": 50000,
  "createdAt": "2026-01-25T10:30:00.000Z",
  "updatedAt": "2026-01-25T10:30:00.000Z"
}
```

### Product Not Found

```http
404 Not Found
```

Example:

```json
{
  "error": "Product not found"
}
```

---

## 8. Update a Product

### Endpoint

```http
PUT /products/:id
```

### Description

Updates an existing product.

The update can modify one or more supported product fields.

### Authentication

Not required.

### Path Parameter

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | String/ObjectId | Yes | MongoDB product identifier |

### Request Headers

```http
Content-Type: application/json
```

### Request Body

Example partial update:

```json
{
  "quantity": 8,
  "price": 75000
}
```

Another example:

```json
{
  "name": "Gaming Laptop",
  "quantity": 8,
  "category": "Electronics",
  "price": 75000
}
```

### Request Example

```bash
curl -X PUT http://localhost:5000/api/products/507f1f77bcf86cd799439011 \
  -H "Content-Type: application/json" \
  -d '{
    "quantity": 8,
    "price": 75000
  }'
```

### Successful Response

```http
200 OK
```

Example:

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "name": "Gaming Laptop",
  "quantity": 8,
  "category": "Electronics",
  "price": 75000,
  "createdAt": "2026-01-25T10:30:00.000Z",
  "updatedAt": "2026-01-25T10:50:00.000Z"
}
```

### Error Responses

#### Product Not Found

```http
404 Not Found
```

```json
{
  "error": "Product not found"
}
```

#### Validation Failure

```http
400 Bad Request
```

Example:

```json
{
  "error": "Product validation failed"
}
```

---

## 9. Delete a Product

### Endpoint

```http
DELETE /products/:id
```

### Description

Deletes an existing product from the inventory.

### Authentication

Not required.

### Path Parameter

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | String/ObjectId | Yes | MongoDB product identifier |

### Request Example

```bash
curl -X DELETE http://localhost:5000/api/products/507f1f77bcf86cd799439011
```

### Successful Response

```http
200 OK
```

Example:

```json
{
  "message": "Product deleted"
}
```

### Product Not Found

```http
404 Not Found
```

Example:

```json
{
  "error": "Product not found"
}
```

---

# 10. Product Data Model

A product follows the Mongoose document structure.

```json
{
  "_id": "MongoDB ObjectId",
  "name": "Product name",
  "quantity": 10,
  "category": "Category",
  "price": 1000,
  "createdAt": "ISO timestamp",
  "updatedAt": "ISO timestamp"
}
```

---

# 11. Validation Rules

The product API expects the following values.

| Field | Rules |
|---|---|
| `name` | Required, non-empty string |
| `quantity` | Required, numeric, greater than or equal to `0` |
| `category` | Required, non-empty string |
| `price` | Required, numeric, greater than or equal to `0` |

Example invalid request:

```json
{
  "name": "Laptop",
  "quantity": -5,
  "category": "Tech",
  "price": 50000
}
```

Expected result:

```http
400 Bad Request
```

---

# 12. HTTP Status Codes

| Status | Meaning |
|---|---|
| `200` | Request completed successfully |
| `201` | Product created successfully |
| `400` | Invalid request or validation failure |
| `404` | Product does not exist |
| `500` | Server or database error |

---

# 13. API Request Flow

The general request flow is:

```text
Client
  |
  | HTTP request
  v
Express Server
  |
  v
Product Route
  |
  v
Mongoose Product Model
  |
  v
MongoDB
  |
  v
JSON response
  |
  v
Client
```

---

# 14. API Testing Examples

## Get Products

```bash
curl http://localhost:5000/api/products
```

## Create Product

```bash
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test Product",
    "quantity": 10,
    "category": "Test",
    "price": 100
  }'
```

## Update Product

```bash
curl -X PUT http://localhost:5000/api/products/YOUR_PRODUCT_ID \
  -H "Content-Type: application/json" \
  -d '{
    "quantity": 20
  }'
```

## Delete Product

```bash
curl -X DELETE http://localhost:5000/api/products/YOUR_PRODUCT_ID
```

---

# 15. API Limitations

The current API does not implement:

- Authentication
- Authorization
- Pagination
- Search
- Filtering
- Sorting
- Bulk operations
- Stock transaction history
- Supplier endpoints
- Category endpoints
- User endpoints
- Reporting endpoints

These should not be documented as current functionality.

---

# 16. Potential Future API Extensions

Future versions could introduce endpoints such as:

```text
POST   /api/auth/register
POST   /api/auth/login

GET    /api/products?search=
GET    /api/products?category=
GET    /api/products?lowStock=true

GET    /api/categories
POST   /api/categories

GET    /api/inventory/transactions
POST   /api/inventory/transactions

GET    /api/suppliers
POST   /api/suppliers

GET    /api/dashboard
```

These are proposed extensions only and are not part of the current implementation.
