# UTA App Architecture v1 (OCI + Python + Flutter)

Project: uta-dev

## Project Overview

This document defines the Version 1 architecture for the Utah Telugu Association (UTA) mobile app and website. The application provides a centralized platform for members to access organizational information, events, and services.

The architecture is designed for:

- Low cost suitable for a non-profit organization
- Simplicity and maintainability
- Good performance and user experience
- Secure member authentication and data handling
- Scalability for future growth

## Scope

The first version of the application includes:

- Member authentication and login
- Member profile management
- Event and calendar viewing
- Announcements and organizational information
- QR code generation for members
- Basic ticket purchase capability
- Admin functionality for managing members and content
- Public website with similar informational capabilities

## User Roles

- Member: Can log in, view content, access QR code, and interact with events
- Admin: Can manage members, events, announcements, and content

## Core Features

- Secure login and authentication
- Event and calendar browsing
- QR code generation and display
- Ticket purchasing
- Announcements and updates
- Admin content management

## Non-Functional Requirements

- Performance: Fast response times for mobile and web users
- Availability: System should be accessible at all times with minimal downtime
- Security: Secure authentication and data handling
- Scalability: Ability to support growth in users and usage
- Cost Efficiency: Optimized for minimal operational cost

## Assumptions

- Initial user base of approximately 200 members
- Low to medium traffic usage
- Development and maintenance handled by volunteers or limited resources
- Budget constraints typical of a non-profit organization

## Risks and Considerations

- Dependency on OCI services and configurations
- Complexity in authentication integration
- App store approval timelines and requirements
- Limited development bandwidth due to volunteer model

## Architecture Overview

### High-Level Components

- Mobile App: Flutter for iOS and Android
- Website: Static React or Next.js site hosted on OCI Object Storage
- Backend: FastAPI using Python
- Cloud Provider: Oracle Cloud Infrastructure (OCI)
- Authentication: OCI Identity Domains
- Database: OCI Autonomous Database
- Storage: OCI Object Storage
- CDN: OCI CDN
- Payments: Stripe

### Architecture Diagram

```text
Flutter Mobile App        Website (React / Static)
        |                          |
        +------------+-------------+
                     |
                  OCI CDN
                     |
               API Gateway
                     |
             FastAPI Backend
        (OCI Functions or VM)
                     |
       +-------------+-------------+
       |             |             |
Autonomous DB  Object Storage  Identity Domains
    (Data)        (Files)          (Auth)
```

## Technology Stack

| Layer | Technology | Purpose |
| --- | --- | --- |
| Mobile App | Flutter | Cross-platform UI for iOS and Android |
| Backend | FastAPI (Python) | API development |
| Auth | OCI Identity Domains | User authentication |
| API Layer | OCI API Gateway | Secure API routing |
| Compute | OCI Functions / VM | Backend execution |
| Database | OCI Autonomous DB | Structured data |
| Storage | OCI Object Storage | Images, PDFs, and static website assets |
| CDN | OCI CDN | Performance optimization |
| Website | React / Next.js | Web interface |
| Payments | Stripe | Ticket purchases |
| QR Codes | Python qrcode + JWT | Member identification |

## Deployment Strategy

- Mobile applications are deployed via the Apple App Store and Google Play Store
- Backend services are deployed on OCI using Functions or Compute
- Website assets are built and deployed to OCI Object Storage
- CDN is used for caching static content and improving latency

## Deployment Topology

The system is deployed on Oracle Cloud Infrastructure in a single region for the initial version.

Components are deployed as follows:

- API Gateway: Public entry point for client requests
- Backend: FastAPI deployed on OCI Functions or Compute instances
- Database: OCI Autonomous Database as a regional service
- Object Storage: Static assets, website hosting, images, and PDFs
- CDN: Static content caching and latency improvement
- Identity Domains: Authentication and token issuing

The architecture is designed to be regionally isolated with the ability to expand to multi-region deployment in the future if required.

## Request Flow

A typical request flow is:

1. User interacts with the mobile app or website
2. Request is sent through OCI CDN and API Gateway
3. API Gateway validates and forwards the request
4. FastAPI backend processes the request
5. Backend interacts with the database, storage, payment provider, or identity service as needed
6. Response is returned to the client

For authenticated requests:

- User logs in via OCI Identity Domains
- A JWT token is issued and stored on the client
- The token is included in subsequent API requests
- API Gateway and the backend enforce authorization

## API Overview

The backend exposes RESTful APIs to support mobile and web clients. APIs are secured using JWT tokens issued by OCI Identity Domains.

Key API endpoints include:

