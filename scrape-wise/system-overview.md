# Scrape Wise — System Overview

## 1. Introduction

Scrape Wise is a Node.js and browser-based web scraping and product-analysis application.

The application accepts product URLs through a browser interface, uses Puppeteer to load and inspect web pages, extracts product information using page-specific selectors, processes customer reviews using Sentiment.js, estimates sales from review counts, stores scraped results as JSON, and presents the results through browser-based dashboards and charts.

The application also contains administrative functionality, product comparison, authentication-related functionality, scheduled-processing code, and client-side JSON/CSV export.

Scrape Wise is best understood as a **prototype web-scraping and product-analysis application**, rather than a production-ready e-commerce intelligence platform.

---

## 2. Problem Statement

Collecting product information manually from web pages is time-consuming and difficult to repeat consistently.

Scrape Wise addresses this problem by automating several stages of the process:

1. Accepting product URLs from a user.
2. Opening the supplied pages through a browser automation engine.
3. Extracting product information from the page.
4. Collecting available review information.
5. Classifying reviews as positive, neutral, or negative.
6. Estimating sales from the number of reviews.
7. Persisting the resulting data.
8. Presenting the information through a browser interface.
9. Comparing products.
10. Exporting collected information for further use.

---

## 3. Primary Goals

The implemented application focuses on:

- Browser-based product data extraction.
- Automated review sentiment analysis.
- Product comparison.
- Product-data visualization.
- JSON-based data persistence.
- Client-side JSON and CSV export.
- Basic authentication functionality.
- Administrative interfaces.
- Server monitoring.
- Support for scheduled scraping through `node-cron`.

Some of these capabilities are incomplete or inconsistent in the current implementation and should therefore not be represented as production-ready functionality.

---

## 4. Technology Stack

### Backend

- Node.js
- Express.js
- Puppeteer
- Puppeteer Extra
- Puppeteer Stealth Plugin
- Sentiment.js
- node-cron
- CORS
- Body Parser
- Cookie Parser
- Node.js `fs` module
- Node.js `path` module
- Node.js `os` module

### Frontend

- HTML
- CSS
- Vanilla JavaScript
- Fetch API
- Browser Local Storage
- Browser Cookies
- Chart.js

The frontend does not use React, Angular, Vue, or another frontend framework.

### Persistence

The current implementation uses JSON files rather than a database.

---

## 5. Repository Structure

```text
scrape-wise/
│
├── .gitignore
├── LICENSE
├── README.md
│
├── admin.html
├── admin.js
├── analysis.html
├── analysis.js
├── index.html
├── login.html
├── script.js
├── styles.css
│
├── package.json
├── package-lock.json
├── server.js
└── user.json
````

The application is intentionally simple in structure, with most backend functionality contained in `server.js` and page-specific browser logic contained in separate JavaScript files.

---

## 6. High-Level Architecture

Scrape Wise follows a simple client-server architecture.

```text
                    Browser
                       |
                       |
        +--------------+--------------+
        |              |              |
    index.html     analysis.html   admin.html
        |              |              |
    script.js      analysis.js      admin.js
        |              |              |
        +--------------+--------------+
                       |
                    HTTP/Fetch
                       |
                       v
                 Express Server
                   server.js
                       |
          +------------+------------+
          |            |            |
      Scraping     Analysis      Admin
       Logic       Logic        Endpoints
          |
          v
      Puppeteer
          |
          v
     Target Website
          |
          v
   Extracted Product Data
          |
          v
   Sentiment Processing
          |
          v
    Sales Estimation
          |
          v
    JSON File Storage
          |
          v
       Frontend
          |
          +------> Charts
          |
          +------> Comparison
          |
          +------> Export
```

---

## 7. Core Processing Flow

The main scraping flow is:

```text
Product URL
    ↓
Frontend Form
    ↓
POST /scrape
    ↓
Input Validation
    ↓
Puppeteer Browser
    ↓
