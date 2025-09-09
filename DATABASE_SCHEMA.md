# Database Schema Documentation: RENTVERSE Project

**Version: 2.0**
**Date: September 9, 2025**
**Status: Approved for Implementation**

---

## 1. Introduction

### 1.1. Document Purpose

This document provides the complete technical specification for the RENTVERSE application's database schema. It details every table, column, data type, relationship, and constraint. This document will serve as the single source of truth for the backend team when implementing the schema using Prisma ORM.

### 1.2. Database Technology

- **Database Management System (DBMS)**: PostgreSQL
- **Object-Relational Mapping (ORM)**: Prisma

---

## 2. Entity-Relationship Diagram (ERD)

The following diagram illustrates the relationships between all tables in the database.

**Key Relationship Summary:**

- A `User` can have many `Properties`, `Reviews`, and `Favorites`.
- A `Property` is linked to one `Project` and is submitted by one `User` (a `PROPERTY_OWNER`).
- A `Property` can have many `PropertyImages`, `Reviews`, `Favorites`, and `PropertyDocuments`.
- `Properties` and `Amenities` have a many-to-many relationship via the `PropertyAmenities` table.

---

## 3. Detailed Table Definitions

### 3.1. Table: `Users`

Stores data for all user roles within the system.

| Column Name         | Data Type     | Description                             | Notes                         |
| :------------------ | :------------ | :-------------------------------------- | :---------------------------- |
| `id`                | `Int`         | Unique identifier for each user.        | Primary Key, Auto-increment.  |
| `fullName`          | `String`      | The user's full name.                   |                               |
| `email`             | `String`      | User's email address, used for login.   | Unique.                       |
| `password`          | `String`      | User's hashed password.                 | **Must be stored as a hash.** |
| `phone`             | `String`      | User's phone number.                    | Optional.                     |
| `country`           | `String`      | User's country of citizenship.          | Optional.                     |
| `profilePictureUrl` | `String`      | URL to the user's profile picture.      | Optional.                     |
| `role`              | `Enum (Role)` | The user's role in the system.          | See Enum Definitions below.   |
| `createdAt`         | `DateTime`    | Timestamp of when the user was created. | Auto-managed by Prisma.       |
| `updatedAt`         | `DateTime`    | Timestamp of the last user data update. | Auto-managed by Prisma.       |

### 3.2. Table: `Projects`

Master data for property developments (e.g., apartment buildings, housing complexes). Used for the auto-fill feature.

| Column Name      | Data Type  | Description                                | Notes                        |
| :--------------- | :--------- | :----------------------------------------- | :--------------------------- |
| `id`             | `Int`      | Unique identifier for each project.        | Primary Key, Auto-increment. |
| `projectName`    | `String`   | The official name of the property project. |                              |
| `developer`      | `String`   | The name of the development company.       | Optional.                    |
| `address`        | `String`   | The general address of the project.        |                              |
| `completionDate` | `DateTime` | The date the project was completed.        | Optional.                    |

### 3.3. Table: `Properties`

The core table containing data for each individual property listing.

| Column Name        | Data Type               | Description                                      | Notes                                   |
| :----------------- | :---------------------- | :----------------------------------------------- | :-------------------------------------- |
| `id`               | `Int`                   | Unique identifier for each property.             | Primary Key, Auto-increment.            |
| `title`            | `String`                | The title of the listing advertisement.          |                                         |
| `description`      | `Text`                  | A detailed description of the property unit.     |                                         |
| `listingType`      | `Enum (ListingType)`    | The type of transaction (e.g., Sale, Rent).      | See Enum Definitions.                   |
| `propertyType`     | `Enum (PropertyType)`   | The category of the property.                    | See Enum Definitions.                   |
| `rentalPrice`      | `Float`                 | The rental price of the property.                | Optional.                               |
| `salePrice`        | `Float`                 | The sale price of the property.                  | Optional.                               |
| `maintenanceFee`   | `Float`                 | The monthly common area maintenance fee.         | Optional.                               |
| `sizeSqft`         | `Int`                   | The size of the property in square feet.         |                                         |
| `bedrooms`         | `Int`                   | The number of bedrooms.                          |                                         |
| `bathrooms`        | `Int`                   | The number of bathrooms.                         |                                         |
| `furnishingStatus` | `Enum (Furnishing)`     | The furnishing level of the property.            | See Enum Definitions.                   |
| `status`           | `Enum (PropertyStatus)` | The current workflow status of the listing.      | Default: `PENDING`.                     |
| `listedById`       | `Int`                   | The ID of the `PROPERTY_OWNER` who submitted it. | Foreign Key -> `Users.id`.              |
| `projectId`        | `Int`                   | Links the property to its parent project.        | Foreign Key -> `Projects.id`. Optional. |

### 3.4. Table: `PropertyDocuments`

Stores ownership documents for verification purposes.

| Column Name          | Data Type                   | Description                                  | Notes                           |
| :------------------- | :-------------------------- | :------------------------------------------- | :------------------------------ |
| `id`                 | `Int`                       | Unique identifier for the document record.   | Primary Key, Auto-increment.    |
| `propertyId`         | `Int`                       | Links the document to the property.          | Foreign Key -> `Properties.id`. |
| `documentType`       | `Enum (DocumentType)`       | The type of document submitted.              | See Enum Definitions.           |
| `fileUrl`            | `String`                    | The URL where the document file is stored.   |                                 |
| `verificationStatus` | `Enum (VerificationStatus)` | The admin's verification status for the doc. | Default: `PENDING`.             |
| `uploadedAt`         | `DateTime`                  | Timestamp of when the document was uploaded. | Auto-managed.                   |

