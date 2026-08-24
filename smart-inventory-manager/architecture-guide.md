# Smart Inventory Manager — Architecture Guide

## 1. Overview

Smart Inventory Manager uses a simple three-tier web application architecture.

```text
┌──────────────────────────────┐
│        React Frontend        │
│                              │
│  Product UI                  │
│  Forms                       │
│  Product List                │
│  Product Cards               │
└──────────────┬───────────────┘
               │
               │ HTTP / REST
               ▼
┌──────────────────────────────┐
│       Express.js API         │
│                              │
│  Product Routes              │
│  Request Handling            │
│  Validation                  │
│  Error Handling              │
└──────────────┬───────────────┘
               │
               │ Mongoose
               ▼
┌──────────────────────────────┐
│          MongoDB             │
│                              │
│       Product Documents      │
└──────────────────────────────┘
```

Each layer has a separate responsibility.

---

## 2. Frontend Architecture

The frontend is implemented using React.

Its primary responsibility is presenting and managing the product inventory interface.

The frontend handles:

- Product display
- Product creation
- Product editing
- Product deletion
- API communication
- Form interaction
- UI state

The frontend communicates with the backend using HTTP requests.

---

## 3. Frontend Component Structure

The application contains React components responsible for different parts of the inventory interface.

The primary component responsibilities include:

### Application Component

Acts as the main frontend entry point and coordinates the inventory interface.

### Product List

Responsible for displaying the collection of products retrieved from the backend.

### Product Card

Represents an individual product and exposes actions such as editing or deleting the product.

### Add Product Form

Collects product information from the user.

Typical fields include:

```text
Name
Quantity
Category
Price
```

The form submits the information to the backend API.

---

## 4. Frontend Request Flow

The frontend follows this general request pattern:

```text
React Component
      |
      v
HTTP Request
      |
      v
Express API
      |
      v
JSON Response
      |
      v
React State
      |
      v
UI Re-render
```

For example, loading products follows:

```text
Product List
     |
     | GET /api/products
     v
Express API
     |
     v
MongoDB
     |
     v
Product JSON
     |
     v
React
```

---

## 5. Backend Architecture

The backend is implemented using Node.js and Express.js.

The Express server provides the application's REST API.

Its responsibilities include:

- HTTP request handling
- Routing
- Product operations
- Input validation
- Database communication
- Error responses
- CORS handling
- Environment configuration

The backend acts as the only application layer that communicates with MongoDB.

---

## 6. Express Request Lifecycle

A typical API request follows this sequence:

```text
HTTP Request
     |
     v
Express Server
     |
     v
Route Handler
     |
     v
Product Model
     |
     v
MongoDB
     |
     v
Result
     |
     v
JSON Response
```

For example:

```text
PUT /api/products/:id
        |
        v
Express
        |
        v
Product update operation
        |
        v
Mongoose
        |
        v
MongoDB
        |
        v
Updated document
        |
        v
JSON response
```

---

## 7. Database Architecture

MongoDB is used as the persistence layer.

Products are stored as MongoDB documents.

Mongoose provides the object modeling layer between the Express application and MongoDB.

The relationship is:

```text
Express
   |
   v
Mongoose Product Model
   |
   v
MongoDB Collection
   |
   v
Product Documents
```

---

## 8. Product Data Model

The product model contains the core inventory fields.

| Field | Type | Required | Description |
|---|---|---|---|
| `_id` | ObjectId | Automatic | MongoDB document identifier |
| `name` | String | Yes | Product name |
| `quantity` | Number | Yes | Current quantity |
| `category` | String | Yes | Product category |
| `price` | Number | Yes | Product price |
| `createdAt` | Date | Automatic | Creation timestamp |
| `updatedAt` | Date | Automatic | Last update timestamp |

Example document:

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

---

## 9. CRUD Architecture

The application's primary business operation is product CRUD.

### Create

```text
POST /api/products
```

Creates a new product.

### Read

