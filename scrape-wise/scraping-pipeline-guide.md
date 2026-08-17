# Scrape Wise — Web Scraping Pipeline Guide

## 1. Overview

The web scraping pipeline is the core processing workflow of Scrape Wise.

The application accepts product URLs through the frontend, sends them to the Express backend, uses Puppeteer to load the target pages, extracts product information from the page, processes available reviews using Sentiment.js, estimates sales from review counts, stores the resulting information, and returns the processed data to the frontend.

The primary scraping implementation is contained in `server.js`.

The frontend interaction with the scraping functionality is handled primarily through `script.js`.

---

## 2. End-to-End Pipeline

The implemented scraping flow can be represented as:

```text
Product URL
    |
    v
Frontend Input
    |
    v
Fetch Request
    |
    v
Express Scraping Endpoint
    |
    v
URL Validation
    |
    v
Puppeteer Browser
    |
    v
Puppeteer Extra
    |
    v
Stealth Plugin
    |
    v
Browser/Page Configuration
    |
    v
Target Website
    |
    v
DOM/Page Content
    |
    v
Selector-Based Extraction
    |
    +----------------------+
    |                      |
    v                      v
Product Information     Reviews
                           |
                           v
                     Sentiment.js
                           |
                           v
                  Sentiment Classification
                           |
                           v
                    Sentiment Summary
                           |
                           +------------------+
                                              |
                                              v
                                      Sales Estimation
                                              |
                                              v
                                      Result Assembly
                                              |
                                              v
                                      JSON Persistence
                                              |
                                              v
                                      HTTP Response
                                              |
                                              v
                                      Frontend Display
                                              |
                              +---------------+---------------+
                              |               |               |
                              v               v               v
                           Analysis       Comparison        Export
3. Scraping Request

The scraping process begins when the user submits one or more product URLs through the frontend.

The frontend sends the URLs to the Express backend.

The backend then processes the supplied URLs and returns structured results.

The scraping operation is therefore divided into two major layers:

Client Side

Responsible for:

Accepting URLs.
Sending requests.
Receiving results.
Displaying product information.
Displaying analysis.
Exporting data.
Server Side

Responsible for:

Browser automation.
Page navigation.
Product extraction.
Review extraction.
Sentiment processing.
Sales estimation.
Data persistence.
4. URL Handling

The backend receives product URLs from the scraping request.

The URL is used as the target address for Puppeteer navigation.

A valid URL does not guarantee successful extraction because the target website must also contain the elements expected by the scraper.

Possible causes of unsuccessful extraction include:

Invalid URL.
Unreachable website.
Website blocking automated traffic.
Changed HTML structure.
Missing product information.
Dynamically loaded content.
Changed CSS selectors.
5. Puppeteer Browser Automation

Scrape Wise uses Puppeteer for browser automation.

Puppeteer provides a browser context that allows the application to:

Launch a browser.
Create pages.
Navigate to URLs.
Execute browser-side JavaScript.
Inspect page content.
Query DOM elements.
Extract text from web pages.

This is different from a simple HTTP request because the target page is loaded through a browser environment.

6. Puppeteer Extra

The application uses Puppeteer Extra as an extension layer around Puppeteer.

This allows additional plugins to be applied to the browser automation process.

The project uses the Puppeteer Stealth Plugin as part of this configuration.

7. Stealth Plugin

Scrape Wise integrates the Puppeteer Stealth Plugin.

The purpose of this integration is to reduce obvious indicators that the browser is being controlled through automation.

The Stealth Plugin should not be interpreted as a guarantee that the target website will permit automated access.

Websites can still detect, restrict, rate-limit, or block automated requests.

8. Browser Configuration

Before navigating to the target page, the scraper configures the Puppeteer page.

The browser configuration includes behavior such as:

User-agent configuration.
Request interception.
Resource filtering.
Page navigation settings.

These settings are intended to reduce unnecessary browser activity and improve scraping behavior.

9. Request Interception

The scraper can intercept browser requests before resources are loaded.

Unnecessary resources can be prevented from loading.

Typical examples include:

Images
Stylesheets
Fonts

The purpose is to reduce:

Network traffic.
Page-loading overhead.
Browser resource consumption.

This is particularly useful when the scraper is interested primarily in textual product information.

10. Page Navigation

Puppeteer navigates to the submitted product URL.

The scraper waits for the required page-loading condition before attempting extraction.

However, page-load completion does not necessarily mean that all dynamically generated content is available.

Some websites load product information asynchronously.

Therefore, extraction reliability depends on the target website's loading behavior.

11. Selector-Based Extraction

Product information is extracted using CSS selectors.

The scraper searches the loaded page for the selectors associated with expected product fields.

Depending on the target page, the extracted information can include:

Product title.
Price.
Rating.
Reviews.
Review count.
Country/origin information.

The extraction logic is therefore dependent on the target website's HTML structure.

12. Product Title Extraction

The scraper attempts to locate the product title using the selectors defined in the implementation.

The extracted value becomes the product's title in the resulting object.

If the expected element is not available, the result may contain a fallback value or an empty value depending on the extraction path.

13. Price Extraction

The scraper extracts the product price from the expected page element.

The extracted value represents the price visible to the scraper.

The application does not independently validate whether the value represents:

Current price.
Discounted price.
Original price.
Subscription price.
Another price shown on the page.

This depends on the selector and target page structure.

14. Rating Extraction

The scraper attempts to retrieve the product's rating from the target page.

The rating is dependent on the availability of the expected rating element.

If the target website changes the location or structure of its rating information, extraction may fail.

15. Review Extraction

Reviews are collected when the target page exposes review elements that match the scraper's extraction logic.

The review content is then passed into the sentiment-analysis stage.

The review pipeline is:

Review Element
      |
      v
Extract Review Text
      |
      v
Sentiment.js
      |
      v
Sentiment Score
      |
      v
Classification
16. Sentiment Analysis

Scrape Wise uses the sentiment npm package, which provides Sentiment.js functionality.

The application analyzes extracted review text and obtains a sentiment score.

The score is then used to classify the review.

The classification logic follows:

Score > 0
    |
    +--> Positive

Score = 0
    |
    +--> Neutral

Score < 0
    |
    +--> Negative
17. Sentiment Summary

Individual review classifications are aggregated into a summary.

The result can be represented as:

{
  "sentimentSummary": {
    "positive": 10,
    "neutral": 5,
    "negative": 2
  }
}

The values represent the number of reviews falling into each sentiment category.

18. Sentiment Processing Characteristics

The sentiment system is lightweight and lexicon-based.

It does not represent a custom-trained machine-learning model.

This is important when describing the project:

Scrape Wise performs automated sentiment analysis using Sentiment.js; it does not implement a custom-trained sentiment classification model.

19. Sentiment Limitations

Sentiment analysis may be affected by:

Sarcasm.
Irony.
Mixed opinions.
Slang.
Product-specific terminology.
Negation.
Poorly written reviews.
Languages not adequately represented by the sentiment lexicon.

For example, a review containing both positive and negative statements may not always be classified according to its intended meaning.

20. Sales Estimation

The application calculates an estimated sales value from the number of reviews.

The implemented calculation uses a review-to-sales assumption.

Conceptually:

Review Count
      |
      v
Review-to-Sales Assumption
      |
      v
Estimated Sales

The estimate should not be interpreted as verified marketplace sales.

It is derived from the application's calculation rather than directly retrieved from the target website.

21. Why Sales Are Estimated

Most product websites do not expose the actual number of units sold to an external scraper.

Scrape Wise therefore uses review information as an indirect indicator.

This is a heuristic rather than a factual sales measurement.

The resulting field should therefore be presented as:

Estimated Sales

rather than:

Actual Sales
22. Result Assembly

After product extraction and review processing, the backend assembles the resulting product object.

The result can contain fields associated with:

Product Information
    |
    +-- Title
    +-- Price
    +-- Rating
    +-- Reviews
    +-- Review Count
    +-- Country/Origin
    |
    +-- Sentiment Summary
    |
    +-- Estimated Sales

The exact fields depend on the available page information and extraction results.

23. Multiple URL Processing

The frontend can submit multiple URLs for scraping.

The backend supports processing multiple supplied URLs.

The implementation uses asynchronous JavaScript processing, including concurrent promise handling.

Conceptually:

URL 1 ──────> Scraper ──────> Result 1
URL 2 ──────> Scraper ──────> Result 2
URL 3 ──────> Scraper ──────> Result 3
                         |
                         v
                  Combined Results

Concurrent scraping can reduce total processing time compared with processing every URL strictly sequentially.

However, high concurrency increases browser and system resource consumption.

24. Data Persistence

After scraping and processing, results are persisted using JSON-based storage.

The project does not use a relational database for the main scraping data.

The JSON approach makes the project easy to run locally but introduces limitations.

Advantages
Simple implementation.
No database server required.
Easy local development.
Human-readable data.
Easy backup.
Limitations
Poor scalability.
Limited concurrency safety.
No database transactions.
Limited querying capabilities.
Difficult multi-user synchronization.
Not suitable for large production datasets.
25. Frontend Result Handling

After the backend completes scraping, the frontend receives the result through the HTTP response.

The frontend can then:

Display product information.
Display review information.
Display sentiment results.
Display estimated sales.
Generate visualizations.
Compare products.
Export results.
26. Analysis Flow

The analysis page uses processed data to provide a more visual representation of the results.

The flow is:

Scraping Result
      |
      v
Frontend JavaScript
      |
      v
Analysis Data
      |
      +----------------------+
      |                      |
      v                      v
Sentiment Visualization   Sales Visualization

Chart.js is used for browser-side visualization.

27. Product Comparison

The application contains product-comparison functionality.

Comparison allows collected product information to be considered together.

The comparison process operates on the structured product results generated by the scraping pipeline.

Conceptually:

Product A
    |
    +----------------+
                     |
                     v
                 Comparison
                     ^
                     |
    +----------------+
    |
Product B

The comparison layer can use information such as:

Price.
Rating.
Review count.
Sentiment.
Estimated sales.
28. JSON Export

The frontend supports JSON export.

The export process is client-side.

Scraped Data
    |
    v
JavaScript
    |
    v
JSON Serialization
    |
    v
Browser Download

The backend does not need to provide a dedicated file-generation service for this operation.

29. CSV Export

The frontend also supports CSV export.

The data is converted into CSV-compatible rows in the browser and then downloaded.

Conceptually:

Product Data
    |
    v
Frontend Conversion
    |
    v
CSV Text
    |
    v
Browser Download
30. Error Handling During Extraction

Scraping depends on external websites, so individual extraction operations can fail.

Examples include:

Missing selector.
Empty value.
Navigation failure.
Browser failure.
Page timeout.
Unexpected HTML.
Website blocking automation.

The scraper includes fallback behavior for some missing values.

However, it does not provide a complete production-grade retry and recovery system.

31. Dynamic Website Limitations

Modern e-commerce websites frequently use:

Client-side rendering.
AJAX requests.
Lazy loading.
Infinite scrolling.
Delayed API responses.
Dynamic selectors.

A selector-based scraper may therefore work correctly one day and fail after a website redesign.

Maintaining a scraper requires periodic validation against the target websites.

32. Rate Limiting

The current implementation should not be described as providing comprehensive production-grade rate limiting.

A production scraper should control:

Requests per minute.
Concurrent browser pages.
Concurrent URLs.
Retry frequency.
Per-domain request volume.

Without these controls, large scraping jobs can consume substantial resources or trigger anti-bot mechanisms.

33. Retry and Backoff

The current scraper does not provide a comprehensive retry strategy with exponential backoff.

A future implementation could use:

Attempt 1
   |
   +--> Failure
          |
          v
      Wait 1 second
          |
          v
Attempt 2
   |
   +--> Failure
          |
          v
      Wait 2 seconds
          |
          v
Attempt 3

This would make the scraper more resilient to temporary failures.

34. Proxy Handling

The project contains proxy-related configuration and references.

However, proxy rotation is not implemented as a complete active feature in the scraper.

Therefore, the current implementation should not be documented as providing:

Round-robin proxy rotation.
Automatic proxy replacement.
Proxy health management.
Automatic failed-proxy removal.

These would require additional implementation.

35. Scheduled Scraping

The project includes node-cron and scheduled-scraping-related code.

The intended architecture is:

Cron Schedule
      |
      v
Scheduled Job
      |
      v
Scraping Function
      |
      v
Product URLs
      |
      v
Scraping Pipeline
      |
      v
Persist Results

However, the current scheduled-processing implementation is incomplete.

Some scheduled functionality references components or variables that are not consistently available in the current implementation.

Therefore, scheduled scraping should be considered a partially implemented feature.

36. Scheduled Scraping — Production Requirements

For reliable scheduled scraping, the following would need to be implemented:

Valid cron configuration.
Persistent URL/job storage.
Job status tracking.
Retry handling.
Failure logging.
Duplicate-job prevention.
Concurrency limits.
Persistent results.
Monitoring.
Notifications.
37. Scraping Logs

The project contains scraping-log functionality.

Logs can be useful for tracking:

Scraping attempts.
Successful operations.
Failed operations.
Processing times.
Target URLs.

However, the current implementation contains overlapping scraping-log functionality, so the logging architecture should be cleaned up before being considered a production logging system.

38. Resource Consumption

Puppeteer is significantly more resource-intensive than a simple HTTP client.

Each browser/page operation can consume:

CPU.
Memory.
Network bandwidth.
Browser processes.

Concurrent scraping therefore needs resource controls.

For larger workloads, a worker/queue architecture would be more appropriate.

39. Recommended Scaling Architecture

A future scalable implementation could use:

Frontend
    |
    v
Express API
    |
    v
Job Queue
    |
    +------------------+
    |                  |
    v                  v
Scraper Worker 1   Scraper Worker 2
    |                  |
    +--------+---------+
             |
             v
       Database / Storage
             |
             v
         Analysis API
             |
             v
          Frontend

This would prevent large scraping jobs from blocking the main Express process.

40. Security Considerations

Accepting arbitrary URLs creates security considerations.

A production implementation should validate:

URL format.
Allowed protocols.
Internal/private IP ranges.
Redirect destinations.
Resource limits.

This is especially important because the server is responsible for opening user-supplied URLs.

41. Scraping Reliability

The reliability of Scrape Wise depends on several external factors:

Scrape Reliability
       |
       +-- Target Website Availability
       |
       +-- HTML Structure
       |
       +-- CSS Selectors
       |
       +-- Dynamic Content
       |
       +-- Anti-Bot Controls
       |
       +-- Browser Compatibility
       |
       +-- Network Conditions

Therefore, scraping success cannot be guaranteed for arbitrary websites.

42. Current Pipeline Status
Pipeline Component	Status
URL submission	Implemented
Express scraping endpoint	Implemented
Puppeteer	Implemented
Puppeteer Extra	Implemented
Stealth plugin	Implemented
User-agent configuration	Implemented
Request interception	Implemented
Selector-based extraction	Implemented
Review extraction	Implemented
Sentiment analysis	Implemented
Sales estimation	Implemented
JSON persistence	Implemented
Frontend visualization	Implemented
Product comparison	Implemented
JSON export	Implemented
CSV export	Implemented
Proxy rotation	Not implemented
Production rate limiting	Not implemented
Comprehensive retry/backoff	Not implemented
Scheduled scraping	Partially implemented
Database persistence	Not implemented
Production job queue	Not implemented
43. Technical Strengths

The scraping implementation demonstrates:

Browser automation.
Express backend development.
Asynchronous JavaScript.
DOM extraction.
Selector-based scraping.
Review processing.
Sentiment analysis.
Data transformation.
JSON persistence.
Frontend/backend communication.
Browser-side visualization.
Data export.
44. Technical Limitations

The main limitations are:

Scraping depends on target-site selectors.
Website changes can break extraction.
Dynamic content can produce incomplete results.
Anti-bot systems may block requests.
No comprehensive retry strategy.
No production-grade rate limiting.
JSON storage limits scalability.
Sales figures are heuristic estimates.
Sentiment classification has language and context limitations.
Proxy rotation is not implemented.
Scheduled scraping is incomplete.
Puppeteer concurrency can consume significant resources.
No production queue architecture exists.
No comprehensive automated scraping test suite exists.
45. Recommended Improvements

Future versions should consider:

Scraping
Site-specific scraper modules.
Centralized selector configuration.
Automatic selector validation.
Better dynamic-content handling.
Retry with exponential backoff.
Domain-specific rate limits.
Processing
Improved sentiment models.
Configurable sentiment thresholds.
More reliable sales estimation.
Additional product metrics.
Infrastructure
Database-backed persistence.
Background workers.
Job queues.
Persistent scheduling.
Centralized logging.
Monitoring.
Security
URL validation.
SSRF protection.
Authentication.
Authorization.
Secure credential storage.
Rate limiting.
46. Summary

The core Scrape Wise pipeline is:

Product URL
    |
    v
Puppeteer
    |
    v
Page Extraction
    |
    v
Product Data
    |
    +------------------+
    |                  |
    v                  v
Reviews            Product Fields
    |
    v
Sentiment.js
    |
    v
Sentiment Summary
    |
    v
Sales Estimation
    |
    v
Structured Result
    |
    v
JSON Persistence
    |
    v
Frontend
    |
    +----------+----------+
    |          |          |
    v          v          v
Analysis   Comparison   Export

This pipeline represents the central implemented functionality of Scrape Wise.

The project is technically useful as a prototype because it combines browser automation, backend processing, sentiment analysis, persistence, visualization, comparison, and export in a single application.

However, features such as proxy rotation, production-grade scheduling, database persistence, comprehensive retry handling, and production security require further implementation before the system can be considered a scalable production scraping platform.
