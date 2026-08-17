# Stray Shield — API Reference

> REST API reference for authentication, report management, user profiles, health checks, and machine-learning-assisted classification.

## 1. Overview

The Stray Shield backend exposes a REST API used by the Next.js frontend and other authorized clients.

The API is implemented using **Node.js and Express.js** and runs on port `3001` during local development.

```text id="7v8j2x"
Base URL
http://localhost:3001
```

Unless otherwise specified, endpoints use:

```text
Content-Type: application/json
```

---

# 2. Authentication

Stray Shield uses **JSON Web Tokens (JWT)** to authenticate protected API requests.

After successful signup or login, the API returns a JWT token.

Protected requests must include the token in the `Authorization` header:

```http
Authorization: Bearer <JWT>
```

### Authentication Flow

```text
Client
  │
  │ POST /api/auth/login
  ▼
Express API
  │
  │ JWT
  ▼
Client
  │
  │ Authorization: Bearer <JWT>
  ▼
Protected Endpoint
```

Requests to protected endpoints without a valid token return:

```http
401 Unauthorized
```

---

# 3. Endpoint Summary

| Method   | Endpoint              | Authentication | Description                   |
| -------- | --------------------- | -------------- | ----------------------------- |
| `POST`   | `/api/auth/signup`    | No             | Register a new user           |
| `POST`   | `/api/auth/login`     | No             | Authenticate a user           |
| `POST`   | `/api/reports/create` | Yes            | Create a report               |
| `GET`    | `/api/reports`        | Yes            | Retrieve reports              |
| `GET`    | `/api/reports/:id`    | Yes            | Retrieve a specific report    |
| `PUT`    | `/api/reports/:id`    | Yes            | Update a report               |
| `DELETE` | `/api/reports/:id`    | Yes            | Delete a report               |
| `GET`    | `/api/users/profile`  | Yes            | Retrieve current user profile |
| `PUT`    | `/api/users/profile`  | Yes            | Update current user profile   |
| `GET`    | `/api/health`         | No             | Check API availability        |
| `POST`   | `/api/classify`       | Yes            | Image-classification endpoint |

---

# 4. Authentication Endpoints

## POST `/api/auth/signup`

Registers a new Stray Shield user.

### Authentication

**Not required.**

### Request

```http
POST /api/auth/signup
Content-Type: application/json
```

### Request Body

| Field                | Type   | Required | Description               |
| -------------------- | ------ | -------- | ------------------------- |
| `email`              | string | Yes      | User email address        |
| `password`           | string | Yes      | User password             |
| `name`               | string | No       | User's display name       |
| `phone`              | string | No       | Contact number            |
| `userType`           | string | Yes      | `citizen` or `ngo`        |
| `organizationName`   | string | No       | NGO organization name     |
| `registrationNumber` | string | No       | NGO registration number   |
| `address`            | string | No       | User/organization address |

### Example Request

```json
{
  "email": "ngo@example.com",
  "password": "securepass",
  "name": "NGO Example",
  "phone": "9876543210",
  "userType": "ngo",
  "organizationName": "RescueOrg",
  "registrationNumber": "REG123",
  "address": "123 Main St"
}
```

### Successful Response

**HTTP 200 OK** or **HTTP 201 Created**

```json
{
  "token": "<jwt-token>",
  "userId": 123,
  "userType": "ngo",
  "message": "Signup successful"
}
```

### Error Responses

| Status | Description                                      |
| ------ | ------------------------------------------------ |
| `400`  | User already exists or invalid registration data |
| `500`  | Signup failed due to a server error              |

### cURL

```bash
curl -X POST http://localhost:3001/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "password": "test123",
    "name": "Test",
    "phone": "1234567890",
    "userType": "citizen"
  }'
```

---

## POST `/api/auth/login`

Authenticates an existing user and returns a JWT.

### Authentication

**Not required.**

### Request

```http
POST /api/auth/login
Content-Type: application/json
```

### Request Body

| Field      | Type   | Required | Description              |
| ---------- | ------ | -------- | ------------------------ |
| `email`    | string | Yes      | Registered email address |
| `password` | string | Yes      | Account password         |
| `userType` | string | Yes      | `citizen` or `ngo`       |

### Example Request

```json
{
  "email": "test@example.com",
  "password": "test123",
  "userType": "citizen"
}
```

### Successful Response

**HTTP 200 OK**

```json
{
  "token": "<jwt-token>",
  "userId": 42,
  "userType": "citizen",
  "message": "Login successful"
}
```

### Error Responses

| Status | Description                        |
| ------ | ---------------------------------- |
| `401`  | Invalid credentials                |
| `500`  | Login failed due to a server error |