### 3.5. Table: `PropertyImages`

Stores the image gallery for each property.

| Column Name    | Data Type | Description                                    | Notes                           |
| :------------- | :-------- | :--------------------------------------------- | :------------------------------ |
| `id`           | `Int`     | Unique identifier for each image.              | Primary Key, Auto-increment.    |
| `propertyId`   | `Int`     | Links the image to the relevant property.      | Foreign Key -> `Properties.id`. |
| `imageUrl`     | `String`  | The public URL of the image file.              |                                 |
| `displayOrder` | `Int`     | The display order in the gallery (1, 2, 3...). | Optional, default: 0.           |

### 3.6. Table: `Amenities`

Master table for all available property amenities.

| Column Name | Data Type | Description                          | Notes                          |
| :---------- | :-------- | :----------------------------------- | :----------------------------- |
| `id`        | `Int`     | Unique identifier for each amenity.  | Primary Key, Auto-increment.   |
| `name`      | `String`  | The name of the amenity.             | Unique. e.g., "Swimming Pool". |
| `icon`      | `String`  | The name or URL of the amenity icon. | Optional.                      |

### 3.7. Table: `PropertyAmenities`

A join table for the many-to-many relationship between `Properties` and `Amenities`.

| Column Name  | Data Type | Description               | Notes                            |
| :----------- | :-------- | :------------------------ | :------------------------------- |
| `propertyId` | `Int`     | Links to `Properties.id`. | Part of a composite Primary Key. |
| `amenityId`  | `Int`     | Links to `Amenities.id`.  | Part of a composite Primary Key. |

### 3.8. Table: `Reviews`

Stores user-submitted reviews and ratings for properties.

| Column Name  | Data Type  | Description                                 | Notes                           |
| :----------- | :--------- | :------------------------------------------ | :------------------------------ |
| `id`         | `Int`      | Unique identifier for each review.          | Primary Key, Auto-increment.    |
| `propertyId` | `Int`      | The property being reviewed.                | Foreign Key -> `Properties.id`. |
| `userId`     | `Int`      | The user who wrote the review.              | Foreign Key -> `Users.id`.      |
| `rating`     | `Int`      | The rating given by the user.               | A value from 1 to 5.            |
| `comment`    | `Text`     | The text content of the review.             | Optional.                       |
| `createdAt`  | `DateTime` | Timestamp of when the review was submitted. | Auto-managed.                   |

### 3.9. Table: `Favorites`

A join table to manage users' favorited properties (many-to-many relationship).

| Column Name  | Data Type | Description                      | Notes                            |
| :----------- | :-------- | :------------------------------- | :------------------------------- |
| `userId`     | `Int`     | Links to the user.               | Part of a composite Primary Key. |
| `propertyId` | `Int`     | Links to the favorited property. | Part of a composite Primary Key. |

### 3.10. Table: `RentalAgreements`

Stores data for each generated rental agreement (for Challenge 3).

| Column Name       | Data Type                | Description                              | Notes                           |
| :---------------- | :----------------------- | :--------------------------------------- | :------------------------------ |
| `id`              | `Int`                    | Unique identifier for each agreement.    | Primary Key, Auto-increment.    |
| `propertyId`      | `Int`                    | The property being rented.               | Foreign Key -> `Properties.id`. |
| `tenantId`        | `Int`                    | The user who is the tenant.              | Foreign Key -> `Users.id`.      |
| `ownerId`         | `Int`                    | The user who is the owner.               | Foreign Key -> `Users.id`.      |
| `startDate`       | `DateTime`               | The start date of the rental period.     |                                 |
| `endDate`         | `DateTime`               | The end date of the rental period.       |                                 |
| `rentalAmount`    | `Float`                  | The agreed-upon rental amount.           |                                 |
| `status`          | `Enum (AgreementStatus)` | The current status of the agreement.     | See Enum Definitions.           |
| `generatedPdfUrl` | `String`                 | URL to the generated PDF agreement file. | Optional.                       |

---

## 4. Enum Type Definitions

- **`Role`**:

  - `PROPERTY_OWNER`: A user who lists and manages properties.
  - `TENANT`: A user who is looking to rent properties.
  - `ADMIN`: A user with moderation and administrative privileges.

- **`ListingType`**:

  - `SALE`
  - `RENT`
  - `BOTH`

- **`PropertyStatus`**:

  - `PENDING`: Awaiting admin approval.
  - `APPROVED`: Live and visible to the public.
  - `REJECTED`: Declined by an admin.
  - `RENTED`: Currently off the market because it is rented.
  - `SOLD`: Currently off the market because it has been sold.

- **`DocumentType`**:

  - `OWNERSHIP_CERTIFICATE`
  - `TAX_RECORD`
  - `UTILITY_BILL`

- **`VerificationStatus`**:

  - `PENDING`: Awaiting admin review.
  - `VERIFIED`: Approved by an admin.
  - `REJECTED`: Rejected by an admin.

- **`PropertyType`**: `APARTMENT`, `HOUSE`, `PENTHOUSE`, `STUDIO`, `COMMERCIAL`
- **`Furnishing`**: `UNFURNISHED`, `PARTIALLY_FURNISHED`, `FULLY_FURNISHED`
- **`AgreementStatus`**: `ACTIVE`, `EXPIRED`, `TERMINATED`
