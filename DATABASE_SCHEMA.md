# Database Schema Documentation: RENTVERSE Project

**Version: 2.3**
**Date: September 10, 2025**
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

- A `Property` has many-to-many relationships with `Amenities`, `Views`, and `FacilityCategories` (via join tables or direct links).
- A `Property` has one-to-many relationships with `PropertyImages`, `Reviews`, `Favorites`, `PropertyDocuments`, and `NearbyFacilities`.
- The core relationships between `Users`, `Properties`, and `Projects` remain central to the architecture.

---

## 3. Detailed Table Definitions

### 3.1. Table: `Users`

_(No changes from Version 2.0)_
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

_(No changes from Version 2.0)_
Master data for property developments. Used for the auto-fill feature.

| Column Name      | Data Type  | Description                                | Notes                        |
| :--------------- | :--------- | :----------------------------------------- | :--------------------------- |
| `id`             | `Int`      | Unique identifier for each project.        | Primary Key, Auto-increment. |
| `projectName`    | `String`   | The official name of the property project. |                              |
| `developer`      | `String`   | The name of the development company.       | Optional.                    |
| `address`        | `String`   | The general address of the project.        |                              |
| `completionDate` | `DateTime` | The date the project was completed.        | Optional.                    |

### 3.3. Table: `Properties`

The core table for individual property listings, now with added detail.

| Column Name         | Data Type                  | Description                                      | Notes                                   |
| :------------------ | :------------------------- | :----------------------------------------------- | :-------------------------------------- |
| `id`                | `Int`                      | Unique identifier for each property.             | Primary Key, Auto-increment.            |
| `title`             | `String`                   | The title of the listing advertisement.          |                                         |
| `description`       | `Text`                     | A detailed description of the property unit.     |                                         |
| `listingType`       | `Enum (ListingType)`       | The type of transaction (e.g., Sale, Rent).      |                                         |
| `propertyType`      | `Enum (PropertyType)`      | The category of the property.                    |                                         |
| `rentalPrice`       | `Float`                    | The rental price of the property.                | Optional.                               |
| **`paymentPeriod`** | **`Enum (PaymentPeriod)`** | **The frequency of the rental payment.**         | **NEW. Optional.**                      |
| `salePrice`         | `Float`                    | The sale price of the property.                  | Optional.                               |
| `maintenanceFee`    | `Float`                    | The monthly common area maintenance fee.         | Optional.                               |
| `sizeSqft`          | `Int`                      | The size of the property in square feet.         |                                         |
| `bedrooms`          | `Int`                      | The number of bedrooms.                          |                                         |
| `bathrooms`         | `Int`                      | The number of bathrooms.                         |                                         |
| `furnishingStatus`  | `Enum (Furnishing)`        | The furnishing level of the property.            |                                         |
| **`latitude`**      | **`Float`**                | **The geographic latitude of the property.**     | **NEW. For maps & automation.**         |
| **`longitude`**     | **`Float`**                | **The geographic longitude of the property.**    | **NEW. For maps & automation.**         |
| `status`            | `Enum (PropertyStatus)`    | The current workflow status of the listing.      | Default: `PENDING`.                     |
| `listedById`        | `Int`                      | The ID of the `PROPERTY_OWNER` who submitted it. | Foreign Key -> `Users.id`.              |
| `projectId`         | `Int`                      | Links the property to its parent project.        | Foreign Key -> `Projects.id`. Optional. |

### 3.4. Table: `PropertyDocuments`

_(No changes from Version 2.0)_
Stores ownership documents for verification.

| Column Name          | Data Type                   | Description                                  | Notes                           |
| :------------------- | :-------------------------- | :------------------------------------------- | :------------------------------ |
| `id`                 | `Int`                       | Unique identifier for the document record.   | Primary Key, Auto-increment.    |
| `propertyId`         | `Int`                       | Links the document to the property.          | Foreign Key -> `Properties.id`. |
| `documentType`       | `Enum (DocumentType)`       | The type of document submitted.              |                                 |
| `fileUrl`            | `String`                    | The URL where the document file is stored.   |                                 |
| `verificationStatus` | `Enum (VerificationStatus)` | The admin's verification status for the doc. | Default: `PENDING`.             |
| `uploadedAt`         | `DateTime`                  | Timestamp of when the document was uploaded. | Auto-managed.                   |

