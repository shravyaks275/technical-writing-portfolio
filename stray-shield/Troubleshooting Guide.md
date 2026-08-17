````markdown
# Stray Shield — Troubleshooting Guide

This guide covers common issues encountered when setting up, developing, testing, and deploying Stray Shield.

---

## 1. Frontend Cannot Connect to Backend

### Symptoms

- API requests fail from the frontend.
- Login or signup does not work.
- Reports cannot be loaded or created.
- The browser displays a network error.

### Possible Causes

- `NEXT_PUBLIC_API_URL` is incorrect.
- The backend is not running.
- The backend is running on a different port.
- CORS configuration is preventing the request.

### How to Diagnose

Check the frontend environment configuration:

```env
NEXT_PUBLIC_API_URL=http://localhost:3001
````

Test the backend health endpoint:

```bash
curl http://localhost:3001/api/health
```

Expected response:

```json
{
  "status": "OK",
  "message": "Stray Shield API is running"
}
```

Also inspect the browser's **Network** tab to verify the API request URL and response status.

### Solution

Start the backend:

```bash
cd server
npm run dev
```

Ensure the frontend uses the correct backend URL:

```env
NEXT_PUBLIC_API_URL=http://localhost:3001
```

Restart the frontend after changing environment variables:

```bash
npm run dev
```

---

## 2. JWT Authentication Fails

### Symptoms

The API returns:

```text
401 Unauthorized
```

or messages such as:

```text
Invalid token
No token provided
```

### Possible Causes

* Authorization header is missing.
* JWT is expired.
* JWT is malformed.
* Backend JWT configuration has changed.
* The user session is no longer valid.

### How to Diagnose

Protected requests should contain:

```http
Authorization: Bearer <token>
```

Check the request headers in the browser's **Network** tab.

Verify the backend environment configuration:

```env
JWT_SECRET=your-secret-key
```

### Solution

Ensure the frontend sends the token using the correct format:

```http
Authorization: Bearer <token>
```

If the token has expired or is no longer valid, log in again to generate a new token.

If the backend JWT secret was changed, previously issued tokens may no longer be valid.

> The JWT secret should remain on the backend and must not be exposed to the frontend.

---

## 3. API Returns 403 Forbidden

### Symptoms

An authenticated request returns:

```text
403 Forbidden
```

### Possible Causes

The user is authenticated but does not have permission to perform the requested operation.

For example, report updates are restricted to NGO users.

### How to Diagnose

Check the authenticated user's `userType`.

Stray Shield uses roles such as:

```text
citizen
ngo
```

Review the endpoint's authorization requirements.

### Solution

Use an account with the required role.

For example:

* Citizens can create and manage their own reports.
* NGOs can access and update reports according to the application's role-based permissions.

---

## 4. API Returns 404 Not Found

### Symptoms

A frontend API request returns:

```text
404 Not Found
```

### Possible Causes

* Incorrect API URL.
* Incorrect endpoint path.
* Backend route is not registered.
* Frontend and backend are using different API versions or routes.

### How to Diagnose

First verify that the backend is running:

```bash
curl http://localhost:3001/api/health
```

Then verify the requested endpoint against the API Reference.

Check the backend route definitions in:

```text
server/index.js
```

### Solution

Verify the complete API URL.

For example:

```text
http://localhost:3001/api/reports
```

Ensure the frontend is not accidentally calling an incorrect or outdated route.

---

## 5. API Returns 500 Internal Server Error

### Symptoms

The API returns:

```text
500 Internal Server Error
```

### Possible Causes

* Backend exception.
* Database failure.
* Invalid request data.
* File or image-processing error.
* ML model or classifier error.

### How to Diagnose

Check the backend terminal or deployment logs.

Also inspect the failed request in the browser's **Network** tab.

Identify:

* Endpoint
* HTTP method
* Request payload
* Response
* Backend error message

### Solution

Resolve the underlying backend error before retrying the request.

If the error involves a database operation, check database connectivity.

If it involves image processing or ML classification, check the relevant model and image-processing files.

---

## 6. Database Connection Fails

### Symptoms

The backend reports errors such as:

```text
ECONNREFUSED
```

or database-dependent API operations fail.

### Possible Causes

* PostgreSQL is not running.
* Database does not exist.
* Incorrect database host or port.
* Incorrect username or password.
* Incorrect database name.
* Incorrect SSL configuration.

### How to Diagnose

Check the backend environment variables:

```env
DB_HOST=localhost
DB_PORT=5432
DB_NAME=stray_shield
DB_USER=postgres
DB_PASSWORD=yourpassword
DB_SSL=false
```

Test the PostgreSQL connection:

```bash
psql -U postgres -h localhost -p 5432 -l
```

Check the database configuration in:

```text
server/config/database.js
```

### Solution

Ensure PostgreSQL is running and verify the database credentials.

Create the database if required:

```sql
CREATE DATABASE stray_shield;
```

For local development, verify that:

```env
DB_SSL=false
```

is appropriate for the local PostgreSQL configuration.

Stray Shield also supports a file-based storage fallback for lightweight local development where applicable.

---

## 7. Image Upload Fails

### Symptoms

* Report creation fails after selecting an image.
* Large image uploads fail.
* Image previews do not appear.
* The backend rejects the request.

### Possible Causes

* Request body exceeds the configured limit.
* Incorrect request format.
* Incorrect image data.
* Image-processing failure.
* Frontend and backend expect different image formats.

### How to Diagnose

Inspect the failed request in:

```text
Browser DevTools → Network
```

Check:

* Request payload
* Content-Type
* Request size
* Response status

Also check backend logs for image or payload parsing errors.

### Solution

The server currently uses a request body limit of approximately 50 MB.

If necessary, review the request-body configuration in:

```text
server/index.js
```

For production use, large images should preferably be stored using dedicated object or image storage, with the backend storing the resulting image URL.

---

## 8. CORS Error

### Symptoms

The browser reports:

```text
blocked by CORS policy
```

or another cross-origin request error.

### Possible Causes

* Backend CORS configuration does not allow the frontend origin.
* Frontend and backend are running on different origins.
* Production frontend origin has not been configured.

### How to Diagnose

Inspect the failed request in the browser's **Network** tab.

Check the response headers for:

```http
Access-Control-Allow-Origin
```

### Solution

For local development, the backend should allow requests from the frontend.

A restricted configuration can be used when required:

```javascript
app.use(
  cors({
    origin: "http://localhost:3000"
  })
);
```

For production, configure CORS to allow the deployed frontend origin.

---

## 9. Report Creation Fails

### Symptoms

A citizen submits a report, but the report is not created.

### Possible Causes

* User is not authenticated.
* JWT is missing or invalid.
* Required report information is missing.
* Image payload is too large.
* Database connection has failed.
* Backend validation or processing fails.

### How to Diagnose

Inspect:

```text
POST /api/reports/create
```

in the browser's **Network** tab.

Verify:

* Authorization header
* Request payload
* Response status
* Backend logs

A basic request can be tested with:

```bash
curl -X POST http://localhost:3001/api/reports/create \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{"location":"Park","description":"Injured dog","contactName":"Test User"}'
```

### Solution

Verify authentication, required fields, database availability, and image handling.

---

## 10. Reports Are Not Loading

### Symptoms

* Dashboard displays no reports.
* Reports remain empty.
* Report filtering does not return expected results.

### Possible Causes

* Backend API is unavailable.
* JWT is invalid.
* Database contains no reports.
* Incorrect status filter is being used.
* User does not have permission to access the requested reports.

### How to Diagnose

Inspect:

```text
GET /api/reports
```

in the browser's **Network** tab.

If a status filter is being used, verify that it is one of:

```text
pending
in_progress
resolved
all
```

### Solution

Verify:

1. Backend is running.
2. User is authenticated.
3. API returns a successful response.
4. Correct status filter is being used.
5. User has permission to access the requested reports.

---

## 11. ML Classification Fails

### Symptoms

The classification endpoint:

```text
POST /api/classify
```

may:

* Return an unexpected response.
* Fail to load the classifier.
* Produce low-confidence predictions.
* Throw an image-processing or model-loading error.

### Possible Causes

* `classifier.json` is missing.
* Classifier data is malformed.
* Model path is incorrect.
* MobileNet cannot be loaded.
* Input image cannot be processed.
* Model initialization has not completed.
* Training data is insufficient.

### How to Diagnose

Check:

```text
ml/predict.js
ml/train.js
ml/health_model/classifier.json
```

Verify that:

```text
ml/health_model/classifier.json
```

exists.

Run the prediction script locally if supported:

```bash
node ml/predict.js
```

Review the output for model-loading, file, tensor, or classification errors.

### Solution

Ensure the classifier dataset and required model dependencies are available.

For backend integration, load the MobileNet model and classifier once during server startup and reuse them for prediction requests where appropriate.

---

## 12. ML Returns Unexpected Health Labels

### Symptoms

The classifier returns a health category that does not match the expected condition.

For example:

```text
Expected: injured
Received: healthy
```

### Possible Causes

* Training dataset is too small.
* Images are incorrectly labeled.
* Training images are not representative of real-world images.
* Poor lighting or image quality.
* Occlusion.
* Different camera angles.
* Background differences.
* Similar visual characteristics between classes.

### How to Diagnose

Inspect the dataset structure:

```text
dataset/
├── healthy/
├── sick/
└── injured/
```

Verify that the images are correctly labeled and that each category contains representative examples.

### Solution

Improve the classifier dataset by:

* Adding more representative images.
* Correcting incorrectly labeled images.
* Increasing variation between training examples.
* Rebuilding the classifier dataset.

The ML classifier should be treated as a **triage aid**, not as a veterinary diagnosis.

---

## 13. ML Classification Returns the Placeholder Label

### Symptoms

The endpoint:

```text
POST /api/classify
```

returns:

```json
{
  "label": "Dog"
}
```

instead of a health category such as:

```text
healthy
sick
injured
```

### Cause

The current backend classification endpoint may still contain placeholder behavior rather than the complete MobileNet + KNN prediction pipeline.

### How to Diagnose

Inspect:

```text
server/index.js
ml/predict.js
ml/train.js
ml/health_model/classifier.json
```

Determine whether the backend endpoint is connected to the trained classifier.

### Solution

Integrate the ML prediction flow into the backend endpoint.

The intended processing flow is:

```text
Image
  ↓