Stealth Plugin
    ↓
User-Agent Configuration
    ↓
Request Interception
    ↓
Page Navigation
    ↓
DOM Loading
    ↓
Selector-Based Extraction
    ↓
Product Information
    ↓
Review Extraction
    ↓
Sentiment Analysis
    ↓
Sales Estimation
    ↓
Structured Result
    ↓
JSON Persistence
    ↓
HTTP Response
    ↓
Frontend Display
    ↓
Chart Visualization
    ↓
JSON / CSV Export
```

---

## 8. Core Product Data

The scraper extracts product information such as:

* Product title
* Price
* Reviews
* Rating
* Country/origin information
* Review count
* Sentiment summary
* Estimated sales

The exact values available depend on whether the target page contains the selectors expected by the scraper.

---

## 9. Review Sentiment Analysis

Reviews are processed through Sentiment.js.

Each review is analyzed and classified based on its sentiment score:

```text
score > 0  → Positive
score < 0  → Negative
score = 0  → Neutral
```

The resulting counts are aggregated into:

```json
{
  "sentimentSummary": {
    "positive": 0,
    "neutral": 0,
    "negative": 0
  }
}
```

Sentiment analysis is therefore part of the implemented processing pipeline rather than being merely a planned feature.

---

## 10. Sales Estimation

The application derives an estimated sales value from the review count.

The current implementation uses a fixed review-to-sales assumption:

```text
estimatedSales = reviewCount / 0.05
```

This is an estimate and should not be interpreted as an actual sales figure.

The ratio is an application assumption rather than verified sales data from the target website.

---

## 11. Product Comparison

The application contains a product comparison flow.

The backend exposes comparison functionality and the frontend provides a corresponding presentation layer.

The comparison functionality allows information from products to be displayed together for analysis.

---

## 12. Visualization

Chart.js is used to visualize processed information.

The application includes visualization for:

* Sentiment distribution
* Estimated sales

The charts are generated on the client side after the scraping response is received.

---

## 13. Data Persistence

Scraped information is stored using JSON files.

This approach is suitable for a prototype or local development environment but does not provide the characteristics of a production database.

The current implementation does not provide a persistent relational or distributed database layer.

---

## 14. Authentication

The repository contains basic login-related functionality and user information storage.

However, the current authentication implementation should not be considered production-grade.

The source-based technical assessment identifies authentication sessions and production authorization as incomplete.

---

## 15. Administrative Functionality

The application contains an administrative interface.

Administrative functionality includes areas such as:

* User listing
* Scraping-log inspection
* Server health information
* Administrative data handling

Some administrative functionality is incomplete or inconsistently integrated into the backend.

---

## 16. Scheduled Processing

The project includes `node-cron` and scheduled scraping code.

The intended architecture allows scraping operations to be triggered automatically using cron expressions.

However, the current implementation contains incomplete scheduled-processing logic and references components that are not consistently available.

Therefore, automated scheduled scraping should be considered **incomplete in the current implementation**.

---

## 17. Current Implementation Classification

Based on the implemented code, Scrape Wise is best classified as:

> A Node.js/Express prototype web-scraping and product-analysis application with browser-based frontend pages, Puppeteer-driven extraction, rule-based review sentiment analysis, JSON persistence, product comparison, and client-side visualization.

It should not currently be presented as a fully production-ready e-commerce intelligence platform.

---

## 18. Implemented vs Incomplete Functionality

| Capability                 | Current Status                    |
| -------------------------- | --------------------------------- |
| Browser scraping           | Implemented                       |
| Puppeteer integration      | Implemented                       |
| Stealth plugin integration | Implemented                       |
| Product extraction         | Implemented                       |
| Sentiment analysis         | Implemented                       |
| Product comparison         | Implemented                       |
| Chart visualization        | Implemented                       |
| JSON export                | Implemented                       |
| CSV export                 | Implemented                       |
| Basic login lookup         | Implemented                       |
| User listing               | Implemented                       |
| User blocking storage      | Partially implemented             |
| Authentication sessions    | Incomplete                        |
| JWT authentication         | Not implemented in active flow    |
| Proxy rotation             | Not implemented in active scraper |
| Scheduled scraping         | Incomplete                        |
| Scraping-log integration   | Incomplete/inconsistent           |
| Power BI integration       | Placeholder                       |
| Database persistence       | Not implemented                   |
| Automated tests            | Not implemented                   |
| Production authorization   | Not implemented                   |

---

## 19. Strengths

The current implementation demonstrates several meaningful technical concepts:

* Real browser automation through Puppeteer.
* Puppeteer Stealth Plugin integration.
* Concurrent processing of supplied URLs.
* Selector-based product extraction.
* Graceful fallback values when selectors are unavailable.
* Actual review sentiment processing.
* Product comparison.
* Client-side data export.
* Browser-based visualization.
* Administrative interfaces.
* Basic server monitoring.

---

## 20. Technical Limitations

The current implementation has several limitations:

1. Scraping depends on specific CSS selectors.
2. Website structure changes can break extraction.
3. Dynamic content may not always be available when extraction occurs.
4. There is no robust retry or recovery mechanism.
5. Rate limiting and exponential backoff are not comprehensively implemented.
6. JSON files are used instead of a production database.
7. Sales figures are estimates based on an assumed review-to-sales ratio.
8. Sentiment analysis can produce inaccurate classifications.
9. Authentication is not production-grade.
10. Administrative authorization is incomplete.
11. Proxy rotation is not implemented in the active scraper.
12. Scheduled scraping is incomplete.
13. Scraping-log integration is inconsistent.
14. There is no automated test suite.
15. Some dependencies and configuration values are unused or incomplete.
16. Some source files contain duplicated or overlapping functionality.

---

## 21. Recommended Future Improvements

Potential improvements include:

* Refactor the backend into separate routes and services.
* Introduce proper authentication and authorization middleware.
* Replace JSON persistence with PostgreSQL or MongoDB.
* Implement robust retry and exponential backoff.
* Add configurable rate limiting.
* Implement reliable scheduled jobs.
* Introduce persistent scraping history.
* Improve selector management.
* Add automated unit and integration tests.
* Improve sentiment analysis using a more advanced NLP model.
* Add production-grade logging.
* Add proper administrative authorization.
* Remove duplicate routes and handlers.
* Replace placeholder integrations with actual implementations.
* Introduce worker processes for large scraping workloads.

---

## 22. Overall Assessment

Scrape Wise demonstrates a complete prototype concept rather than a simple static website.

The strongest implemented flow is:

```text
URL
 ↓
