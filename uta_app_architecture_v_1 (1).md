# UTA App Architecture v1 (OCI + Python + Flutter)

## Project Overview
This project defines the initial version of a mobile and web application for the Utah Telugu Association (UTA). The application provides a centralized platform for members to access organizational information, events, and services. The goal is to deliver a cost-effective, maintainable, and scalable solution suitable for a non-profit organization.

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

## Deployment Strategy
- Mobile applications deployed via Apple App Store and Google Play Store
- Backend services deployed on OCI
- Website hosted using OCI Object Storage and CDN

## API Overview
The backend exposes RESTful APIs to support mobile and web clients. APIs are secured using JWT tokens issued by OCI Identity Domains.

Key API endpoints include:
- POST /auth/login
- GET /members/me
- GET /events
- GET /announcements
- GET /qr
- POST /tickets/purchase
- GET /admin/members
- POST /admin/events

APIs are designed to be stateless and return JSON responses. Authentication is required for all member-specific endpoints.

## Data Model Overview
The system uses a relational data model implemented in OCI Autonomous Database.

Core entities include:

- Member
  - id
  - name
  - email
  - role
  - status

- Event
  - id
  - title
  - description
  - date
  - location

- Announcement
  - id
  - title
  - content
  - created_at

- Ticket
  - id
  - member_id
  - event_id
  - status
  - payment_reference

- QRCode
  - id
  - member_id
  - token
  - expiry

Relationships:
- A Member can have multiple Tickets
- An Event can have multiple Tickets
- A Member has one QRCode

The data model is designed for simplicity and supports future extension.

## CI/CD and Deployment Flow

The project uses GitHub for source code management and GitHub Actions for CI/CD.

High-level flow:

- Code changes are pushed to GitHub repository
- GitHub Actions pipeline is triggered on push to main branch
- Pipeline performs:
  - Dependency installation
  - Code linting and testing
  - Build steps (backend / website)
- On successful build, deployment steps are executed

Deployment targets:
- Backend (FastAPI) is deployed to OCI (Functions or Compute)
- Website is built and deployed to OCI Object Storage

OCI CLI or APIs are used within GitHub Actions to perform deployments. Credentials are stored securely as GitHub repository secrets.

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
- Stripe API keys (test vs live)

Environment separation ensures that development activities do not impact production data or users.



## Deployment Topology

The system is deployed on Oracle Cloud Infrastructure (OCI) in a single region for the initial version.

Components are deployed as follows:

- API Gateway: Public entry point for all client requests
- Backend (FastAPI): Deployed on OCI Functions or Compute instances
- Database: OCI Autonomous Database (regional service)
- Object Storage: Used for static assets and website hosting
- CDN: Used for caching static content and improving latency

The architecture is designed to be regionally isolated with the ability to expand to multi-region deployment in the future if required.

## Security Overview

Authentication is managed using OCI Identity Domains.

- Users authenticate via Identity Domains and receive JWT tokens
- Tokens are included in API requests for authorization
- API Gateway validates incoming requests before forwarding to backend services

Data security considerations:

- All communication is over HTTPS
- Sensitive configuration (API keys, credentials) is managed via environment variables
- Role-based access control is enforced at the application level (Member, Admin)

The system avoids storing sensitive user data beyond what is required for functionality.

## Request Flow

A typical request flow is as follows:

1. User interacts with mobile app or website
2. Request is sent to API Gateway
3. API Gateway validates and forwards the request
4. Backend (FastAPI) processes the request
5. Backend interacts with database or storage as needed
6. Response is returned to the client

For authenticated requests:

- User logs in via Identity Domains
- JWT token is issued and stored on client
- Token is included in subsequent API requests

## CI/CD and Deployment Flow


The project uses GitHub for source code management and GitHub Actions for CI/CD.

High-level flow:

- Code changes are pushed to GitHub repository
- GitHub Actions pipeline is triggered on push to main branch
- Pipeline performs:
  - Dependency installation
  - Code linting and testing
  - Build steps (backend / website)
- On successful build, deployment steps are executed

Deployment targets:

- Backend (FastAPI) is deployed to OCI (Functions or Compute)
- Website is built and deployed to OCI Object Storage

OCI CLI or APIs are used within GitHub Actions to perform deployments. Credentials are stored securely as GitHub repository secrets.

### CI/CD Flow Diagram

```
Developer
   │
   ▼
GitHub Repository (push)
   │
   ▼
GitHub Actions Pipeline
   │
   ├── Install & Test
   ├── Build
   ▼
Deployment Step
   │
   ├── Deploy Backend (OCI)
   └── Deploy Website (Object Storage)
```

## Environment Strategy

The system follows a simple environment model:

- Development (Dev): Used for local development and testing
- Production (Prod): Used by end users

Configuration differences are handled using environment variables.

Key configurations include:

- API base URLs
- Database connection strings
- Authentication configuration
- Stripe API keys (test vs live)

Environment separation ensures that development activities do not impact production systems.

### Environment Flow Diagram

```
        Developer
            │
            ▼
          Dev
            │
            ▼
        (Tested)
            │
            ▼
          Prod
            │
            ▼
         Users
```




## Flow Diagrams

### Architecture Diagram (Original)

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

### CI/CD Flow Diagram

```
Developer
   │
   ▼
GitHub Repository (push)
   │
   ▼
GitHub Actions Pipeline
   │
   ├── Install & Test
   ├── Build
   ▼
Deployment Step
   │
   ├── Deploy Backend (OCI)
   └── Deploy Website (Object Storage)
```

### Environment Flow Diagram

```
        Developer
            │
            ▼
          Dev
            │
            ▼
        (Tested)
            │
            ▼
          Prod
            │
            ▼
         Users
```

