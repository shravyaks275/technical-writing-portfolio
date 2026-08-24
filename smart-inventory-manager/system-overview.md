# Smart Inventory Manager — System Overview

## 1. Introduction

Smart Inventory Manager is a web-based inventory management application for managing product and inventory information through a simple web interface.

The application follows a client-server architecture consisting of:

- A React frontend responsible for the user interface.
- An Express.js backend responsible for REST API operations.
- A MongoDB database accessed through Mongoose.

The current implementation focuses on product-level inventory management and provides CRUD operations for products.

The system allows users to:

- View products
- Add products
- View individual product details
- Edit existing products
- Delete products
- Maintain product quantity
- Maintain product category
- Maintain product price

The application is intentionally lightweight and focuses on the core inventory management workflow rather than implementing a complete enterprise inventory platform.

---

## 2. System Purpose

The primary purpose of Smart Inventory Manager is to provide a simple interface for maintaining inventory records.

The application replaces manual product tracking with a web-based CRUD workflow.

The main interaction flow is:

```text
User
  |
  v
React Frontend
  |
  | HTTP request
  v
Express REST API
  |
  v
Mongoose
  |
  v
MongoDB
```

The frontend does not communicate directly with MongoDB. All database operations are performed by the backend API.

---

## 3. Current Scope

The current implementation is centered around a single primary domain entity:

- Product

A product contains the following information:

| Field | Description |
|---|---|
| `_id` | MongoDB-generated unique identifier |
| `name` | Product name |
| `quantity` | Available inventory quantity |
| `category` | Product category |
| `price` | Product price |
| `createdAt` | Creation timestamp |
| `updatedAt` | Last modification timestamp |

The backend exposes REST endpoints that allow the frontend to perform complete CRUD operations against this entity.

---

## 4. Key Features

### 4.1 Product Creation

Users can add a new product through the frontend.

The product form accepts:

- Name
- Quantity
- Category
- Price

The frontend sends the information to:

```text
POST /api/products
```

The backend validates the product data and creates a MongoDB document through the Mongoose model.

---

### 4.2 Product Listing

The application retrieves the available products through:

```text
GET /api/products
```

The returned product collection is consumed by the React frontend and displayed in the inventory interface.

---

### 4.3 Product Details

A specific product can be retrieved using its MongoDB identifier:

```text
GET /api/products/:id
```

The backend uses the supplied ID to locate the corresponding product document.

---

### 4.4 Product Editing

Existing products can be updated using:

```text
PUT /api/products/:id
```

The update operation supports product fields such as:

- Name
- Quantity
- Category
- Price

The update is performed against the existing MongoDB document.

---

### 4.5 Product Deletion

Products can be removed using:

```text
DELETE /api/products/:id
```

The backend locates the product using its MongoDB ID and removes it from the database.

---

## 5. User Interaction Flow

The primary application workflow is:

```text
Open Application
      |
      v
Load Product List
      |
      v
View Inventory
      |
      +--------------------+
      |                    |
      v                    v
 Add Product          Select Product
      |                    |
      v                    +-------> Edit Product
 Save Product             |
      |                    +-------> Delete Product
      v
Refresh Product List
```

The application therefore follows a straightforward CRUD-oriented interaction model.

---

## 6. Technology Stack

### Frontend

- React
- React DOM
- React Scripts
- Axios / HTTP client functionality
- Tailwind CSS
- PostCSS

### Backend

- Node.js
- Express.js
- CORS
- dotenv

### Database

- MongoDB
- Mongoose
- MongoDB Node.js driver

### Development Tools

Recommended development tools include:

- Git
- Visual Studio Code
- Postman
- MongoDB Compass

---

## 7. High-Level Architecture

Smart Inventory Manager uses a three-layer architecture.

### Presentation Layer

The React application provides the user interface.

Responsibilities include:

- Rendering products
- Collecting product information
- Displaying forms
- Handling user interactions
- Sending HTTP requests
- Updating the displayed product state

### Application/API Layer

The Express server provides the REST API.

Responsibilities include:

- Receiving HTTP requests
- Validating request data
- Executing product operations
- Returning HTTP responses
- Handling API errors
- Communicating with MongoDB

### Data Layer

MongoDB stores product documents.

Mongoose provides the application-level model used to interact with the database.

---

## 8. Data Flow

### Product Creation

```text
User enters product information
        |
        v
React product form
        |
        v
POST /api/products
        |
        v
Express route
        |
        v
Product validation
        |
        v
Mongoose Product model
        |
        v
MongoDB
        |
        v
Created product returned
        |
        v
React interface updated
```

### Product Update

```text
User selects product
        |
        v
Edit product information
        |
        v
PUT /api/products/:id
        |
        v
Express API
        |
        v
Mongoose
        |
        v
MongoDB
        |
        v
Updated product
        |
        v
Frontend
```

### Product Deletion

```text
User selects delete
        |
        v
DELETE /api/products/:id
        |
        v
Express API
        |
        v
MongoDB
        |
        v
Deletion result
        |
        v
Frontend refresh
```

---

## 9. Validation

Product data is expected to follow these rules:

| Field | Requirement |
|---|---|
| `name` | Required, non-empty string |
| `quantity` | Required, numeric value greater than or equal to zero |
| `category` | Required, non-empty string |
| `price` | Required, numeric value greater than or equal to zero |

Invalid product information results in an HTTP `400` response.

---

## 10. Error Handling

The API uses HTTP status codes to communicate operation results.

Common responses include:

| Status | Meaning |
|---|---|
| `200` | Request completed successfully |
| `201` | Product successfully created |
| `400` | Invalid or failed validation |
| `404` | Requested product does not exist |
| `500` | Server or database failure |

Example:

```json
{
  "error": "Product not found"
}
```

---

## 11. Security and Authentication Scope

The current API does not implement user authentication or authorization.

The product endpoints are publicly accessible.

There is no implemented:

- JWT authentication
- Login system
- User management
- Role-based access control
- Permission system

Authentication and authorization would therefore need to be added before exposing the current API as a production multi-user inventory platform.

---

## 12. Current API Limitations

The current implementation focuses on basic product CRUD.

The API does not currently implement:

- Authentication
- Authorization
- Pagination
- Product search
- Product filtering
- Product sorting
- Bulk operations
- Inventory transaction history
- Supplier management
- Category management endpoints
- User management
- Reporting
- Advanced analytics

These should be considered potential future extensions rather than current features.

---

## 13. Potential Future Improvements

Possible future enhancements include:

### Authentication

Add:

- User registration
- Login
- JWT authentication
- Role-based authorization

### Inventory Management

Add:

- Stock-in transactions
- Stock-out transactions
- Inventory history
- Low-stock alerts
- Stock adjustment records

### Product Management

Add:

- Product search
- Category filtering
- Sorting
- Pagination
- Bulk product operations

### Additional Entities

Potential future entities include:

- Categories
- Suppliers
- Users
- Inventory transactions

### Reporting

Add:

- Inventory dashboards
- Low-stock reports
- Product value reports
- Inventory movement reports

---

## 14. Project Positioning

Smart Inventory Manager is best described as a lightweight MERN-style inventory CRUD application.

Its strongest implemented capability is the complete product management lifecycle:

```text
Create
  ↓
Read
  ↓
Update
  ↓
Delete
```

The project demonstrates the integration of a React frontend with an Express REST API and MongoDB persistence layer.
