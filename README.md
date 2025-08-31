# IBCOL GraphQL Micro API

A GraphQL API microservice for the International Blockchain Olympiad (IBCOL) application system. This service handles team registrations, student and advisor management, project submissions, and administrative functions.

## 🚀 Features

- **Team Applications**: Register and manage team applications for the blockchain olympiad
- **Student Management**: Track student information, education records, and verification
- **Advisor Management**: Manage team advisors and their organizational affiliations
- **Project Submissions**: Handle project details, whitepapers, and presentation uploads
- **Authentication**: Secure access control with token-based authentication
- **Admin Functions**: Application review and management for administrators
- **Email Integration**: Verification emails and notifications via SendGrid
- **Newsletter Management**: Mailchimp integration for subscription management

## 🛠️ Tech Stack

- **Node.js**: JavaScript runtime
- **Apollo Server**: GraphQL implementation
- **MongoDB**: Database with Mongoose ODM
- **@accounts/server**: Authentication framework
- **SendGrid**: Email delivery service
- **Mailchimp**: Newsletter management
- **Now/Vercel**: Deployment platform

## 📋 API Structure

The API is organized around the following main entities:

- **Application**: Team registrations with associated students, advisors, and projects
- **AccessToken**: Authentication tokens for secure access
- **AdminEmail**: Administrative access control
- **Various GraphQL scalar types**: Date, JSON, etc.

## 🔧 Development Setup

### Prerequisites

- Node.js (v10+)
- MongoDB instance
- SendGrid API key
- Mailchimp API key (optional)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/ibcol/ibcol-micro-graphql-api.git
   cd ibcol-micro-graphql-api
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Configure environment variables:
   - Create a `.env` file based on the environment configurations in the `now-*.json` files
   - Set up MongoDB connection string, SendGrid API key, and other required variables

4. Start the development server:
   ```bash
   npm run local
   ```

## 🚢 Deployment

The service is configured for deployment on Now/Vercel with multiple environment targets:

- **Local**: `npm run local` - Development with hot reloading
- **Dev**: `npm run dev` - Development environment
- **Stage**: `npm run stage` - Staging/UAT environment
- **Production**: `npm run production` - Production environment

### Deployment Commands

- Deploy to development: `npm run deploy-dev`
- Deploy to staging: `npm run deploy-stage`
- Deploy to production: `npm run deploy-production`

## 📚 Documentation

For more detailed documentation, please refer to the [Wiki](./wiki/README.md) which includes:

- [System Architecture](./wiki/architecture.md)
- [API Reference](./wiki/api-reference.md)
- [Data Models](./wiki/data-models.md)
- [Authentication](./wiki/authentication.md)
- [Deployment Guide](./wiki/deployment.md)

## 🔗 Related Projects

- [IBCOL Main Website](https://github.com/ibcol/ibcol) - Main IBCOL website and frontend
- [IBCOL To WWW](https://github.com/altx-labs/ibcol-towww) - Website deployment utilities

## 📄 License

This project is proprietary and not licensed for public use.

## 👥 Contributors

- [William Li](http://williamli.io) - [williamli@bbi.io](mailto:williamli@bbi.io)
- [Breaking Bad Interactive](http://bbi.io) - [hello@bbi.io](mailto:hello@bbi.io)