### cURL

```bash
curl -X POST http://localhost:3001/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "password": "test123",
    "userType": "citizen"
  }'
```

---

# 5. Report Endpoints

Reports represent stray-dog cases submitted by citizens and managed by NGOs.

## POST `/api/reports/create`

Creates a new stray-dog report.

### Authentication

**Required.**

```http
Authorization: Bearer <JWT>
```

### Request Body

| Field          | Type   | Required | Description                          |
| -------------- | ------ | -------- | ------------------------------------ |
| `location`     | string | Yes      | Report location                      |
| `latitude`     | number | No       | Geographic latitude                  |
| `longitude`    | number | No       | Geographic longitude                 |
| `description`  | string | No       | Description of the reported dog/case |
| `contactName`  | string | No       | Reporter's name                      |
| `contactPhone` | string | No       | Reporter's phone number              |
| `contactEmail` | string | No       | Reporter's email                     |
| `imageUrl`     | string | No       | URL/path to an uploaded image        |

> Multi-image selection and preview are handled on the frontend through the reporting interface. The API field documented here represents the image URL/path supplied to the backend.

### Example Request

```json
{
  "location": "Corner of 5th and Main",
  "latitude": 12.34,
  "longitude": 56.78,
  "description": "Small brown dog, appears injured.",
  "contactName": "Alice",
  "contactPhone": "555-1234",
  "contactEmail": "alice@example.com",
  "imageUrl": "http://localhost:3001/uploads/img123.jpg"
}
```

### Successful Response

**HTTP 200 OK**

```json
{
  "message": "Report created successfully",
  "report": {
    "id": 101,
    "user_id": 42,
    "location": "Corner of 5th and Main",
    "status": "pending",
    "created_at": "..."
  }
}
```

### Error Responses

| Status | Description                             |
| ------ | --------------------------------------- |
| `401`  | Missing or invalid authentication token |
| `500`  | Report creation failed                  |

### cURL

```bash
curl -X POST http://localhost:3001/api/reports/create \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "location": "Park",
    "description": "Injured dog",
    "contactName": "Bob"
  }'
```

---

## GET `/api/reports`

Retrieves reports available to the authenticated user.

### Authentication

**Required.**

### Access Behavior

The returned reports depend on the authenticated user's role:

* **Citizens** see their own reports.
* **NGOs** can see all reports.

### Query Parameters

| Parameter | Type   | Required | Description                                              |
| --------- | ------ | -------- | -------------------------------------------------------- |
| `status`  | string | No       | Filter by `pending`, `in_progress`, `resolved`, or `all` |

### Example Request

```http
GET /api/reports?status=pending
Authorization: Bearer <JWT>
```

### Successful Response

**HTTP 200 OK**

```json
{
  "reports": [
    {
      "id": 101,
      "status": "pending",
      "location": "..."
    }
  ]
}
```

### Error Responses

| Status | Description                              |
| ------ | ---------------------------------------- |
| `401`  | Authentication required or token invalid |
| `500`  | Failed to retrieve reports               |

### cURL

```bash
curl "http://localhost:3001/api/reports?status=pending" \
  -H "Authorization: Bearer <token>"
```

---

## GET `/api/reports/:id`

Retrieves details for a specific report.

### Authentication

**Required.**

### Path Parameters

| Parameter | Type    | Required | Description |
| --------- | ------- | -------- | ----------- |
| `id`      | integer | Yes      | Report ID   |

### Example Request

```http
GET /api/reports/101
Authorization: Bearer <JWT>
```

### Successful Response

**HTTP 200 OK**

```json
{
  "report": {
    "id": 101,
    "user_id": 42,
    "location": "...",
    "status": "pending"
  }
}
```

### Error Responses

| Status | Description                                       |
| ------ | ------------------------------------------------- |
| `401`  | Missing or invalid token                          |
| `403`  | Citizen attempted to access another user's report |
| `404`  | Report not found                                  |
| `500`  | Failed to retrieve report                         |

### cURL

```bash
curl http://localhost:3001/api/reports/101 \
  -H "Authorization: Bearer <token>"
```

---

## PUT `/api/reports/:id`

Updates an existing report.

This endpoint is primarily used by NGOs to update report status.

### Authentication

**Required.**

### Authorization

Only users with:

```text
userType = ngo
```

can update reports.

### Path Parameters

| Parameter | Type    | Required | Description |
| --------- | ------- | -------- | ----------- |
| `id`      | integer | Yes      | Report ID   |

### Request Body

| Field    | Type   | Required | Description       |
| -------- | ------ | -------- | ----------------- |
| `status` | string | Yes      | New report status |

Supported statuses include:

```text
pending
in_progress
resolved
```

