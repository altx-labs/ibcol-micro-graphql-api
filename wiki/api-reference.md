# API Reference

This document provides a comprehensive reference for the GraphQL API operations available in the IBCOL GraphQL Micro API.

## GraphQL Schema

The API is built around the following main types:

- `Application`: Team applications with associated students, advisors, and projects
- `AccessToken`: Authentication tokens
- `AdminEmail`: Administrative access control
- Custom scalar types: `Date`, `JSON`

## Authentication

Most operations require authentication using an access token. The token should be included in the request headers:

```
{
  "email": "user@example.com",
  "token": "your-access-token"
}
```

## Queries

### getApplications

Retrieves applications associated with the authenticated user's email.

```graphql
query GetApplications($accessToken: TokenInput!) {
  getApplications(accessToken: $accessToken) {
    id
    ref
    teamName
    studentRecords {
      firstName
      lastName
      email
    }
    projectRecords {
      name
      projectCategoryKey
    }
    meta {
      createdAt
      updatedAt
    }
  }
}
```

Variables:
```json
{
  "accessToken": {
    "email": "user@example.com",
    "token": "your-access-token"
  }
}
```

### getApplicationsAsAdmin

Retrieves all applications (admin access required).

```graphql
query GetApplicationsAsAdmin {
  getApplicationsAsAdmin {
    id
    ref
    teamName
    studentRecords {
      firstName
      lastName
      email
    }
    projectRecords {
      name
      projectCategoryKey
    }
    meta {
      createdAt
      updatedAt
      approvedAt
      rejectedAt
    }
  }
}
```

## Mutations

### addApplication

Creates a new team application.

```graphql
mutation AddApplication($application: ApplicationInput!) {
  addApplication(application: $application) {
    id
    ref
    teamName
  }
}
```

Variables:
```json
{
  "application": {
    "teamName": "Blockchain Innovators",
    "studentRecords": [
      {
        "firstName": "John",
        "lastName": "Doe",
        "email": "john@example.com",
        "phoneNumber": "+1234567890",
        "educationRecords": [
          {
            "institutionName": "University of Blockchain",
            "state": "California",
            "city": "San Francisco",
            "countryCode": "US",
            "degree": "Bachelor",
            "programme": "Computer Science",
            "yearOfGraduation": "2023"
          }
        ]
      }
    ],
    "projectRecords": [
      {
        "name": "Decentralized Identity Solution",
        "projectCategoryKey": "IDENTITY",
        "description": "A blockchain-based identity verification system"
      }
    ]
  }
}
```

### updateApplication

Updates an existing application.

```graphql
mutation UpdateApplication($accessToken: TokenInput!, $application: ApplicationUpdateInput!) {
  updateApplication(accessToken: $accessToken, application: $application) {
    id
    ref
    teamName
  }
}
```

Variables:
```json
{
  "accessToken": {
    "email": "user@example.com",
    "token": "your-access-token"
  },
  "application": {
    "ref": "ABC-1234567890",
    "teamName": "Updated Team Name",
    "studentRecords": [...],
    "projectRecords": [...]
  }
}
```

### deleteApplicationById

Deletes an application by ID (admin access required).

```graphql
mutation DeleteApplication($id: ID!) {
  deleteApplicationById(id: $id) {
    id
    ref
  }
}
```

Variables:
```json
{
  "id": "application-id"
}
```

## Input Types

### TokenInput

```graphql
input TokenInput {
  email: String!
  token: String!
}
```

### ApplicationInput

```graphql
input ApplicationInput {
  teamName: String!
  studentRecords: [StudentRecordInput!]!
  advisorRecords: [AdvisorRecordInput]
  projectRecords: [ProjectRecordInput!]!
}
```

### ApplicationUpdateInput

```graphql
input ApplicationUpdateInput {
  ref: String!
  teamName: String!
  studentRecords: [StudentRecordInput!]!
  advisorRecords: [AdvisorRecordInput]
  projectRecords: [ProjectRecordInput!]!
}
```

### StudentRecordInput

```graphql
input StudentRecordInput {
  firstName: String!
  lastName: String!
  phoneNumber: String
  email: String!
  educationRecords: [EducationRecordInput!]!
}
```

### EducationRecordInput

```graphql
input EducationRecordInput {
  institutionName: String!
  state: String!
  city: String!
  countryCode: String!
  degree: String!
  programme: String!
  yearOfGraduation: String!
  studentNumber: String
  studentCardFrontFileId: String
  studentCardBackFileId: String
  transcriptFileId: String
}
```

### AdvisorRecordInput

```graphql
input AdvisorRecordInput {
  firstName: String!
  lastName: String!
  phoneNumber: String
  email: String!
  associationRecords: [AssociationRecordInput!]!
}
```

### AssociationRecordInput

```graphql
input AssociationRecordInput {
  organisationName: String!
  title: String!
  sectorCode: String!
  state: String!
  city: String!
  countryCode: String!
  yearCommencement: String!
  yearCessation: String
}
```

### ProjectRecordInput

```graphql
input ProjectRecordInput {
  ref: String
  name: String!
  projectCategoryKey: String!
  description: String!
  whitepaperFileId: String
  presentationFileId: String
}
```

## Response Types

### Application

```graphql
type Application {
  id: ID!
  ref: String!
  teamName: String!
  studentRecords: [StudentRecord!]!
  advisorRecords: [AdvisorRecord]
  projectRecords: [ProjectRecord!]!
  verificationKeys: [VerificationKey]!
  meta: ApplicationMeta
}
```

### StudentRecord

```graphql
type StudentRecord {
  firstName: String!
  lastName: String!
  phoneNumber: String
  email: String!
  educationRecords: [EducationRecord!]!
  emailVerified: Boolean
  phoneNumberVerified: Boolean
}
```

### EducationRecord

```graphql
type EducationRecord {
  institutionName: String!
  state: String!
  city: String!
  countryCode: String!
  degree: String!
  programme: String!
  yearOfGraduation: String!
  studentNumber: String
  studentCardFrontFileId: String
  studentCardBackFileId: String
  transcriptFileId: String
  isVerified: Boolean
}
```

### AdvisorRecord

```graphql
type AdvisorRecord {
  firstName: String!
  lastName: String!
  phoneNumber: String
  email: String!
  associationRecords: [AssociationRecord!]!
}
```

### AssociationRecord

```graphql
type AssociationRecord {
  organisationName: String!
  title: String!
  sectorCode: String!
  state: String!
  city: String!
  countryCode: String!
  yearCommencement: String!
  yearCessation: String
}
```

### ProjectRecord

```graphql
type ProjectRecord {
  ref: String!
  name: String!
  projectCategoryKey: String!
  description: String!
  whitepaperFileIds: [DropFile]
  presentationFileIds: [DropFile]
}
```

### DropFile

```graphql
type DropFile {
  receivedAt: Date
  fileId: String!
}
```

### ApplicationMeta

```graphql
type ApplicationMeta {
  createdAt: Date
  updatedAt: Date
  approvedAt: Date
  rejectedAt: Date
  deletedAt: Date
}
```

### VerificationKey

```graphql
type VerificationKey {
  publicKey: String!
  password: String!
}
```