Image preprocessing
  ↓
MobileNet feature extraction
  ↓
KNN classification
  ↓
Health label
```

Expected classification categories are:

```text
healthy
sick
injured
```

---

## 14. Deployment Problems

### Symptoms

The application works locally but fails after deployment.

Examples include:

* Frontend build failures.
* API requests failing in production.
* Database connection errors.
* Missing environment variables.
* CORS errors.
* TensorFlow.js errors.

### Possible Causes

* Production environment variables are missing.
* Frontend is using the local backend URL.
* Database credentials are incorrect.
* Production frontend origin is not allowed by CORS.
* Server-side ML dependencies are incompatible with the hosting environment.

### How to Diagnose

Check the deployment logs provided by the hosting platform.

Verify:

```text
NEXT_PUBLIC_API_URL
JWT_SECRET
NODE_ENV
DB_HOST
DB_PORT
DB_NAME
DB_USER
DB_PASSWORD
DB_SSL
```

Also verify that the frontend is not configured to use:

```text
http://localhost:3001
```

in production.

### Solution

Configure the required environment variables in the deployment platform.

For the frontend:

```text
NEXT_PUBLIC_API_URL=<production-backend-url>
```

For the backend, configure the required authentication and database variables.

For server-side ML, verify that the deployment environment supports the TensorFlow.js dependencies required by the application.

---

## 15. TensorFlow.js Deployment Issues

### Symptoms

The ML pipeline works locally but fails in the deployed Node.js environment.

### Possible Causes

* TensorFlow.js server-side dependencies are incompatible with the deployment environment.
* Required native dependencies are unavailable.
* Model loading fails during deployment.

### How to Diagnose

Inspect deployment logs for errors related to:

```text
TensorFlow
tfjs
native bindings
model loading
```

### Solution

For server-side Node.js inference, evaluate the use of:

```text
@tensorflow/tfjs-node
```

where appropriate for the implementation.

The selected hosting environment must support the required dependencies.

---

## 16. Diagnostic Commands

### Test Backend Health

```bash
curl http://localhost:3001/api/health
```

### Start Frontend

```bash
npm run dev
```

### Start Backend

```bash
cd server
npm run dev
```

### Build Frontend

```bash
npm run build
```

### Start Production Frontend

```bash
npm start
```

### Test PostgreSQL

```bash
psql -U postgres -h localhost -p 5432 -l
```

### Run ML Prediction

```bash
node ml/predict.js
```

---

## 17. Troubleshooting Checklist

### Frontend

* [ ] Next.js development server is running.
* [ ] Frontend is available on port `3000`.
* [ ] `NEXT_PUBLIC_API_URL` is correct.
* [ ] Frontend was restarted after environment changes.
* [ ] Browser Network tab shows the expected backend URL.

### Backend

* [ ] Express server is running.
* [ ] Backend is available on port `3001`.
* [ ] `/api/health` returns successfully.
* [ ] Required environment variables are configured.
* [ ] Backend logs do not show unhandled errors.

### Authentication

* [ ] User is logged in.
* [ ] JWT is present.
* [ ] Authorization header uses `Bearer <token>`.
* [ ] Token has not expired.
* [ ] User has the required role.

### Database

* [ ] PostgreSQL is running.
* [ ] Database exists.
* [ ] Database credentials are correct.
* [ ] Database host and port are correct.
* [ ] SSL configuration is appropriate.

### Images

* [ ] Image format is supported.
* [ ] Request size is within the configured limit.
* [ ] Image data is sent correctly.
* [ ] Backend can process the image.

### ML

* [ ] `classifier.json` exists.
* [ ] ML dependencies are installed.
* [ ] Dataset categories are correctly structured.
* [ ] `ml/predict.js` can run successfully.
* [ ] Backend ML integration is configured correctly.

### Deployment

* [ ] Production environment variables are configured.
* [ ] Frontend uses the production API URL.
* [ ] Backend can access the production database.
* [ ] CORS allows the production frontend.
* [ ] Hosting environment supports the required ML dependencies.

---

## 18. Security Considerations

Do not commit sensitive configuration to the repository.

The following should remain outside source control:

```text
.env
.env.local
JWT_SECRET
Database passwords
API keys
Access tokens
Production credentials
```

Use environment variables for local development and the hosting platform's secret-management facilities for production.

When sharing logs or screenshots while troubleshooting, remove authentication tokens, passwords, API keys, and other sensitive information.

---

## 19. Troubleshooting Workflow

When investigating an issue, follow the application flow:

```text
Frontend
   ↓
API Request
   ↓
Express Backend
   ↓
Authentication / Authorization
   ↓
Database / Image Processing / ML
   ↓
API Response
   ↓
Frontend
```

Start with the simplest checks:

1. Confirm the affected service is running.
2. Test the backend health endpoint.
3. Inspect the browser Network tab.
4. Check the backend logs.
5. Verify environment variables.
6. Check authentication and authorization.
7. Check database connectivity when applicable.
8. Check image-processing or ML files when applicable.
9. Review deployment logs if the problem occurs only in production.

This approach helps isolate whether the problem originates in the frontend, API, authentication layer, database, ML pipeline, or deployment environment.

```
```
