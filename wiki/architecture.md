# System Architecture

This document outlines the architecture of the IBCOL GraphQL Micro API and its place within the broader IBCOL ecosystem.

## System Context

The IBCOL GraphQL API is a microservice that provides backend functionality for the IBCOL application system. It interacts with several other components and services:

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                        IBCOL Ecosystem                              │
│                                                                     │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      │
│  │              │      │              │      │              │      │
│  │  IBCOL Web   │◄────►│  GraphQL API │◄────►│   MongoDB    │      │
│  │  Frontend    │      │  (This Repo) │      │   Database   │      │
│  │              │      │              │      │              │      │
│  └──────────────┘      └──────┬───────┘      └──────────────┘      │
│                               │                                     │
│                               │                                     │
│                               ▼                                     │
│                        ┌──────────────┐                            │
│                        │  External    │                            │
│                        │  Services    │                            │
│                        │              │                            │
│                        └──────────────┘                            │
│                               │                                     │
│                               │                                     │
│                               ▼                                     │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      │
│  │              │      │              │      │              │      │
│  │   SendGrid   │      │  Mailchimp   │      │ File Storage │      │
│  │   (Email)    │      │ (Newsletter) │      │   Service    │      │
│  │              │      │              │      │              │      │
│  └──────────────┘      └──────────────┘      └──────────────┘      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Component Architecture

The internal architecture of the GraphQL API follows a modular structure:

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                     GraphQL API Architecture                        │
│                                                                     │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      │
│  │              │      │              │      │              │      │
│  │  Apollo      │◄────►│  GraphQL     │◄────►│  Mongoose    │      │
│  │  Server      │      │  Schema      │      │  Models      │      │
│  │              │      │              │      │              │      │
│  └──────────────┘      └──────┬───────┘      └──────┬───────┘      │
│                               │                      │              │
│                               │                      │              │
│                               ▼                      ▼              │
│                        ┌──────────────┐      ┌──────────────┐      │
│                        │              │      │              │      │
│                        │  Resolvers   │◄────►│  Database    │      │
│                        │              │      │  Access      │      │
│                        └──────┬───────┘      └──────────────┘      │
│                               │                                     │
│                               │                                     │
│                               ▼                                     │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      │
│  │              │      │              │      │              │      │
│  │  Auth        │◄────►│  Business    │◄────►│  External    │      │
│  │  Middleware  │      │  Logic       │      │  Integrations│      │
│  │              │      │              │      │              │      │
│  └──────────────┘      └──────────────┘      └──────────────┘      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Data Flow

The typical data flow through the system:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │     │             │
│  Client     │────►│  Apollo     │────►│  GraphQL    │────►│  Mongoose   │
│  Request    │     │  Server     │     │  Resolvers  │     │  Models     │
│             │     │             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
                                               │                   │
                                               │                   │
                                               ▼                   ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │     │             │
│  Client     │◄────│  Response   │◄────│  Business   │◄────│  MongoDB    │
│  Response   │     │  Formatting │     │  Logic      │     │  Database   │
│             │     │             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
```

## Authentication Flow

The authentication process:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │     │             │
│  Client     │────►│  Login      │────►│  Generate   │────►│  Store      │
│  Request    │     │  Request    │     │  Token      │     │  Token      │
│             │     │             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
      ▲                                                            │
      │                                                            │
      │                                                            │
      │            ┌─────────────┐     ┌─────────────┐            │
      │            │             │     │             │            │
      └────────────│  Send       │◄────│  Email      │◄───────────┘
                   │  Response   │     │  Verification│
                   │             │     │             │
                   └─────────────┘     └─────────────┘
```

## Deployment Architecture

The API is deployed using Now/Vercel with multiple environment configurations:

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                     Deployment Architecture                         │
│                                                                     │
│  ┌──────────────┐      ┌──────────────┐      ┌──────────────┐      │
│  │              │      │              │      │              │      │
│  │  Local       │      │  Development │      │  Staging     │      │
│  │  Environment │      │  Environment │      │  Environment │      │
│  │              │      │              │      │              │      │
│  └──────────────┘      └──────────────┘      └──────────────┘      │
│                                                                     │
│                        ┌──────────────┐                            │
│                        │              │                            │
│                        │  Production  │                            │
│                        │  Environment │                            │
│                        │              │                            │
│                        └──────────────┘                            │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────┐      │
│  │                                                          │      │
│  │                    Now/Vercel Platform                   │      │
│  │                                                          │      │
│  └──────────────────────────────────────────────────────────┘      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Technology Stack

- **Node.js**: JavaScript runtime environment
- **Apollo Server**: GraphQL server implementation
- **Mongoose**: MongoDB object modeling tool
- **@accounts/server**: Authentication framework
- **SendGrid**: Email delivery service
- **Mailchimp**: Newsletter management
- **Now/Vercel**: Deployment platform

## Key Components

1. **GraphQL Schema**: Defines the API's type system and operations
2. **Resolvers**: Implement the logic for each GraphQL operation
3. **Database Models**: Mongoose schemas for MongoDB data storage
4. **Authentication**: Token-based authentication system
5. **External Integrations**: Connections to SendGrid, Mailchimp, and file storage
6. **Deployment Configuration**: Environment-specific settings for different deployment targets