Puppeteer
 ↓
Selector-Based Extraction
 ↓
Review Extraction
 ↓
Sentiment Analysis
 ↓
Sales Estimation
 ↓
Structured JSON
 ↓
Frontend Visualization
 ↓
Export
```

The project is particularly useful as a portfolio example because it demonstrates browser automation, backend API development, frontend integration, data processing, sentiment analysis, visualization, and file-based persistence within one application.

````

This is the version I would use rather than the earlier inflated description. The source-derived assessment specifically says the core scraping pipeline is the strongest implemented portion, while several surrounding features remain incomplete. :contentReference[oaicite:1]{index=1}

---

## 2. `developer-setup-guide.md`

```md
# Scrape Wise — Developer Setup Guide

## 1. Overview

This guide explains how to install, configure, run, and verify the Scrape Wise application locally.

Scrape Wise is a Node.js/Express application with a browser-based frontend. The backend serves the HTML pages and exposes the scraping and analysis functionality.

---

## 2. Prerequisites

Install the following before starting:

- Node.js
- npm
- Git
- A modern web browser
- Sufficient system resources for Puppeteer and Chromium

Puppeteer downloads and runs a browser during installation and scraping operations, so the local environment must be capable of running Chromium.

---

## 3. Clone the Repository

Clone the repository:

