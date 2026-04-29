# uta-dev

# UTA App Architecture v1 (OCI + Python + Flutter)

## Project Overview

This project defines the initial version of a mobile and web application for the Utah Telugu Association (UTA). The application provides a centralized platform for members to access organizational information, events, and services. The goal is to deliver a cost-effective, maintainable, and scalable solution suitable for a non-profit organization.

## Scope

The first version of the application includes:

* Member authentication and login
* Member profile management
* Event and calendar viewing
* Announcements and organizational information
* QR code generation for members
* Basic ticket purchase capability
* Admin functionality for managing members and content
* Public website with similar informational capabilities

## User Roles

* Member: Can log in, view content, access QR code, and interact with events
* Admin: Can manage members, events, announcements, and content

## Core Features

* Secure login and authentication
* Event and calendar browsing
* QR code generation and display
* Ticket purchasing
* Announcements and updates
* Admin content management

## Non-Functional Requirements

* Performance: Fast response times for mobile and web users
* Availability: System should be accessible at all times with minimal downtime
* Security: Secure authentication and data handling
* Scalability: Ability to support growth in users and usage
* Cost Efficiency: Optimized for minimal operational cost

## Assumptions

* Initial user base of approximately 200 members
* Low to medium traffic usage
* Development and maintenance handled by volunteers or limited resources
* Budget constraints typical of a non-profit organization

## Risks and Considerations

* Dependency on OCI services and configurations
* Complexity in authentication integration
* App store approval timelines and requirements
* Limited development bandwidth due to volunteer model

## Deployment Strategy

* Mobile applications deployed via Apple App Store and Google Play Store
* Backend services deployed on OCI
* Website hosted using OCI Object Storage and CDN

## Overview

This document outlines the Version 1 architecture for the Utah Telugu Association (UTA) mobile app and website. The focus is on:

* Low cost (non-profit friendly)
* Simplicity and maintainability
* Good performance and user experience
* Scalability for future growth

---

## 🏗️ Architecture Overview

### High-Level Components

* **Mobile App**: Flutter (iOS + Android)
* **Website**: Static React / Next.js (hosted on OCI Object Storage)
* **Backend**: FastAPI (Python)
* **Cloud Provider**: Oracle Cloud Infrastructure (OCI)

### Architecture Diagram

```
Flutter Mobile App        Website (React / Static)
        │                          │
        └──────────────┬───────────┘
                       │
                    OCI CDN
                       │
                 API Gateway
                       │
               FastAPI Backend
         (OCI Functions or VM)
                       │
        ┌──────────────┼──────────────┐
        │              │              │
Autonomous DB   Object Storage   Identity Domains
   (Data)           (Files)          (Auth)
```

---

## 🔧 Technology Stack

| Layer      | Technology            | Purpose                           |
| ---------- | --------------------- | --------------------------------- |
| Mobile App | Flutter               | Cross-platform UI (iOS + Android) |
| Backend    | FastAPI (Python)      | API development                   |
| Auth       | OCI Identity Domains  | User authentication               |
| API Layer  | OCI API Gateway       | Secure API routing                |
| Compute    | OCI Functions / VM    | Backend execution                 |
| Database   | OCI Autonomous DB     | Structured data                   |
| Storage    | OCI Object Storage    | Images, PDFs                      |
| CDN        | OCI CDN               | Performance optimization          |
| Website    | React / Next.js       | Web interface                     |
| Payments   | Stripe                | Ticket purchases                  |
| QR Codes   | Python (qrcode + JWT) | Member identification             |

---

## ⚡ Performance Strategy

To ensure fast and intuitive experience:

* Use **CDN** for static content and media
* Implement **client-side caching** in Flutter
* Optimize API responses (minimal payloads)
* Use efficient database queries and indexing
* Avoid unnecessary backend calls

---

## 💰 Cost Breakdown (All-in-One Table)

### Assumptions

* ~200 users
* Low to medium usage
* Non-profit organization

| Category | Component       | Service          | Monthly Cost | Yearly Cost | Notes                |
| -------- | --------------- | ---------------- | ------------ | ----------- | -------------------- |
| OCI      | Authentication  | Identity Domains | $3 – $5      | $36 – $60   | ~$0.016/user         |
| OCI      | API Layer       | API Gateway      | $1 – $2      | $12 – $24   | Low traffic          |
| OCI      | Backend         | Functions / VM   | $0 – $3      | $0 – $36    | Free tier mostly     |
| OCI      | Database        | Autonomous DB    | $0           | $0          | Free tier sufficient |
| OCI      | Storage         | Object Storage   | $0 – $2      | $0 – $24    | Minimal usage        |
| OCI      | CDN             | OCI CDN          | $0 – $2      | $0 – $24    | Performance          |
| OCI      | Monitoring      | Logging          | $0 – $1      | $0 – $12    | Minimal              |
| External | Apple Developer | Apple            | $0           | $0          | Non-profit waiver    |
| External | Google Play     | Google           | ~$2          | $25         | One-time amortized   |
| External | Domain          | Registrar        | ~$1          | $10 – $15   | Optional             |

---

## 🧾 Total Cost Summary

| Type            | Monthly Cost       | Yearly Cost          |
| --------------- | ------------------ | -------------------- |
| OCI Total       | $5 – $15           | $60 – $180           |
| External Total  | $3 – $5            | $35 – $40            |
| **Grand Total** | **$8 – $20/month** | **$100 – $220/year** |

---

---
