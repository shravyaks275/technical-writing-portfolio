# Scrape Wise — API Reference

## 1. Overview

The Scrape Wise backend exposes HTTP endpoints through the Express server defined in `server.js`.

The API is responsible for:

- Product scraping
- Product analysis
- Sentiment processing
- Data retrieval
- User functionality
- Administrative functionality
- Server health monitoring

The API is primarily consumed by the frontend JavaScript application.

---

## 2. Base URL

When running locally, the API is served by the Express application.

```text
http://localhost:3000
````

The exact port depends on the server configuration.

---

## 3. Request Format

Most API requests use JSON request bodies.

For JSON requests, use:

```http
Content-Type: application/json
```

Example:

```http
POST /api/scrape
Content-Type: application/json
```

```json
{
  "url": "https://example.com/product"
}
```

---

# 4. Scraping API

## POST `/api/scrape`

Starts a scraping operation for a supplied product URL.

### Description

The endpoint accepts a product URL and uses the server-side Puppeteer scraping pipeline to retrieve product information.

The scraping process may include:

* Product title
* Price
* Rating
* Review information
* Review count
* Country/origin information
* Sentiment analysis
* Estimated sales

### Authentication

The current scraping endpoint does not implement a production-grade authentication requirement.

### Request Body

The request contains the product URL.

```json
{
  "url": "https://example.com/product"
}
```

### Example Request

```bash
curl -X POST http://localhost:3000/api/scrape \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com/product"}'
```

### Response

A successful response contains the scraped product information and processed analysis.

The exact fields depend on the information successfully extracted from the target page.

Conceptually:

```json
{
  "title": "Example Product",
  "price": "₹999",
  "rating": 4.5,
  "reviews": [],
  "reviewCount": 120,
  "sentiment": {
    "positive": 80,
    "neutral": 25,
    "negative": 15
  },
  "estimatedSales": 1200
}
```

The actual response structure should be treated according to the implementation in `server.js`.

### Possible Failures

The scraping request can fail because of:

* Invalid URL
* Target website unavailable
* Navigation failure
* Page timeout
* Browser failure
* Target website blocking automation
* Changed page structure
* Missing selectors
* Unexpected page content

---

# 5. Product Data

The scraper extracts product information from the target page using selectors implemented in `server.js`.

The available fields depend on the target page.

## Product Title

Represents the product name extracted from the page.

```json
{
  "title": "Example Product"
}
```

## Price

Represents the price extracted from the target page.

```json
{
  "price": "₹999"
}
```

The value reflects the page content and is not independently verified.

## Rating

Represents the product rating extracted from the target page.

```json
{
  "rating": 4.5
}
```

## Review Count

Represents the number of reviews identified by the scraper.

```json
{
  "reviewCount": 120
}
```

## Country / Origin

Where available, the scraper can extract country or origin-related product information.

```json
{
  "country": "India"
}
```

The availability of this field depends on the target page.

---

# 6. Review Analysis

Reviews extracted during scraping are processed through the application's sentiment-analysis functionality.

The project uses the `sentiment` npm package.

The processing flow is:

```text
Review
   |
   v
Sentiment.js
   |
   v
Sentiment Score
   |
   v