```bash
git clone https://github.com/shravyaks275/scrape-wise.git
cd scrape-wise
````

---

## 4. Install Dependencies

Install the dependencies declared in `package.json`:

```bash
npm install
```

This installs the packages required by the application, including:

* Express
* Puppeteer
* Puppeteer Extra
* Puppeteer Stealth Plugin
* Sentiment
* node-cron
* Chart.js-related frontend dependencies
* CORS
* Cookie Parser
* Body Parser
* dotenv

---

## 5. Project Structure

After cloning the repository, the main structure is:

```text
scrape-wise/
│
├── admin.html
├── admin.js
├── analysis.html
├── analysis.js
├── index.html
├── login.html
├── script.js
├── styles.css
│
├── server.js
├── package.json
├── package-lock.json
├── user.json
│
├── .gitignore
└── LICENSE
```

---

## 6. Backend Entry Point

The primary backend entry point is:

```text
server.js
```

The server uses Express to:

* Serve frontend pages.
* Expose API-style routes.
* Receive scraping requests.
* Run Puppeteer.
* Process extracted information.
* Perform sentiment analysis.
* Persist data.
* Provide administrative functionality.
* Provide server-health information.

---

## 7. Environment Configuration

The project can use environment variables for server configuration.

Example configuration:

```env
PORT=3000
NODE_ENV=development
HEADLESS=true
TIMEOUT=30000
LOG_LEVEL=info
USE_PROXY=false
PROXY_LIST=
SENTIMENT_THRESHOLD=0.5
CRON_TIME=0 8 * * *
```

### Important

These values describe the intended configuration used by the application. Not every configuration value represents an actively functioning feature in the current implementation.

For example, proxy-related configuration exists, but proxy rotation is not implemented in the active scraper.

---

## 8. Configuration Variables

| Variable              | Purpose                               |
| --------------------- | ------------------------------------- |
| `PORT`                | Port used by the Express server       |
| `NODE_ENV`            | Application environment               |
| `HEADLESS`            | Controls Puppeteer browser visibility |
| `TIMEOUT`             | Browser/page timeout                  |
| `LOG_LEVEL`           | Intended logging configuration        |
| `USE_PROXY`           | Intended proxy configuration          |
| `PROXY_LIST`          | Intended proxy list                   |
| `SENTIMENT_THRESHOLD` | Sentiment configuration value         |
| `CRON_TIME`           | Intended scheduled-job expression     |

---

## 9. Start the Application

The project defines the following npm start command:

```bash
npm start
```

This executes:

```bash
node server.js
```

The Express server listens on the configured port.

If the default port is used, access the application through:

```text
http://localhost:3000
```

---

## 10. Application Pages

The repository contains several frontend pages.

### Home Page

```text
http://localhost:3000/
```

The home page provides the primary scraping interface.

### Login Page

```text
http://localhost:3000/login.html
```

Provides the login interface.

### Analysis Page

```text
http://localhost:3000/analysis.html
```

Provides analysis and visualization functionality.

### Admin Page

```text
http://localhost:3000/admin.html
```

Provides administrative functionality.

---

## 11. Verify the Server

After starting the application, verify that the server is responding.

Open:

```text
http://localhost:3000
```

The Scrape Wise home page should be displayed.

---

## 12. Server Health Check

The application exposes a server-health endpoint.

Example:

```bash
curl http://localhost:3000/admin/server-health
```

The response provides server status information such as:

* Server status
* Process uptime
* Memory usage

---

## 13. Running a Scrape

The main scraping interface accepts one or more product URLs.

The frontend sends the scraping request to the Express backend.

The backend then:

1. Validates the supplied URLs.
2. Launches Puppeteer.
3. Applies Puppeteer Extra/Stealth functionality.
4. Configures browser behavior.
5. Navigates to the supplied page.
6. Waits for page content.
7. Extracts product information.
8. Extracts reviews.
9. Runs sentiment analysis.
10. Calculates estimated sales.
11. Builds the result object.
12. Persists the data.
13. Returns the result to the frontend.

---

## 14. Browser Automation

Puppeteer is responsible for browser-based extraction.

The application uses a real browser environment rather than performing only HTTP requests.

This allows JavaScript-rendered pages to be processed more effectively than a basic HTTP scraper.

---

## 15. Production Execution

The application can be started with:

```bash
NODE_ENV=production npm start
```

For production deployment, additional infrastructure should be considered, including:

* Process management
* Centralized logging
* Persistent storage
* Authentication and authorization
* Monitoring
* Retry handling
* Rate limiting

---

## 16. Troubleshooting

### `npm` command not found

Install Node.js and npm.

### Puppeteer installation problems

Verify that the local system has sufficient disk space, memory, and permissions for Puppeteer's browser installation.

### Port already in use

Change the configured `PORT` value or stop the process currently using port 3000.

### Scraping returns missing values

The scraper relies on page-specific CSS selectors. If the target website has changed its HTML structure, selectors may no longer match.

### Scraping fails on dynamic pages

Some pages load content asynchronously after the initial DOM event. Additional wait conditions may be required for those websites.

### Scheduled scraping does not execute

The repository contains scheduling-related code, but the current scheduled-processing implementation is incomplete. Do not assume that setting `CRON_TIME` alone enables reliable automated scraping.

---

## 17. Development Notes

The current project is structured as a prototype rather than a fully modular production application.

Most backend functionality is contained in `server.js`, while frontend behavior is separated into page-specific JavaScript files.

Future development should consider separating:

```text
routes/
services/
middleware/
storage/
config/
```

from the main server file.

---

## 18. Important Implementation Notes

The following should be understood before deploying the project:

* JSON files are used for persistence.
* Database persistence is not implemented.
* Production authentication is not implemented.
* Proxy rotation is not implemented in the active scraper.
* Scheduled scraping is incomplete.
* Automated tests are not present.
* Administrative authorization requires further work.
* Some configuration values are placeholders.

The project can therefore be run locally as a prototype, but additional engineering is required for production deployment.

````

---

## 3. `scraping-pipeline-guide.md`

```md
# Scrape Wise — Web Scraping Pipeline Technical Guide