### Example Request

```json
{
  "status": "in_progress"
}
```

### Successful Response

**HTTP 200 OK**

```json
{
  "message": "Report updated successfully",
  "report": {
    "id": 101,
    "status": "in_progress"
  }
}
```

### Error Responses

| Status | Description                              |
| ------ | ---------------------------------------- |
| `403`  | User is not authorized to update reports |
| `404`  | Report not found                         |
| `500`  | Failed to update report                  |

### cURL

```bash
curl -X PUT http://localhost:3001/api/reports/101 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{"status":"in_progress"}'
```

---

## DELETE `/api/reports/:id`

Deletes a report.

### Authentication

**Required.**

### Authorization

Citizens can delete their own reports. Additional NGO/admin permissions may be introduced in future versions.

### Path Parameters

| Parameter | Type    | Required | Description |
| --------- | ------- | -------- | ----------- |
| `id`      | integer | Yes      | Report ID   |

### Successful Response

**HTTP 200 OK**

```json
{
  "message": "Report deleted successfully"
}
```

### Error Responses

| Status | Description                                 |
| ------ | ------------------------------------------- |
| `403`  | User is not authorized to delete the report |
| `404`  | Report not found                            |
| `500`  | Failed to delete report                     |

### cURL

```bash
curl -X DELETE http://localhost:3001/api/reports/101 \
  -H "Authorization: Bearer <token>"
```

---

# 6. User Endpoints

## GET `/api/users/profile`

Retrieves the profile of the currently authenticated user.

### Authentication

**Required.**

### Successful Response

**HTTP 200 OK**

```json
{
  "user": {
    "id": 42,
    "email": "test@example.com",
    "name": "Test",
    "user_type": "citizen",
    "created_at": "..."
  }
}
```

### Error Responses

| Status | Description             |
| ------ | ----------------------- |
| `401`  | Authentication required |
| `404`  | User not found          |

### cURL

```bash
curl http://localhost:3001/api/users/profile \
  -H "Authorization: Bearer <token>"
```

---

## PUT `/api/users/profile`

Updates the profile of the currently authenticated user.

### Authentication

**Required.**

### Request

Provide the fields that need to be updated as JSON.

Example:

```json
{
  "name": "Updated Name",
  "phone": "9876543210",
  "address": "Updated Address"
}
```

### Response

The endpoint returns the updated user profile according to the fields supported by the current implementation.

> The exact set of accepted profile fields should be verified against the current backend implementation before integrating external clients.

---

# 7. Health Endpoint

## GET `/api/health`

Checks whether the Stray Shield API is running.

### Authentication

**Not required.**

This endpoint is useful for:

* Local development
* Deployment verification
* Basic service monitoring
* Health checks

### Successful Response

**HTTP 200 OK**

```json
{
  "status": "OK",
  "message": "Stray Shield API is running"
}
```

### cURL

```bash
curl http://localhost:3001/api/health
```

---

# 8. AI Classification

## POST `/api/classify`

Provides an endpoint for image classification.

### Current Implementation Status

> ⚠️ **Implementation status: Placeholder / integration point**

The current endpoint requires authentication and returns a mocked classification response. The backend is prepared for integration with the TensorFlow.js classification pipeline, but the endpoint does not yet represent the complete production ML workflow.

The ML implementation is located in:

```text
ml/predict.js
```

and uses MobileNet inference with a KNN classifier.

### Authentication

**Required.**

```http
Authorization: Bearer <JWT>
```

### Current Request

The current server implementation expects image data through an `imageBuffer`-style input.

```json
{
  "imageBuffer": "<base64-or-image-data>"
}
```

The exact final request contract should be established when the ML pipeline is integrated directly into the API.

### Current Response

```json
{
  "label": "Dog"
}
```

### Planned Classification Response

Once the ML pipeline is integrated, the endpoint can return health-related classifications such as:

```json
{
  "label": "injured"
}
```

or:

```json
{
  "label": "healthy"
}
```

or:

```json
{
  "label": "sick"
}
```

### Implementation Notes

The existing ML pipeline contains the components required for:

1. Loading the MobileNet model.
2. Extracting image embeddings.
3. Running KNN classification.
4. Producing a classification result.

The API can eventually integrate this workflow either:

* **Synchronously** during an API request, or
* **Asynchronously** through a background processing workflow.

For production use, asynchronous processing may be preferable for larger images or computationally expensive workloads.

---

# 9. Authorization Matrix

The API uses the authenticated user's role to determine access to certain operations.

