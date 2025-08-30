# Data Models

This document describes the data models used in the IBCOL GraphQL Micro API and their relationships.

## Entity Relationship Diagram

```
┌───────────────┐       ┌───────────────┐       ┌───────────────┐
│               │       │               │       │               │
│  Application  │───┐   │  AccessToken  │       │  AdminEmail   │
│               │   │   │               │       │               │
└───────────────┘   │   └───────────────┘       └───────────────┘
        │           │            │                      │
        │           │            │                      │
        │           │            │                      │
        ▼           │            ▼                      ▼
┌───────────────┐   │   ┌───────────────┐       ┌───────────────┐
│               │   │   │               │       │               │
│  Student      │◄──┘   │  User         │◄──────│  Admin        │
│  Record       │       │  (Implicit)   │       │  (Implicit)   │
│               │       │               │       │               │
└───────────────┘       └───────────────┘       └───────────────┘
        │
        │
        │
        ▼
┌───────────────┐       ┌───────────────┐
│               │       │               │
│  Education    │       │  Project      │
│  Record       │       │  Record       │
│               │       │               │
└───────────────┘       └───────────────┘
                                │
                                │
                                │
                                ▼
                        ┌───────────────┐
                        │               │
                        │  File         │
                        │  (External)   │
                        │               │
                        └───────────────┘
```

## MongoDB Collections

The API uses the following MongoDB collections:

- `Applications`: Stores team applications
- `AccessTokens`: Manages authentication tokens
- `AdminEmails`: Controls administrative access

## Schema Definitions

### Application

The central entity representing a team's application to the blockchain olympiad.

**MongoDB Schema:**
```javascript
{
  teamName: String,
  ref: String,
  studentRecords: Array,
  advisorRecords: {type: Array, default: []},
  projectRecords: {type: Array, default: []},
  verificationKeys: {type: Array, default: []},
  meta: {
    createdAt: Date,
    updatedAt: Date,
    approvedAt: Date,
    rejectedAt: Date,
    deletedAt: Date
  }
}
```

**Key Fields:**
- `teamName`: Name of the participating team
- `ref`: Unique reference code for the application
- `studentRecords`: Array of student information
- `advisorRecords`: Array of advisor information
- `projectRecords`: Array of project submissions
- `verificationKeys`: Cryptographic keys for verification
- `meta`: Metadata including timestamps for various status changes

### AccessToken

Manages authentication tokens for users.

**MongoDB Schema:**
```javascript
{
  email: String,
  token: String,
  seed: String,
  activatedAt: Date,
  lastAccessedAt: Date,
  meta: {
    createdAt: Date,
    updatedAt: Date,
    deletedAt: Date
  }
}
```

**Key Fields:**
- `email`: User's email address (primary identifier)
- `token`: Authentication token
- `seed`: Seed phrase for verification
- `activatedAt`: When the token was activated
- `lastAccessedAt`: Last time the token was used
- `meta`: Standard metadata timestamps

### AdminEmail

Controls administrative access to the system.

**MongoDB Schema:**
```javascript
{
  email: String,
  meta: {
    createdAt: Date,
    updatedAt: Date,
    deletedAt: Date
  }
}
```

**Key Fields:**
- `email`: Administrator's email address
- `meta`: Standard metadata timestamps

## Nested Data Structures

### StudentRecord

Information about a student team member.

```javascript
{
  firstName: String,
  lastName: String,
  phoneNumber: String,
  email: String,
  educationRecords: Array,
  emailVerified: Boolean,
  phoneNumberVerified: Boolean
}
```

### EducationRecord

Details about a student's educational background.

```javascript
{
  institutionName: String,
  state: String,
  city: String,
  countryCode: String,
  degree: String,
  programme: String,
  yearOfGraduation: String,
  studentNumber: String,
  studentCardFrontFileId: String,
  studentCardBackFileId: String,
  transcriptFileId: String,
  isVerified: Boolean
}
```

### AdvisorRecord

Information about a team advisor.

```javascript
{
  firstName: String,
  lastName: String,
  phoneNumber: String,
  email: String,
  associationRecords: Array
}
```

### AssociationRecord

Details about an advisor's professional affiliations.

```javascript
{
  organisationName: String,
  title: String,
  sectorCode: String,
  state: String,
  city: String,
  countryCode: String,
  yearCommencement: String,
  yearCessation: String
}
```

### ProjectRecord

Information about a team's project submission.

```javascript
{
  ref: String,
  name: String,
  projectCategoryKey: String,
  description: String,
  whitepaperFileIds: Array,
  presentationFileIds: Array
}
```

### DropFile

Reference to an uploaded file.

```javascript
{
  receivedAt: Date,
  fileId: String
}
```

## Data Relationships

1. **One-to-Many: Application to StudentRecords**
   - An application contains multiple student records
   - Each student record belongs to one application

2. **One-to-Many: Application to AdvisorRecords**
   - An application can have multiple advisor records
   - Each advisor record belongs to one application

3. **One-to-Many: Application to ProjectRecords**
   - An application can have multiple project records
   - Each project record belongs to one application

4. **One-to-Many: StudentRecord to EducationRecords**
   - A student record can have multiple education records
   - Each education record belongs to one student

5. **One-to-Many: AdvisorRecord to AssociationRecords**
   - An advisor record can have multiple association records
   - Each association record belongs to one advisor

6. **One-to-Many: ProjectRecord to DropFiles**
   - A project record can have multiple whitepaper and presentation files
   - Each file reference belongs to one project

## Soft Delete Pattern

The API implements a soft delete pattern using the `deletedAt` timestamp in the `meta` object. Records are not physically removed from the database but are marked as deleted by setting this timestamp.

## Reference Generation

The system generates unique reference codes for applications and projects using the `randomstring` library, ensuring uniqueness through database checks before assignment.