## 1. Overview

The scraping pipeline is the core technical component of Scrape Wise.

It combines:

- Express.js
- Puppeteer
- Puppeteer Extra
- Puppeteer Stealth Plugin
- CSS selector-based extraction
- Sentiment.js
- Sales estimation
- JSON persistence
- Frontend visualization

The pipeline converts a product URL into structured product-analysis data.

---

## 2. Pipeline

```text
Product URL
    ↓
Frontend Submission
    ↓
Express Scraping Endpoint
    ↓
URL Validation
    ↓
Puppeteer Launch
    ↓
Stealth Configuration
    ↓
User-Agent Configuration
    ↓
Request Interception
    ↓
Page Navigation
    ↓
DOM Loading
    ↓
Product Selector Matching
    ↓
Product Extraction
    ↓
Review Extraction
    ↓
Sentiment Analysis
    ↓
Sales Estimation
    ↓
Result Assembly
    ↓
JSON Persistence
    ↓
HTTP Response
    ↓
Frontend Visualization
````

---

## 3. URL Submission

The user provides a product URL through the frontend scraping interface.

The frontend sends the request to the Express backend.

The backend verifies that the required URL input is available before starting the scraping process.

---

## 4. Puppeteer Browser Launch

The backend uses Puppeteer to launch a browser instance.

Puppeteer allows the application to execute JavaScript and interact with pages through a browser context.

This is useful for websites where product information is rendered dynamically.

---

## 5. Puppeteer Extra

Puppeteer Extra is used as an extension layer around Puppeteer.

The project also integrates the Puppeteer Stealth Plugin.

---

## 6. Stealth Plugin

The Stealth Plugin is intended to reduce obvious browser-automation signals.

It should be described as a detection-mitigation mechanism rather than a guarantee that scraping will remain undetected.

A target website can still identify or block automated traffic.

---

## 7. User-Agent Configuration

The scraper configures browser identification information before navigating to the target page.

This is part of the browser-request configuration used during scraping.

---

## 8. Request Interception

The scraper can intercept browser requests and prevent unnecessary resource loading.

Resources such as:

* Images
* Stylesheets
* Fonts

can be blocked to reduce unnecessary network traffic.

This helps reduce bandwidth and browser processing overhead.

---

## 9. Page Navigation

Puppeteer navigates to the supplied URL.

The scraper waits for the DOM content to become available before attempting extraction.

This does not guarantee that every asynchronously rendered element has finished loading.

---

## 10. Selector-Based Extraction

The scraper uses CSS selectors to identify product information.

The extraction process can retrieve fields including:

* Product title
* Price
* Reviews
* Rating
* Country/origin
* Review count

The exact extraction depends on the HTML structure of the target website.

---

## 11. Selector Dependency

A major technical characteristic of the application is its dependency on selectors.

For example:

```text
Target Website HTML
        ↓