| Operation                      |    Citizen    |            NGO            |
| ------------------------------ | :-----------: | :-----------------------: |
| Register                       |       ✅       |             ✅             |
| Login                          |       ✅       |             ✅             |
| Create report                  |       ✅       |             ✅             |
| View own reports               |       ✅       |             ✅             |
| View all reports               |       ❌       |             ✅             |
| View accessible report details |       ✅       |             ✅             |
| Update report status           |       ❌       |             ✅             |
| Delete own report              |       ✅       | Depends on implementation |
| View own profile               |       ✅       |             ✅             |
| Update own profile             |       ✅       |             ✅             |
| Use classification endpoint    | Authenticated |       Authenticated       |

> Authorization behavior should always be considered implementation-specific. This table reflects the documented application behavior rather than defining permissions that are not currently implemented.

---

# 10. Report Status Values

Reports use the following status values:

| Status        | Meaning                                           |
| ------------- | ------------------------------------------------- |
| `pending`     | Report has been submitted and is awaiting action. |
| `in_progress` | The case is currently being reviewed or handled.  |
| `resolved`    | The case has been resolved.                       |

The expected lifecycle is:

```text
pending
   │
   ▼
in_progress
   │
   ▼
resolved
```

---

# 11. Common HTTP Status Codes

| Status | Meaning               | Typical Usage                       |
| -----: | --------------------- | ----------------------------------- |
|  `200` | OK                    | Successful request                  |
|  `201` | Created               | Resource successfully created       |
|  `400` | Bad Request           | Invalid or incomplete request       |
|  `401` | Unauthorized          | Missing or invalid authentication   |
|  `403` | Forbidden             | Authenticated user lacks permission |
|  `404` | Not Found             | Requested resource does not exist   |
|  `500` | Internal Server Error | Unexpected server-side failure      |

---

# 12. Example Authenticated Request

Once a client has received a JWT from the login endpoint, it can use the token when calling protected resources.

```bash
curl http://localhost:3001/api/reports \
  -H "Authorization: Bearer <jwt-token>"
```

The server validates the token before processing the request.

---

# 13. API Usage Flow

A typical citizen interaction with the API looks like:

```text
POST /api/auth/signup
        │
        ▼
Receive JWT
        │
        ▼
POST /api/reports/create
        │
        ▼
GET /api/reports
        │
        ▼
GET /api/reports/:id
        │
        ▼
Track report status
```

An NGO workflow can additionally include:

```text
POST /api/auth/login
        │
        ▼
Receive JWT
        │
        ▼
GET /api/reports
        │
        ▼
Filter reports
        │
        ▼
PUT /api/reports/:id
        │
        ▼
Update status
```

---

# 14. Security Considerations

Clients integrating with the Stray Shield API should follow these practices:

* Always use HTTPS in production.
* Never expose the JWT secret to clients.
* Store authentication tokens securely.
* Do not commit `.env` files or credentials.
* Validate request data before sending it to the API.
* Restrict access to protected endpoints using JWT authentication.
* Use appropriate CORS configuration in production.
* Avoid logging authentication tokens or sensitive user information.

---

# 15. Local API Reference

For local development, the API base URL is:

```text
http://localhost:3001
```

### Authentication

```text
POST /api/auth/signup
POST /api/auth/login
```

### Reports

```text
POST   /api/reports/create
GET    /api/reports
GET    /api/reports/:id
PUT    /api/reports/:id
DELETE /api/reports/:id
```

### Users

```text
GET /api/users/profile
PUT /api/users/profile
```

### Health

```text
GET /api/health
```

### Machine Learning

```text
POST /api/classify
```

---

# 16. Implementation Reference

The API is implemented in the Express.js backend located under:

```text
server/
```

Relevant areas include:

```text
server/
├── index.js
├── config/
│   └── database.js
├── migration/
└── seed/
```

The machine-learning implementation is located under:

```text
ml/
├── predict.js
├── train.js
└── health_model/
```

For information about installing and running the API locally, see the [Developer Setup Guide](developer-setup-guide.md).

For a high-level explanation of the architecture and request flow, see the [System Overview](system-overview.md).

---

## 17. API Status

| Area                   | Status                     |
| ---------------------- | -------------------------- |
| Authentication         | ✅ Implemented              |
| Report creation        | ✅ Implemented              |
| Report retrieval       | ✅ Implemented              |
| Report updates         | ✅ Implemented              |
| Report deletion        | ✅ Implemented              |
| User profile           | ✅ Implemented              |
| Health check           | ✅ Implemented              |
| JWT authentication     | ✅ Implemented              |
| ML classification API  | ⚠️ Integration/placeholder |
| Production ML workflow | 🔄 Future improvement      |

> **Documentation principle:** This reference distinguishes implemented API behavior from planned functionality so that developers can rely on it when integrating with the current application.
