# Smart Inventory Manager — Developer Setup Guide

## 1. Overview

This guide explains how to install, configure, run, and verify Smart Inventory Manager in a local development environment.

The application consists of:

- React frontend
- Express.js backend
- MongoDB database

The frontend and backend are installed separately.

---

## 2. Prerequisites

Install the following before starting.

### Required

- Node.js
- npm
- Git
- MongoDB or MongoDB Atlas

### Recommended

- Visual Studio Code
- Postman
- MongoDB Compass

A minimum of approximately 4 GB RAM is recommended for comfortable local development.

---

## 3. Clone the Repository

Clone the repository:

```bash
git clone https://github.com/shravyaks275/Smart-Inventory-Manager.git
```

Navigate into the project:

```bash
cd Smart-Inventory-Manager
```

---

## 4. Project Structure

The project is divided into frontend and backend areas.

A simplified structure is:

```text
Smart-Inventory-Manager/
│
├── client/
│   └── src/
│       ├── components/
│       └── ...
│
├── server/
│   ├── models/
│   ├── routes/
│   ├── server.js
│   ├── package.json
│   └── .env
│
└── ...
```

The `client` directory contains the React application.

The `server` directory contains the Express API.

---

## 5. Backend Setup

Open a terminal and navigate to the server:

```bash
cd server
```

Install backend dependencies:

```bash
npm install
```

The backend uses packages including:

- Express
- Mongoose
- CORS
- dotenv
- MongoDB

---

## 6. Backend Environment Variables

Create an environment file:

```text
server/.env
```

Add the required configuration:

```env
MONGO_URI=mongodb+srv://username:password@cluster-name.mongodb.net/inventory_db?retryWrites=true&w=majority
PORT=5000
NODE_ENV=development
```

Replace the MongoDB connection information with the credentials for your own database.

Do not commit real database credentials to source control.

---

## 7. MongoDB Setup

The application requires MongoDB for persistent product storage.

You can use either:

- MongoDB Atlas
- A locally installed MongoDB instance

### MongoDB Atlas

Create a MongoDB Atlas cluster and obtain the connection string.

The connection string should be placed in:

```text
server/.env
```

Example:

```env
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/inventory_db
```

---

## 8. Local MongoDB

If MongoDB is installed locally, the connection string can point to the local MongoDB server.

Example:

```env
MONGO_URI=mongodb://localhost:27017/inventory_db
```

The exact URI depends on the local MongoDB configuration.

---

## 9. Start the Backend

From the `server` directory:

```bash
node server.js
```

If the project defines a development script, the corresponding npm command can also be used.

The API is configured to run on port:

```text
5000
```

The local API base URL is:

```text
http://localhost:5000/api
```

---

## 10. Frontend Setup

Open a second terminal.

Navigate to the client:

```bash
cd client
```

Install dependencies:

```bash
npm install
```

The frontend uses packages including:

- React
- React DOM
- React Scripts
- Axios
- Tailwind CSS
- PostCSS

---

## 11. Start the Frontend

From the `client` directory:

```bash
npm start
```

The React development server normally runs at:

```text
http://localhost:3000
```

---

## 12. Run the Full Application

Two terminal windows are recommended.

### Terminal 1 — Backend

```bash
cd Smart-Inventory-Manager/server
npm install
node server.js
```

### Terminal 2 — Frontend

```bash
cd Smart-Inventory-Manager/client
npm install
npm start
```

Then open:

```text
http://localhost:3000
```

The application should communicate with:

```text
http://localhost:5000/api
```

---

## 13. Verify the Backend

Test the products endpoint:

```bash
curl http://localhost:5000/api/products
```

A successful response should return a JSON array containing product records.

Example:

```json
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "name": "Laptop",
    "quantity": 5,
    "category": "Electronics",
    "price": 50000
  }
]
```

---

## 14. Verify Product Creation

Run:

```bash
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d "{\"name\":\"Keyboard\",\"quantity\":20,\"category\":\"Accessories\",\"price\":2000}"
```

A successful request should create a product in MongoDB.

---

## 15. Verify Product Retrieval

After creating a product, copy its `_id`.

Then run:

```bash
curl http://localhost:5000/api/products/YOUR_PRODUCT_ID
```

Replace:

```text
YOUR_PRODUCT_ID
```

with the actual MongoDB ObjectId.

---

## 16. Verify Product Update

Run:

```bash
curl -X PUT http://localhost:5000/api/products/YOUR_PRODUCT_ID \
  -H "Content-Type: application/json" \
  -d "{\"quantity\":25,\"price\":2500}"
```

The product should be updated in MongoDB.

---

## 17. Verify Product Deletion

Run:

```bash
curl -X DELETE http://localhost:5000/api/products/YOUR_PRODUCT_ID
```

The specified product should be removed.

---

## 18. Testing with Postman

The API can also be tested using Postman.

Set the base URL to:

```text
http://localhost:5000/api
```

Example requests:

```text
GET     /products
POST    /products
GET     /products/:id
PUT     /products/:id
DELETE  /products/:id
```

For `POST` and `PUT`, select:

```text
Body → raw → JSON
```

Example:

```json
{
  "name": "Laptop",
  "quantity": 10,
  "category": "Electronics",
  "price": 50000
}
```

---

## 19. Common Development Problems

### Backend does not start

Check:

- Node.js is installed.
- Dependencies were installed.
- Port `5000` is available.
- `server/.env` exists.
- `MONGO_URI` is configured.

---

### MongoDB connection fails

Check:

- MongoDB is running if using a local installation.
- MongoDB Atlas cluster is available.
- Username and password are correct.
- Network access is configured in MongoDB Atlas.
- The connection string is correct.

---

### Frontend does not start

Check:

```bash
npm install
```

inside the `client` directory.

Also verify that port `3000` is available.

---

### Frontend cannot communicate with backend

Verify that the backend is running:

```text
http://localhost:5000
```

Check browser developer tools for failed network requests.

Confirm that the frontend is using the correct API base URL.

---

### Product creation fails

Check the request body.

The expected fields are:

```json
{
  "name": "Product",
  "quantity": 10,
  "category": "Category",
  "price": 1000
}
```

Make sure:

- Name is not empty.
- Quantity is not negative.
- Category is not empty.
- Price is not negative.

---

### Product cannot be found

Verify that the MongoDB ObjectId is correct.

A request such as:

```text
GET /api/products/invalid-id
```

will not identify the intended document.

---

## 20. Development Recommendations

During development:

- Keep MongoDB credentials in `.env`.
- Do not commit `.env`.
- Use MongoDB Compass to inspect documents.
- Use Postman to test API endpoints independently of the frontend.
- Check browser developer tools when frontend requests fail.
- Check the Express terminal for backend errors.
- Keep frontend and backend running in separate terminals.

---

## 21. Development Workflow

A typical development workflow is:

```text
Clone repository
      |
      v
Install backend dependencies
      |
      v
Configure MongoDB
      |
      v
Start Express API
      |
      v
Install frontend dependencies
      |
      v
Start React application
      |
      v
Open browser
      |
      v
Test product CRUD
```

---

## 22. Production Considerations

Before deploying the application to production, the following should be addressed:

- Use production MongoDB credentials.
- Store secrets securely.
- Configure CORS for approved origins.
- Use HTTPS.
- Add authentication and authorization.
- Add input validation and stronger error handling.
- Add database backups.
- Add logging and monitoring.
- Avoid exposing unrestricted CRUD operations publicly.

The current implementation should therefore be treated primarily as a lightweight inventory application rather than a production-ready multi-user inventory platform.