Expected CSS Selector
        ↓
Matching Element
        ↓
Extracted Value
```

If the target website changes its HTML structure, the selector may stop matching.

The scraper includes fallback behavior for unavailable selectors, but it cannot automatically discover new selectors.

---

## 12. Review Extraction

Product reviews are collected from the page when the expected review elements are available.

The resulting review text is passed to the sentiment-processing stage.

---

## 13. Sentiment Analysis

Scrape Wise uses Sentiment.js.

The basic classification process is:

```text
Review
  ↓
Sentiment.js
  ↓
Sentiment Score
  ↓
Classification
```

The application categorizes reviews using the score:

```text
score > 0 → Positive

score < 0 → Negative

score = 0 → Neutral
```

The resulting counts are aggregated.

Example:

```json
{
  "sentimentSummary": {
    "positive": 12,
    "neutral": 4,
    "negative": 3
  }
}
```

---

## 14. Sentiment Limitations

Sentiment.js is not a domain-specific machine-learning model trained specifically for product reviews.

Potential problems include:

* Sarcasm
* Irony
* Slang
* Technical terminology
* Non-English reviews
* Context-dependent language

For higher-accuracy production sentiment analysis, a more advanced NLP model could be introduced.

---

## 15. Sales Estimation

The application estimates sales from review count.

The current calculation uses:

```text
estimatedSales = reviewCount / 0.05
```

The calculation is based on an assumed relationship between reviews and purchases.

It should therefore be treated as an estimate.

It does not represent verified sales data.

---

## 16. Result Assembly

After extraction and processing, the application assembles structured product information.

The resulting structure can contain:

```text
productTitle
price
reviews
rating
countryOrigin
reviewsCount
sentimentSummary
estimatedSales
```

---

## 17. Persistence

The processed result is serialized and stored using JSON-based persistence.

This provides simple local storage but does not provide the reliability or scalability of a production database.

---

## 18. Frontend Processing

After receiving the backend response, the frontend can:

* Display raw data.
* Update sentiment charts.
* Update sales charts.
* Compare products.
* Export data.

---

## 19. Visualization Pipeline

```text
Scraped Result
      ↓
HTTP Response
      ↓
Frontend JavaScript
      ↓
Data Processing
      ↓
Chart.js
      ↓
Sentiment Chart
      +