Positive / Neutral / Negative
```

---

# 7. Sentiment Classification

The sentiment result is based on the score produced by Sentiment.js.

The classification follows the application's implemented logic.

Conceptually:

```text
Score > 0  → Positive
Score = 0  → Neutral
Score < 0  → Negative
```

A sentiment summary can contain counts such as:

```json
{
  "positive": 80,
  "neutral": 25,
  "negative": 15
}
```

These values represent classified reviews rather than an independently verified opinion metric.

---

# 8. Sales Estimation

Scrape Wise estimates product sales using review information.

The value is calculated using the application's review-to-sales assumption.

Example:

```json
{
  "estimatedSales": 1200
}
```

### Important

`estimatedSales` is a calculated estimate.

It does **not** represent verified marketplace sales data.

The target website does not necessarily provide actual sales figures to the application.

---

# 9. Analysis API

The application contains functionality for processing and displaying product analysis.

Analysis is primarily consumed by the frontend after scraping.

The analysis can include:

* Sentiment distribution
* Product metrics
* Estimated sales
* Product comparison data

The frontend uses the returned product data to generate visualizations.

---

# 10. Product Comparison

Scraped product results can be compared in the frontend.

Comparison can use fields such as:

* Product name
* Price
* Rating
* Review count
* Sentiment
* Estimated sales

The comparison functionality is primarily client-side and operates on scraped results.

It does not represent a separate external comparison service.

---

# 11. Authentication Endpoints

The project contains login functionality and user data stored locally.

The authentication implementation is prototype-level.

## POST `/api/login`

The login functionality is associated with the application's login flow.

The request contains user credentials.

Example:

```json
{
  "username": "user@example.com",
  "password": "password"
}
```

The exact request and response fields must follow the implementation in `server.js` and the corresponding frontend login logic.

### Security Note

The current implementation should not be considered a production-grade authentication system.

Before production deployment, authentication should use:

* Secure password hashing
* Secure sessions or signed tokens
* Authentication middleware
* Role-based authorization
* Secure credential storage

---

# 12. User Data

The application uses local JSON-based storage for user information.

The repository contains:

```text
user.json
```

This allows the application to operate without requiring an external user database.

However, JSON storage is not appropriate for large-scale concurrent production usage.

---

# 13. Administrative API

The application provides administrative functionality through routes defined in `server.js`.

These routes are used by the administrative interface.

Administrative functionality includes areas such as:

* Server health
* User information
* Scraping information
* Administrative monitoring

---

# 14. GET `/admin/server-health`

Returns information about the running Node.js server.

### Authentication

The current implementation does not provide complete production-grade authorization for administrative functionality.

### Example Request

```bash
curl http://localhost:3000/admin/server-health
```

### Response

The response provides server runtime information such as:

```json
{
  "status": "OK",
  "uptime": 1234,
  "memory": {}
}
```

The exact response fields depend on the current implementation.

### Information Provided

The endpoint can expose information related to:

* Server status
* Process uptime
* Memory usage

---

# 15. Administrative User Endpoints

The project contains administrative functionality for working with user information.

The implementation uses local JSON-based user data.

Administrative user operations should therefore be understood as application-level prototype functionality rather than a complete identity-management system.

Before production deployment, administrative user operations should be protected using proper authentication and authorization.

---

# 16. Scraping Logs

The backend contains scraping-log functionality.

Scraping logs can be used to record information associated with scraping operations.

Potential information includes:

* Target URL
* Scraping status
* Processing information
* Errors
* Timing information

The current implementation contains overlapping logging-related functionality, so the logging architecture should be consolidated before production deployment.

---

# 17. Scheduled Scraping

The project contains `node-cron` and scheduled-processing-related functionality.

Scheduled scraping is intended to allow scraping tasks to be executed automatically.

Conceptually:

```text
Cron Schedule
     |
     v
Scheduled Job
     |
     v
Scraping Function
     |
     v
Product Data
```

However, the current implementation does not provide a complete production-ready scheduled job architecture.

The feature should therefore be considered partially implemented.

---

# 18. Error Responses

API errors may originate from several layers:

```text
Client Request
      |
      v
Express
      |
      v
Scraping Logic
      |
      v
Puppeteer
      |
      v
Target Website
```

Possible error categories include:

| Error                | Possible Cause                             |
| -------------------- | ------------------------------------------ |
| Invalid URL          | Supplied URL is malformed                  |
| Navigation failure   | Target page cannot be loaded               |
| Timeout              | Target page takes too long to load         |
| Extraction failure   | Expected selector is missing               |
| Browser error        | Puppeteer/browser failure                  |
| Network error        | Connectivity problem                       |
| Empty result         | Target page does not contain expected data |
| Authentication error | Invalid login information                  |
| Storage error        | JSON file cannot be read or written        |

---

# 19. HTTP Status Codes

The API uses HTTP status codes to indicate request results.

Common codes include:

| Status | Meaning                           |
| ------ | --------------------------------- |
| `200`  | Request completed successfully    |
| `400`  | Invalid request                   |
| `401`  | Authentication required or failed |
| `403`  | Request is not authorized         |
| `404`  | Resource or route not found       |
| `500`  | Internal server error             |

The exact status returned depends on the route and error condition implemented in `server.js`.

---

# 20. CORS

The Express backend uses CORS middleware.

This allows the frontend to communicate with the backend from a different origin when the appropriate CORS configuration is enabled.

For local development, the frontend and backend can be configured to communicate through the local server.

If the browser reports a CORS error, check:

1. Backend CORS configuration.
2. Frontend API URL.
3. Browser network request.
4. Response headers.
5. HTTP method and preflight request.

---

# 21. Request Flow

A normal scraping request follows this sequence:

```text
Browser
   |
   | POST /api/scrape
   v
Express Server
   |
   v
Validate Request
   |
   v
Launch Puppeteer
   |
   v
Navigate to Product URL
   |
   v
Extract Product Data
   |
   v
Extract Reviews
   |
   v
Run Sentiment Analysis
   |
   v
Estimate Sales
   |
   v
Persist Result
   |
   v
Return JSON Response
   |
   v
Browser
```

---

# 22. Frontend API Consumption

The frontend JavaScript communicates with the backend using browser HTTP requests.

The primary frontend files involved in the application flow include:

```text
script.js
analysis.js
admin.js
```

These files consume backend functionality and update the corresponding HTML interfaces.

---

# 23. API and Frontend Relationship

The application follows a simple client-server architecture:

```text
+-----------------------------+
|         Frontend            |
|                             |
| index.html                  |
| analysis.html               |
| admin.html                  |
| login.html                  |
| script.js                   |
| analysis.js                 |
| admin.js                    |
+-------------+---------------+
              |
              | HTTP
              v
