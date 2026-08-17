## 2. `developer-setup-guide.md`

Copy everything below directly into that file:

````md
# Scrape Wise — Developer Setup Guide

## 1. Overview

This guide explains how to install, configure, run, and verify the Scrape Wise application locally.

Scrape Wise is a Node.js and Express-based web application with a browser-based frontend. The backend handles web scraping, product analysis, sentiment processing, data persistence, and administrative functionality.

The application uses Puppeteer for browser automation and stores application data using JSON files.

---

## 2. Prerequisites

Before setting up Scrape Wise, ensure the following are installed:

- Node.js
- npm
- Git
- A modern web browser
- An environment capable of running Chromium through Puppeteer

Puppeteer requires a browser runtime, so the development machine must have sufficient memory, storage, and permissions to install and execute its browser components.

---

## 3. Clone the Repository

Clone the repository using Git:

```bash
git clone https://github.com/shravyaks275/scrape-wise.git
````

Navigate into the project directory:

```bash
cd scrape-wise
```

---

## 4. Install Dependencies

Install the dependencies defined in `package.json`:

```bash
npm install
```

The project includes dependencies for:

* Express.js
* Puppeteer
* Puppeteer Extra
* Puppeteer Stealth Plugin
* Sentiment.js
* node-cron
* CORS
* Body Parser
* Cookie Parser
* dotenv

The exact installed versions are determined by `package-lock.json`.

---

## 5. Project Structure

The main project structure is:

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

### Main Files

| File                | Purpose                                                                                                         |
| ------------------- | --------------------------------------------------------------------------------------------------------------- |
| `server.js`         | Express server, scraping logic, API routes, sentiment processing, persistence, and administrative functionality |
| `script.js`         | Main frontend scraping interface and client-side functionality                                                  |
| `analysis.js`       | Product analysis and visualization functionality                                                                |
| `admin.js`          | Administrative page functionality                                                                               |
| `index.html`        | Main application page                                                                                           |
| `analysis.html`     | Analysis and visualization page                                                                                 |
| `admin.html`        | Administrative interface                                                                                        |
| `login.html`        | Login interface                                                                                                 |
| `styles.css`        | Application styling                                                                                             |
| `user.json`         | Local user data                                                                                                 |
| `package.json`      | Project metadata, dependencies, and npm scripts                                                                 |
| `package-lock.json` | Locked dependency versions                                                                                      |

---

## 6. Backend Entry Point

The main backend entry point is:

```text
server.js
```

The Express server is responsible for:

* Starting the HTTP server.
* Serving frontend resources.
* Receiving scraping requests.
* Launching Puppeteer.
* Extracting product information.
* Extracting reviews.
* Performing sentiment analysis.
* Calculating estimated sales.
* Persisting scraped information.
* Providing administrative endpoints.
* Providing server-health information.

---

## 7. Starting the Application

The project defines an npm start command that runs the Express server.

Start the application with:

```bash
npm start
```

The command executes the Node.js server entry point.

After the server starts, open the application in a browser using the configured server port.

For the default local configuration, this is:

```text
http://localhost:3000
```

---

## 8. Application Pages

The project contains several HTML-based interfaces.

### Main Application

```text
http://localhost:3000/
```

The main page provides the primary scraping interface.

---

### Login Page

```text
http://localhost:3000/login.html
```

The login page provides the application's user login interface.

---

### Analysis Page

```text
http://localhost:3000/analysis.html
```

The analysis page presents processed product information and visualizations.

---

### Admin Page

```text
http://localhost:3000/admin.html
```

The admin page provides administrative functionality.

---

## 9. Environment Configuration

The application contains configuration values for server behavior.

Configuration can include values such as:

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

These values should be treated according to how they are actually consumed by the current implementation.

Not every configuration value represents an active production feature.

For example, proxy-related configuration exists, but functional proxy rotation is not implemented in the active scraping flow.

---

## 10. Configuration Reference

| Variable              | Purpose                                           |
| --------------------- | ------------------------------------------------- |
| `PORT`                | Port used by the Express server                   |
| `NODE_ENV`            | Application environment                           |
| `HEADLESS`            | Intended Puppeteer headless-browser configuration |
| `TIMEOUT`             | Intended browser/page timeout configuration       |
| `LOG_LEVEL`           | Intended logging configuration                    |
| `USE_PROXY`           | Proxy-related configuration                       |
| `PROXY_LIST`          | Proxy-related configuration                       |
| `SENTIMENT_THRESHOLD` | Sentiment-processing configuration                |
| `CRON_TIME`           | Scheduled-processing configuration                |

Some of these settings are present in the project configuration but are not fully integrated into the active application flow.

---

## 11. Running the Scraper

After starting the server:

1. Open the main application.
2. Enter a supported product URL.
3. Submit the scraping request.
4. The frontend sends the request to the Express backend.
5. The backend launches Puppeteer.
6. Puppeteer loads the target page.
7. Product information is extracted.
8. Available reviews are processed.
9. Sentiment analysis is performed.
10. Estimated sales are calculated.
11. The resulting data is returned to the frontend.
12. The frontend displays the results.

---

## 12. Browser Automation

Puppeteer is responsible for browser automation.

The application uses Puppeteer Extra and the Puppeteer Stealth Plugin during scraping.

The browser automation process allows the scraper to interact with pages that require browser rendering or JavaScript execution.

---

## 13. Local Data Storage

The current project uses JSON-based storage.

The repository contains:

```text
user.json
```

Application data is therefore stored locally rather than through a relational database or external database service.

This approach is appropriate for a prototype or local development environment but has limitations for multi-user production deployments.

---

## 14. Authentication Setup

The project contains a login interface and user information stored in JSON.

The current authentication implementation should be considered prototype-level functionality.

The implementation does not provide a complete production authentication architecture with:

* JWT-based sessions
* Dedicated authentication middleware
* Persistent session management
* Production-grade password management
* Comprehensive authorization middleware

Therefore, the login functionality should not be treated as production-ready security.

---

## 15. Administrative Functionality

The project contains a dedicated administrative interface:

```text
admin.html
admin.js
```

Administrative functionality includes functionality related to:

* Users
* Scraping logs
* Server health
* Administrative information

The current implementation contains incomplete authorization controls, so administrative endpoints should not be exposed publicly without additional security measures.

---

## 16. Server Health Verification

The application exposes server-health functionality.

A local request can be made using:

```bash
curl http://localhost:3000/admin/server-health
```

The endpoint provides information related to:

* Server status
* Process uptime
* Memory usage

A successful response confirms that the Express backend is running and responding to requests.

---

## 17. Scraping Verification

To verify the scraping pipeline:

1. Start the application.
2. Open the main page.
3. Enter a supported product URL.
4. Submit the URL.
5. Wait for the scraping process to complete.
6. Confirm that product information is displayed.
7. Navigate to the analysis page.
8. Verify that sentiment and sales information are displayed.

Because extraction depends on target-page selectors, successful execution does not guarantee that every field will contain a value.

---

## 18. Supported Scraping Behavior

The scraper extracts information based on selectors defined in the implementation.

Depending on the target page, the result can include:

* Product title
* Price
* Rating
* Review information
* Review count
* Country/origin information
* Sentiment summary
* Estimated sales

The scraper therefore depends on the HTML structure of the target website.

---

## 19. Puppeteer Installation Problems

If `npm install` fails while installing Puppeteer:

1. Confirm Node.js is installed.
2. Confirm npm is available.
3. Check available disk space.
4. Check whether the environment permits browser installation.
5. Review the npm installation error for Chromium-related failures.

Puppeteer requires browser binaries and can therefore require more resources than a normal Node.js dependency installation.

---

## 20. Port Conflicts

If the application cannot start because the configured port is already in use:

1. Identify the process using the port.
2. Stop that process, or
3. Change the configured application port.

For example:

```env
PORT=3001
```

Then restart the application.

---

## 21. Scraping Returns Missing Data

A scrape may complete successfully while some fields are missing.

Possible reasons include:

* The target website changed its HTML structure.
* A selector no longer matches.
* Product information is dynamically loaded.
* Reviews are unavailable.
* The page uses a different layout.
* The supplied URL is not a supported product page.

The scraper uses selector-based extraction, so it cannot automatically adapt to arbitrary website layouts.

---

## 22. Dynamic Content Problems

Some websites load product information after the initial page load.

If the scraper executes extraction before the required content becomes available, fields may be missing.

Potential improvements include:

* Waiting for specific selectors.
* Waiting for network-idle conditions.
* Adding site-specific wait conditions.
* Adding configurable extraction delays.

---

## 23. Scraping Failure

If an entire scraping request fails:

1. Check the server terminal.
2. Check the browser console.
3. Verify the supplied URL.
4. Verify that the target website is accessible.
5. Check whether the target site blocks automated browsing.
6. Check Puppeteer/browser errors.
7. Check whether the site's HTML structure has changed.

---

## 24. Sentiment Analysis Problems

Sentiment analysis is performed using Sentiment.js.

Unexpected results may occur when reviews contain:

* Sarcasm
* Irony
* Mixed sentiment
* Slang
* Technical language
* Unsupported languages

The current implementation should therefore be treated as a lightweight sentiment classification mechanism rather than a highly accurate domain-specific NLP system.

---

## 25. Estimated Sales Problems

The application estimates sales using review information.

The estimate is based on an assumed relationship between review counts and sales.

Therefore:

* It is not actual sales information.
* It should not be interpreted as verified marketplace data.
* Changes to the review count directly affect the estimate.
* Different product categories may have very different review-to-purchase ratios.

---

## 26. Chart or Analysis Problems

If charts do not appear on the analysis page:

1. Confirm that scraping returned data.
2. Check the browser console.
3. Verify that the analysis page loaded correctly.
4. Check whether the expected data structure was returned.
5. Verify that Chart.js is available to the page.
6. Reload the analysis page after completing a scrape.

The charts depend on data returned by the scraping and analysis pipeline.

---

## 27. JSON Storage Problems

If locally stored data is missing or incorrect:

1. Check the relevant JSON files.
2. Verify that the application has permission to read and write the files.
3. Check the server terminal for file-system errors.
4. Verify that the JSON structure is valid.

JSON-based persistence is not designed for concurrent high-volume production access.

---

## 28. Scheduled Scraping Problems

The project contains `node-cron` and scheduled-processing functionality.

However, the current scheduled-processing implementation is incomplete.

The repository contains references to scheduled scraping and related configuration, but the complete automated scheduling workflow should not be considered production-ready.

If scheduled scraping is required, verify:

* Cron configuration.
* Scraping function availability.
* Required variables.
* Persistence behavior.
* Error handling.
* Duplicate execution behavior.

---

## 29. Proxy Configuration Problems

The project contains proxy-related configuration.

However, proxy rotation is not implemented in the active scraping flow.

Therefore, setting:

```env
USE_PROXY=true
```

does not by itself provide a complete proxy-rotation system.

A future implementation should provide:

* Proxy validation.
* Proxy selection.
* Proxy rotation.
* Failed-proxy handling.
* Retry behavior.
* Proxy authentication where required.

---

## 30. Administrative Access Problems

Administrative functionality currently lacks a complete production-grade authorization layer.

If administrative routes are deployed publicly, unauthorized users may potentially access administrative functionality.

Before production deployment, implement:

* Authentication middleware.
* Role-based authorization.
* Protected administrative routes.
* Secure sessions or tokens.
* Password hashing.
* Proper access-control checks.

---

## 31. Duplicate Route Issues

The backend contains overlapping route definitions.

This can make maintenance and debugging more difficult.

If an endpoint behaves unexpectedly:

1. Search `server.js` for duplicate route definitions.
2. Determine which handler is active.
3. Remove obsolete handlers.
4. Keep a single authoritative implementation.

---

## 32. Duplicate Frontend Logic

Some frontend functionality is implemented in overlapping handlers.

If a button or form appears to execute multiple times:

1. Inspect the corresponding JavaScript file.
2. Search for multiple event listeners.
3. Remove duplicate handlers.
4. Keep one authoritative event implementation.

---

## 33. Deployment Considerations

The current project is primarily suitable for prototype/local deployment.

A production deployment should additionally provide:

* Persistent database storage.
* Secure authentication.
* Administrative authorization.
* Centralized logging.
* Monitoring.
* Rate limiting.
* Retry handling.
* Resource limits for Puppeteer.
* Browser-process management.
* Secure configuration management.

---

## 34. Production Security Considerations

Before production deployment:

* Do not expose administrative endpoints without authorization.
* Do not rely on plain JSON user storage for sensitive accounts.
* Do not store plaintext passwords.
* Use secure session/token management.
* Validate all incoming URLs.
* Apply rate limits.
* Restrict scraping concurrency.
* Protect environment secrets.
* Validate exported data.
* Add structured logging and monitoring.

---

## 35. Common Development Checklist

Use the following checklist when setting up the project:

```text
[ ] Node.js installed
[ ] npm available
[ ] Repository cloned
[ ] npm install completed
[ ] Puppeteer installed correctly
[ ] Server started
[ ] Main page accessible
[ ] Login page accessible
[ ] Analysis page accessible
[ ] Admin page accessible
[ ] Test product URL submitted
[ ] Product data returned
[ ] Sentiment data generated
[ ] Estimated sales displayed
[ ] Charts rendered
[ ] Server-health endpoint responds
```

---

## 36. Known Implementation Limitations

The current implementation has the following known limitations:

* Selector-dependent scraping.
* No robust retry system.
* No comprehensive rate limiting.
* JSON-based persistence.
* Prototype-level authentication.
* Incomplete administrative authorization.
* Incomplete scheduled scraping.
* Proxy rotation not implemented.
* No production database.
* No automated test suite.
* Some duplicate route definitions.
* Some duplicate frontend logic.
* Placeholder configuration and integrations.

---

## 37. Summary

For normal local development, the basic process is:

```text
Clone Repository
      ↓
npm install
      ↓
npm start
      ↓
Open localhost
      ↓
Submit Product URL
      ↓
Puppeteer Scrapes Page
      ↓
Product Data Extracted
      ↓
Reviews Analyzed
      ↓
Sentiment Generated
      ↓
Sales Estimated
      ↓
Results Stored
      ↓
Analysis Displayed
```

The application is functional as a scraping and analysis prototype, but the current implementation requires additional engineering before it should be treated as a production-ready scraping platform.

```

This is the **second file**. Next in the documentation set is **`scraping-pipeline-guide.md`**.
```