Sales Chart
```

---

## 20. Concurrent Scraping

The implementation supports concurrent processing of supplied URLs using `Promise.all()`.

This allows multiple supplied URLs to be processed without waiting for each URL to finish before starting the next one.

However, concurrency should be controlled carefully when scaling the application because excessive concurrent browser sessions can consume significant system resources.

---

## 21. Error Handling

The scraper provides fallback values when individual selectors cannot be found.

However, the current implementation does not provide comprehensive retry and recovery behavior.

A failed scraping task may therefore result in incomplete or lost data.

---

## 22. Proxy Handling

The project contains proxy-related configuration and references.

However, proxy rotation is **not implemented in the active scraper flow**.

Therefore, professional documentation should not describe Scrape Wise as currently providing functional round-robin proxy rotation.

---

## 23. Scheduled Scraping

The project includes `node-cron` and scheduled scraping code.

The intended flow is:

```text
Cron Trigger
    ↓
Scheduled Scraping Function
    ↓
scrapeWebsite()
    ↓
Merge Results
    ↓
Persist Data
```

However, the current scheduled implementation is incomplete and contains references to components that are not consistently available.

---

## 24. Technical Limitations

The scraping pipeline currently has the following limitations:

1. CSS selectors are dependent on target-site HTML.
2. Website layout changes can break extraction.
3. Dynamic content may require additional waiting logic.
4. There is no robust exponential-backoff mechanism.
5. Rate limiting is not comprehensively handled.
6. There is no reliable retry system.
7. JSON persistence limits scalability.
8. Sales estimation is assumption-based.
9. Sentiment accuracy varies by language and context.
10. Proxy rotation is not active.
11. Scheduled processing is incomplete.

---

## 25. Recommended Improvements

Potential improvements include:

* Centralized selector configuration.
* Site-specific scraper adapters.
* Retry with exponential backoff.
* Configurable rate limiting.
* Better dynamic-content detection.
* Persistent scraping history.
* Database-backed storage.
* Worker-based scraping.
* Queue-based processing.
* Advanced sentiment models.
* Automated scraper tests.
* Better monitoring and logging.

---

## 26. Summary

The implemented core pipeline is:

```text
URL
 ↓
Puppeteer
 ↓
Selector-Based Extraction
 ↓
Review Extraction
 ↓
Sentiment.js
 ↓
Sales Estimation
 ↓
JSON Storage
 ↓
Frontend Visualization
 ↓
Export
```

This pipeline represents the strongest implemented portion of Scrape Wise.

````

The source-based technical assessment explicitly identifies this pipeline as the strongest implemented part of the project. :contentReference[oaicite:2]{index=2}

---

## 4. `api-reference.md`

```md
# Scrape Wise — API Reference

## 1. Overview

Scrape Wise exposes HTTP endpoints through its Express.js backend.

The API is primarily used by the browser frontend to:

- Start scraping operations.
- Compare product information.
- Retrieve administrative information.
- Inspect server health.
- Handle authentication-related operations.

The API is implemented directly in `server.js`.

> **Important:** This reference documents the routes identified in the current implementation. It does not add hypothetical endpoints that are not implemented.

---

## 2. Base URL

For local development:

