# Authentication

This document describes the authentication and authorization mechanisms used in the IBCOL GraphQL Micro API.

## Authentication System

The API uses a token-based authentication system built on the `@accounts` packages:

- `@accounts/server`: Core authentication server
- `@accounts/password`: Password-based authentication
- `@accounts/mongo`: MongoDB integration for account storage
- `@accounts/graphql-api`: GraphQL integration for authentication

## Authentication Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │     │             │
│  Client     │────►│  Request    │────►│  Generate   │────►│  Store      │
│  Login      │     │  Token      │     │  Token      │     │  Token      │
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

1. User requests authentication
2. System generates a token and seed phrase
3. Email with verification link/code is sent to the user
4. User verifies their email by clicking the link or entering the code
5. Token is activated and can be used for API requests

## Token Structure

The access token system uses the following structure:

```javascript
{
  email: String,      // User's email address
  token: String,      // Authentication token
  seed: String,       // Seed phrase for verification
  activatedAt: Date,  // When the token was activated
  lastAccessedAt: Date // Last time the token was used
}
```

## Making Authenticated Requests

To make authenticated requests to the API, include the token information in the request headers:

```javascript
{
  "email": "user@example.com",
  "token": "your-access-token"
}
```

In GraphQL operations, pass the token as a parameter:

```graphql
query GetApplications($accessToken: TokenInput!) {
  getApplications(accessToken: $accessToken) {
    # fields
  }
}
```

With variables:
```json
{
  "accessToken": {
    "email": "user@example.com",
    "token": "your-access-token"
  }
}
```

## Token Validation

The API validates tokens using the `isTokenValid` helper function:

```javascript
const isTokenValid = async ({email = "", token = ""}, requireAdminAccess = false) => {
  let current = Date.now();
  
  const accessToken = await AccessToken.findOneAndUpdate({
    email: email.toLowerCase(), token,
    deletedAt: {
      $exists: false
    },
    activatedAt: { 
      $exists : true,
      $ne: null
    }
  }, {
    $set: {
      lastAccessedAt: current
    }
  }, {new: true});

  const adminEmail = await AdminEmail.findOne({
    email: email.toLowerCase(),
    deletedAt: {
      $exists: false
    }
  });
    
  return !requireAdminAccess ? !_.isEmpty(accessToken) : !_.isEmpty(accessToken) && !_.isEmpty(adminEmail);
}
```

This function:
1. Checks if the token exists and is activated
2. Updates the `lastAccessedAt` timestamp
3. Optionally checks for admin privileges
4. Returns a boolean indicating if the token is valid

## Administrative Access

Administrative access is controlled through the `AdminEmail` collection. Users with emails in this collection have administrative privileges when authenticated.

To check for admin access, the `isTokenValid` function is called with `requireAdminAccess` set to `true`:

```javascript
if (!await isTokenValid(accessToken, true)) {
  throw('Unauthorized: Admin access required');
}
```

## Security Considerations

1. **Token Storage**: Tokens are stored in the MongoDB database with activation status
2. **Email Verification**: Tokens require email verification before activation
3. **Token Expiry**: Tokens can be invalidated by setting the `deletedAt` timestamp
4. **Admin Separation**: Administrative access is controlled through a separate collection
5. **Secure Communication**: All API communication should be over HTTPS

## Email Verification

The system uses SendGrid to send verification emails with:
- A verification link
- A verification code
- A seed phrase for additional security

The email template is defined in `emails/login-verification.mjml` and rendered using MJML for responsive email design.

