# Deployment Guide

This document provides instructions for deploying the IBCOL GraphQL Micro API to different environments.

## Deployment Architecture

The API is configured for deployment on Now/Vercel with multiple environment targets:

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

## Environment Configurations

The API uses separate configuration files for each environment:

- `now-local.json`: Local development
- `now-dev.json`: Development environment
- `now-stage.json`: Staging/UAT environment
- `now-production.json`: Production environment

### Configuration Structure

Each configuration file follows this structure:

```json
{
  "version": 2,
  "name": "ibcol-micro-graphql-api-[environment]",
  "alias": [
    "api.[environment].ibcol.org"
  ],
  "regions": ["iad1"],
  "env": {
    "ENV": "[environment]",
    "NODE_ENV": "production",
    "MONGO_URL": "@ibcol-mongo-url-[environment]",
    "APP_URL": "https://[environment].ibcol.org",
    "FILEPOND_API_URL": "https://[environment].ibcol.org",
    "FILEPOND_API_ENDPOINT": "/file/",
    "SENDGRID_API_KEY": "@ibcol-sendgrid-api"
  },
  "builds": [{ "src": "index.js", "use": "@now/node-server" }],
  "routes": [{ "src": "/.*", "dest": "index.js" }]
}
```

## Environment Variables

The following environment variables are required:

| Variable | Description | Example |
|----------|-------------|---------|
| `ENV` | Current environment | `production` |
| `NODE_ENV` | Node environment | `production` |
| `MONGO_URL` | MongoDB connection string | `mongodb+srv://<username>:<password>@<cluster>.mongodb.net/<database>` |
| `APP_URL` | Main application URL | `https://www.ibcol.org` |
| `FILEPOND_API_URL` | File storage API URL | `https://www.ibcol.org` |
| `FILEPOND_API_ENDPOINT` | File storage API endpoint | `/file/` |
| `SENDGRID_API_KEY` | SendGrid API key | `SG.xxxxxxxxxxxx` |
| `MAILCHIMP_API_KEY` | Mailchimp API key (optional) | `xxxxxxxxxxxx` |

## Deployment Commands

The package.json includes scripts for deploying to different environments:

```bash
# Deploy to development
npm run deploy-dev

# Deploy to staging
npm run deploy-stage

# Deploy to production
npm run deploy-production
```

These commands use the Now/Vercel CLI to deploy the API with the appropriate configuration.

## Local Development

To run the API locally:

1. Install dependencies:
   ```bash
   npm install
   ```

2. Create a `.env` file with the required environment variables or use the `now-local.json` configuration.

3. Start the development server:
   ```bash
   npm run local
   ```

This will start the API on port 4000 with hot reloading enabled.

## Deployment Process

1. **Prepare for Deployment**:
   - Ensure all changes are committed to the repository
   - Run tests to verify functionality
   - Update version number in package.json if necessary

2. **Deploy to Staging**:
   ```bash
   npm run deploy-stage
   ```

3. **Verify Staging Deployment**:
   - Test the API functionality in the staging environment
   - Verify integration with other services

4. **Deploy to Production**:
   ```bash
   npm run deploy-production
   ```

5. **Verify Production Deployment**:
   - Monitor logs for any errors
   - Verify API functionality in production

## Vercel Secrets

The deployment uses Vercel secrets for sensitive information:

- `@ibcol-mongo-url-dev`: MongoDB connection string for development
- `@ibcol-mongo-url-stage`: MongoDB connection string for staging
- `@ibcol-mongo-url-production`: MongoDB connection string for production
- `@ibcol-sendgrid-api`: SendGrid API key

To manage these secrets:

```bash
# Add a secret
now secrets add ibcol-mongo-url-production "mongodb+srv://..."

# Remove a secret
now secrets rm ibcol-mongo-url-production
```

## Deployment Regions

The API is configured to deploy to the `iad1` region (US East) by default. This can be modified in the configuration files if needed.

## Monitoring and Logs

After deployment, you can monitor the API and view logs using the Vercel dashboard:

1. Go to the [Vercel Dashboard](https://vercel.com/dashboard)
2. Select the appropriate project
3. Navigate to the "Deployments" tab
4. Click on the deployment to view details and logs

## Rollback Procedure

If issues are encountered after deployment:

1. Identify the last stable deployment in the Vercel dashboard
2. Click on the "..." menu for that deployment
3. Select "Promote to Production"

This will roll back to the selected deployment.