```text
http://localhost:3000
````

---

# 3. Scraping

## POST `/scrape`

Starts a scraping operation for supplied product URLs.

### Purpose

The endpoint receives product URLs and uses Puppeteer to extract product information.

### Request

The request contains the URLs to be scraped.

Example conceptual request:

```json
{
  "urls": [
    "https://example.com/product"
  ]
}
```

### Processing

The backend:

1. Validates the URL input.
2. Launches Puppeteer.
3. Configures browser behavior.
4. Navigates to the supplied URL.
5. Extracts product information.
6. Extracts reviews.
7. Performs sentiment analysis.
8. Estimates sales.
9. Stores the resulting data.
10. Returns the processed result.

### Response

The response contains the scraped product data and associated analysis.

Typical data fields include:

```json
{
  "productTitle": "Example Product",
  "price": "100",
  "reviewsCount": 20,
  "rating": 4.2,
  "estimatedSales": 400,
  "sentimentSummary": {
    "positive": 15,
    "neutral": 3,
    "negative": 2
  }
}
```

The exact response depends on the target page and selectors.

---

# 4. Product Comparison

## Comparison Endpoint

The application contains a separate comparison API and corresponding frontend presentation layer.

The endpoint is used to compare product information collected by the application.

Because the current implementation contains overlapping/duplicate route definitions, the exact route behavior should be verified directly against the active `server.js` implementation before integrating an external client.

---

# 5. Authentication

The application contains basic authentication-related functionality.

The current implementation uses user information stored locally rather than a production identity provider.

Authentication should therefore be considered prototype-level functionality.

The technical assessment identifies:

* Basic login lookup as implemented.
* User listing as implemented.
* Authentication sessions as incomplete.
* JWT authentication as not implemented in the active flow.
* Production authorization as not implemented.

---

# 6. Administrative Endpoints

Administrative functionality is exposed through Express routes.

The application contains functionality related to:

* User listing.
* Scraping logs.
* Server health.
* Administrative data management.

---

## GET `/admin/server-health`

Returns server status information.

### Example

```bash
curl http://localhost:3000/admin/server-health
```

### Response

The response contains server information such as:

```json
{
  "status": "Server is running...",
  "uptime": "...",
  "memoryUsage": "..."
}
```

---

## Administrative Authorization

The current administrative endpoints should not be treated as production-secured APIs.

Production authorization middleware is not implemented.

---

# 7. Scraping Logs

The application contains scraping-log functionality.

However, the source contains overlapping/duplicate scraping-log route implementations.

Therefore, integrations depending on a specific scraping-log response contract should be verified against the active route definition.

---

# 8. Data Export

JSON and CSV export are implemented on the client side.

The frontend does not require a dedicated backend download endpoint for these exports.

The flow is:

```text
API Response
    ↓
Frontend JavaScript
    ↓
Data Formatting
    ↓
Browser Download
```

---

# 9. Error Handling

The scraping endpoint can return errors when scraping cannot be completed.

Possible causes include:

* Invalid URL input.
* Navigation failure.
* Target page failure.
* Missing selectors.
* Browser launch problems.
* Unexpected page structure.

Because scraping depends on external websites, API consumers should not assume that every valid URL will produce complete product data.

---

# 10. API Architecture

The general request flow is:

```text
Browser
   ↓
Fetch API
   ↓
Express Route
   ↓
Scraping / Processing Logic
   ↓
JSON Result
   ↓
HTTP Response
   ↓
Frontend
```

---

# 11. API Limitations

The current API layer has several limitations:

* Routes are concentrated in `server.js`.
* Some routes are duplicated.
* Administrative authorization is incomplete.
* Authentication is prototype-level.
* Error contracts are not consistently formalized.
* Scraping results depend on target website structure.
* JSON persistence is used instead of a database.
* There is no automated API test suite.

---

# 12. API Development Recommendations

A future version should separate API functionality into:

```text
routes/
├── scrape.routes.js
├── compare.routes.js
├── auth.routes.js
└── admin.routes.js
```

Business logic should then move into:

```text
services/
├── scraper.service.js
├── sentiment.service.js
├── comparison.service.js
├── logging.service.js
└── scheduler.service.js
```

Authentication should be implemented through dedicated middleware:

```text
middleware/
├── auth.middleware.js
└── admin.middleware.js
```

This would make the API easier to test, maintain, and secure.

```

---

These four are the **core Scrape Wise portfolio documents** I would actually keep. The earlier source-based review also identified duplicated `/scrape` and `/admin/scraping-logs` routes, multiple overlapping frontend handlers, server-side code inside `admin.js`, placeholder configuration, and undefined scheduled-scraping variables. :contentReference[oaicite:3]{index=3}

I would **not create a separate ML Technical Guide for Scrape Wise** like you did for Stray Shield, because the actual project is using **Sentiment.js rather than an ML model/pipeline of the same kind**. The sentiment-processing section belongs in the scraping-pipeline technical guide. :contentReference[oaicite:4]{index=4}
```
