# Stray Shield — Troubleshooting Guide

> Common development, configuration, API, database, image-processing, machine-learning, and deployment issues encountered when running Stray Shield.

## 1. Troubleshooting Approach

When an issue occurs, start by identifying which layer of the application is affected.

```text
Browser / Frontend
        │
        ▼
Next.js Application
        │
        ▼
Express API
        │
   ┌────┴─────┐
   ▼          ▼
Database      ML Pipeline