- `POST /auth/login`
- `GET /members/me`
- `GET /events`
- `GET /announcements`
- `GET /qr`
- `POST /tickets/purchase`
- `GET /admin/members`
- `POST /admin/events`

APIs are designed to be stateless and return JSON responses. Authentication is required for all member-specific endpoints.

## Data Model Overview

The system uses a relational data model implemented in OCI Autonomous Database.

Core entities include:

### Member

- `id`
- `name`
- `email`
- `role`
- `status`

### Event

- `id`
- `title`
- `description`
- `date`
- `location`

### Announcement

- `id`
- `title`
- `content`
- `created_at`

### Ticket

- `id`
- `member_id`
- `event_id`
- `status`
- `payment_reference`

### QRCode

- `id`
- `member_id`
- `token`
- `expiry`

Relationships:

- A Member can have multiple Tickets
- An Event can have multiple Tickets
- A Member has one QRCode

The data model is designed for simplicity and supports future extension.

## Security Overview

Authentication is managed using OCI Identity Domains.

- Users authenticate via Identity Domains and receive JWT tokens
- Tokens are included in API requests for authorization
- API Gateway validates incoming requests before forwarding to backend services
- Role-based access control is enforced at the application level for Member and Admin roles

Data security considerations:

- All communication is over HTTPS
- Sensitive configuration such as API keys and credentials is managed through environment variables
- Stripe keys are separated by environment for test and live payment flows
- The system avoids storing sensitive user data beyond what is required for functionality

## Performance Strategy

To ensure a fast and intuitive experience:

- Use CDN for static content and media
- Implement client-side caching in Flutter
- Optimize API responses with minimal payloads
- Use efficient database queries and indexing
- Avoid unnecessary backend calls

## CI/CD and Deployment Flow

The project uses GitHub for source code management and GitHub Actions for CI/CD.

High-level flow:

1. Code changes are pushed to the GitHub repository
2. GitHub Actions pipeline is triggered on push to the main branch
3. Pipeline performs dependency installation, code linting, testing, and build steps
4. On successful build, deployment steps are executed

Deployment targets:

- Backend is deployed to OCI Functions or Compute
- Website is built and deployed to OCI Object Storage

OCI CLI or APIs are used within GitHub Actions to perform deployments. Credentials are stored securely as GitHub repository secrets.

### CI/CD Flow Diagram

```text
Developer
   |
   v
GitHub Repository (push)
   |
   v
GitHub Actions Pipeline
   |
   +-- Install and Test
   +-- Build
   |
   v
Deployment Step
   |
   +-- Deploy Backend (OCI)
   +-- Deploy Website (Object Storage)
```

## Environment Strategy

The system follows a simple environment model to support development and production usage.

Environments:

- Development (Dev): Used for local development and testing
- Production (Prod): Used by end users

Configuration differences are handled using environment variables.

Key environment configurations include:

- API base URLs
- Database connection strings
- Authentication configuration
- Stripe API keys for test and live environments

Environment separation ensures that development activities do not impact production data or users.

### Environment Flow Diagram

```text
Developer
   |
   v
 Dev
   |
   v
Tested
   |
   v
 Prod
   |
   v
Users
```

## Cost Breakdown

### Cost Assumptions

- Approximately 200 users
- Low to medium usage
- Non-profit organization
- OCI Free Tier is used where possible

| Category | Component | Service | Monthly Cost | Yearly Cost | Notes |
| --- | --- | --- | --- | --- | --- |
| OCI | Authentication | Identity Domains | $3 - $5 | $36 - $60 | Approximately $0.016/user |
| OCI | API Layer | API Gateway | $1 - $2 | $12 - $24 | Low traffic |
| OCI | Backend | Functions / VM | $0 - $3 | $0 - $36 | Free tier mostly |
| OCI | Database | Autonomous DB | $0 | $0 | Free tier sufficient |
| OCI | Storage | Object Storage | $0 - $2 | $0 - $24 | Minimal usage |
| OCI | CDN | OCI CDN | $0 - $2 | $0 - $24 | Performance |
| OCI | Monitoring | Logging | $0 - $1 | $0 - $12 | Minimal |
| External | Apple Developer | Apple | $0 | $0 | Non-profit waiver |
| External | Google Play | Google | ~$2 | $25 | One-time cost amortized |
| External | Domain | Registrar | ~$1 | $10 - $15 | Optional |

## Total Cost Summary

| Type | Monthly Cost | Yearly Cost |
| --- | --- | --- |
| OCI Total | $5 - $15 | $60 - $180 |
| External Total | $3 - $5 | $35 - $40 |
| Grand Total | $8 - $20/month | $100 - $220/year |