### 3.5. Table: `PropertyImages`, `Amenities`, `PropertyAmenities`

_(No changes from Version 2.0)_

### 3.6. Table: `Views`

**NEW TABLE**. Master table for all available property views.

| Column Name | Data Type | Description                           | Notes                                  |
| :---------- | :-------- | :------------------------------------ | :------------------------------------- |
| `id`        | `Int`     | Unique identifier for each view type. | Primary Key, Auto-increment.           |
| `name`      | `String`  | The name of the view.                 | Unique. e.g., "City View", "Sea View". |

### 3.7. Table: `PropertyViews`

**NEW TABLE**. A join table for the many-to-many relationship between `Properties` and `Views`.

| Column Name  | Data Type | Description               | Notes                            |
| :----------- | :-------- | :------------------------ | :------------------------------- |
| `propertyId` | `Int`     | Links to `Properties.id`. | Part of a composite Primary Key. |
| `viewId`     | `Int`     | Links to `Views.id`.      | Part of a composite Primary Key. |

### 3.8. Table: `FacilityCategories`

**NEW TABLE**. Master table for categories of nearby facilities.

| Column Name | Data Type | Description                           | Notes                           |
| :---------- | :-------- | :------------------------------------ | :------------------------------ |
| `id`        | `Int`     | Unique identifier for each category.  | Primary Key, Auto-increment.    |
| `name`      | `String`  | The name of the category.             | Unique. e.g., "Transportation". |
| `icon`      | `String`  | The name or URL of the category icon. | Optional.                       |

### 3.9. Table: `NearbyFacilities`

**NEW TABLE**. Stores specific nearby facilities for a property, populated automatically via a third-party API.

| Column Name        | Data Type | Description                                | Notes                                   |
| :----------------- | :-------- | :----------------------------------------- | :-------------------------------------- |
| `id`               | `Int`     | Unique identifier for the facility record. | Primary Key, Auto-increment.            |
| `propertyId`       | `Int`     | Links the facility to the property.        | Foreign Key -> `Properties.id`.         |
| `categoryId`       | `Int`     | The category of the facility.              | Foreign Key -> `FacilityCategories.id`. |
| `name`             | `String`  | The specific name of the facility.         | e.g., "Grand Indonesia Mall".           |
| `distanceInMeters` | `Int`     | The distance from the property in meters.  |                                         |

### 3.10. Table: `Reviews`, `Favorites`, `RentalAgreements`

_(No changes from Version 2.0)_

---

## 4. Enum Type Definitions

- **`Role`**: `PROPERTY_OWNER`, `TENANT`, `ADMIN`
- **`ListingType`**: `SALE`, `RENT`, `BOTH`
- **`PropertyStatus`**: `PENDING`, `APPROVED`, `REJECTED`, `RENTED`, `SOLD`
- **`DocumentType`**: `OWNERSHIP_CERTIFICATE`, `TAX_RECORD`, `UTILITY_BILL`
- **`VerificationStatus`**: `PENDING`, `VERIFIED`, `REJECTED`
- **`PropertyType`**: `APARTMENT`, `HOUSE`, `PENTHOUSE`, `STUDIO`, `COMMERCIAL`
- **`Furnishing`**: `UNFURNISHED`, `PARTIALLY_FURNISHED`, `FULLY_FURNISHED`
- **`PaymentPeriod`**: **NEW ENUM**. `MONTHLY`, `QUARTERLY`, `BI_ANNUALLY`, `YEARLY`
- **`AgreementStatus`**: `ACTIVE`, `EXPIRED`, `TERMINATED`