+-----------------------------+
|       Express Backend       |
|                             |
| server.js                   |
+-------------+---------------+
              |
       +------+------+
       |             |
       v             v
   Puppeteer    JSON Storage
       |
       v
 Target Websites
```

---

# 24. Security Considerations

The current API should be considered prototype-level.

Before exposing the API publicly, the following should be implemented or strengthened:

* Authentication middleware.
* Role-based authorization.
* Password hashing.
* Session/token security.
* Request validation.
* URL validation.
* SSRF protection.
* Rate limiting.
* Request-size limits.
* Administrative route protection.
* Secure environment variables.
* Centralized error handling.

---

# 25. SSRF Considerations

The scraping API accepts user-supplied URLs.

This creates a potential Server-Side Request Forgery (SSRF) risk if arbitrary internal addresses can be requested by the server.

A production implementation should validate submitted URLs and prevent access to:

* Localhost.
* Private IP addresses.
* Internal network services.
* Cloud metadata endpoints.
* Other restricted network destinations.

---

# 26. Scraping Resource Limits

Because Puppeteer launches browser processes, unrestricted API access can consume significant server resources.

Production deployments should therefore limit:

* Number of concurrent scraping jobs.
* Maximum URLs per request.
* Browser/page lifetime.
* Navigation timeout.
* Memory usage.
* Request frequency.

---

# 27. API Limitations

The current API has several architectural limitations:

1. Scraping depends on external website structure.
2. The API does not provide a production job queue.
3. JSON storage limits scalability.
4. Authentication is prototype-level.
5. Administrative authorization requires strengthening.
6. Proxy rotation is not implemented.
7. Scheduled scraping is incomplete.
8. Comprehensive retry handling is not implemented.
9. Scraping can be resource-intensive.
10. Product extraction is selector-dependent.

---

# 28. Example End-to-End API Usage

### Step 1 — Submit Product URL

```bash
curl -X POST http://localhost:3000/api/scrape \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com/product"}'
```

### Step 2 — Backend Processing

The server:

```text
Receives URL
    ↓
Launches Puppeteer
    ↓
Loads target page
    ↓
Extracts product information
    ↓
Extracts reviews
    ↓
Runs sentiment analysis
    ↓
Calculates estimated sales
    ↓
Stores result
```

### Step 3 — Receive Result

The backend returns the processed product information as JSON.

### Step 4 — Frontend Analysis

The frontend uses the response to:

* Display product information.
* Display sentiment.
* Display estimated sales.
* Generate charts.
* Compare products.
* Export results.

---

# 29. API Development Recommendations

For future versions, the API could be improved by introducing:

### Validation

Use schema-based validation for incoming requests.

### Authentication

Implement secure authentication and authorization middleware.

### Database

Replace JSON persistence with a database for production use.

### Background Jobs

Move Puppeteer workloads into worker processes.

### Queue

Introduce a job queue for long-running scraping tasks.

### Rate Limiting

Protect endpoints from excessive requests.

### Monitoring

Add structured logs, metrics, and health monitoring.

### API Versioning

Introduce versioned routes such as:

```text
/api/v1/scrape
```

This would allow future API changes without immediately breaking existing clients.

---

# 30. Current API Capability Summary

| Capability                   | Status                         |
| ---------------------------- | ------------------------------ |
| Product scraping             | Implemented                    |
| Puppeteer browser automation | Implemented                    |
| Product extraction           | Implemented                    |
| Review extraction            | Implemented                    |
| Sentiment analysis           | Implemented                    |
| Sales estimation             | Implemented                    |
| JSON persistence             | Implemented                    |
| Frontend API integration     | Implemented                    |
| Product comparison           | Implemented                    |
| JSON export                  | Implemented                    |
| CSV export                   | Implemented                    |
| Server health                | Implemented                    |
| Administrative interface     | Implemented                    |
| User functionality           | Implemented at prototype level |
| Production authentication    | Not implemented                |
| Production authorization     | Not implemented                |
| Proxy rotation               | Not implemented                |
| Production job queue         | Not implemented                |
| Comprehensive rate limiting  | Not implemented                |
| Comprehensive retry system   | Not implemented                |
| Production database          | Not implemented                |
| Complete scheduled scraping  | Partially implemented          |

---

# 31. Summary

The Scrape Wise API provides the backend interface between the browser-based application and the scraping/analysis pipeline.

Its primary responsibility is to:

```text
Receive Request
      ↓
Process Product URL
      ↓
Scrape Website
      ↓
Extract Product Data
      ↓
Analyze Reviews
      ↓
Calculate Metrics
      ↓
Persist Results
      ↓
Return Structured Data
```

The API is suitable for the current prototype architecture.

For production use, the API would require stronger authentication, authorization, request validation, SSRF protection, rate limiting, persistent database storage, background job processing, monitoring, and more robust error handling.

````

That completes the **four Scrape Wise documentation files**:

```text
scrape-wise/
├── system-overview.md
├── developer-setup-guide.md
├── scraping-pipeline-guide.md
└── api-reference.md
````