```text
GET /api/products
GET /api/products/:id
```

Retrieves all products or a specific product.

### Update

```text
PUT /api/products/:id
```

Updates an existing product.

### Delete

```text
DELETE /api/products/:id
```

Deletes an existing product.

---

## 10. Create Product Flow

```text
User
 |
 | enters product data
 v
React Form
 |
 | POST /api/products
 v
Express
 |
 | validates data
 v
Mongoose Product
 |
 v
MongoDB
 |
 | created document
 v
Express
 |
 | JSON response
 v
React
```

---

## 11. Read Product Flow

For all products:

```text
React
 |
 | GET /api/products
 v
Express
 |
 v
Mongoose
 |
 v
MongoDB
 |
 v
Product collection
 |
 v
JSON response
 |
 v
React Product List
```

For a specific product:

```text
GET /api/products/:id
```

The supplied MongoDB ObjectId is used to locate the document.

---

## 12. Update Product Flow

```text
User
 |
 | edits product
 v
React
 |
 | PUT /api/products/:id
 v
Express
 |
 v
Mongoose
 |
 v
MongoDB
 |
 | updated document
 v
Express
 |
 v
React
```

The update operation can modify fields such as:

- Name
- Quantity
- Category
- Price

---

## 13. Delete Product Flow

```text
User
 |
 | selects delete
 v
React
 |
 | DELETE /api/products/:id
 v
Express
 |
 v
Mongoose
 |
 v
MongoDB
 |
 | deletion result
 v
Express
 |
 v
React
```

---

## 14. Validation Architecture

Validation prevents invalid inventory data from being stored.

The current product validation rules include:

```text
name
    required
    non-empty string

quantity
    required
    numeric
    >= 0

category
    required
    non-empty string

price
    required
    numeric
    >= 0
```

Invalid requests return a `400 Bad Request` response.

---

## 15. Error Handling Architecture

The backend communicates failures using HTTP status codes.

```text
400
 |
 +-- Invalid input
 |
404
 |
 +-- Product does not exist
 |
500
 |
 +-- Server/database failure
```

Example:

```json
{
  "error": "Product not found"
}
```

---

## 16. Environment Configuration

The backend uses environment variables for server and database configuration.

Typical configuration includes:

```env
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/inventory_db
PORT=5000
NODE_ENV=development
```

The `dotenv` package is used to load environment configuration.

---

## 17. CORS

The backend uses CORS middleware to allow the frontend to communicate with the API from a different origin during development.

This is required because the React development server and Express API can run on different ports.

Typical development configuration:

```text
React
localhost:3000

Express
localhost:5000
```

---

## 18. Deployment Architecture

The application can be deployed as separate frontend and backend services.

Conceptually:

```text
Browser
   |
   v
React Frontend
   |
   | HTTPS REST requests
   v
Express Backend
   |
   | MongoDB connection
   v
MongoDB
```

The MongoDB connection string should be provided through environment configuration rather than hard-coded into application source code.

---

## 19. Architectural Strengths

The current architecture provides:

- Clear separation between frontend and backend
- REST-based communication
- Dedicated database layer
- Mongoose schema modeling
- Simple CRUD workflow
- Straightforward development setup
- Easy extension of product functionality

---

## 20. Architectural Limitations

The current architecture does not yet provide:

- Authentication
- Authorization
- Role-based access
- Dedicated service layer
- Repository abstraction
- Pagination
- Search
- Filtering
- Inventory transaction modeling
- Supplier management
- Category management
- Audit history

These are potential architectural extensions rather than implemented components.

---

## 21. Future Architecture

As the application grows, the backend could evolve toward:

```text
React Frontend
      |
      v
Express Routes
      |
      v
Controllers
      |
      v
Services
      |
      v
Repositories / Models
      |
      v
MongoDB
```

Additional domain modules could then be introduced for:

```text
Users
Products
Categories
Suppliers
Inventory Transactions
Reports
```

Authentication and authorization could also be introduced at the API boundary.
