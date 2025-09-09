# API Contract Documentation: RENTVERSE Project

**Version: 2.0**
**Date: September 9, 2025**
**Status: Approved for Implementation**

---

## 1. Introduction

### 1.1. Document Purpose

This document is the single source of truth for all API interactions within the RENTVERSE ecosystem. It provides a strict definition for every endpoint, including methods, request structures, and response schemas. Its purpose is to enable parallel development between frontend and backend teams and minimize integration errors.

### 1.2. Specification Format

This specification is written using **OpenAPI Specification (OAS) v3.0.0** in **YAML** format for human readability.

---

## 2. OpenAPI Specification (Version 2.0)

This specification reflects the expanded features including user profiles, reviews, favorites, and document uploads.

```yaml
openapi: "3.0.0"
info:
  title: RENTVERSE API v2.0
  description: An updated, comprehensive API for the RENTVERSE platform, supporting an expanded feature set including reviews, favorites, and document verification.
  version: 2.0.0
servers:
  - url: http://localhost:8080/api
    description: Local Development Server via API Gateway
paths:
  # --- Authentication & User Management ---
  /auth/register:
    post:
      summary: Register a new user
      tags: [Authentication]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UserRegister"
      responses:
        "201":
          description: User created successfully
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/UserResponse"
        "409":
          description: Email already exists
  /auth/login:
    post:
      summary: Log in a user
      tags: [Authentication]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UserLogin"
      responses:
        "200":
          description: Login successful, returns access token
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/LoginResponse"
        "401":
          description: Invalid credentials
  /users/me:
    get:
      summary: Get current user's profile
      tags: [Users]
      security: [{ bearerAuth: [] }]
      responses:
        "200":
          description: User profile data
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/UserResponse"
    patch:
      summary: Update current user's profile
      tags: [Users]
      security: [{ bearerAuth: [] }]
      requestBody:
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UserProfileUpdate"
      responses:
        "200":
          description: Profile updated successfully
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/UserResponse"

  # --- Property & Project Management ---
  /projects:
    get:
      summary: Get all projects for auto-fill
      tags: [Projects]
      security: [{ bearerAuth: [] }]
      responses:
        "200":
          description: A list of projects
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/ProjectSummary"
  /properties:
    get:
      summary: Get all approved properties (for public view)
      tags: [Properties]
      responses:
        "200":
          description: A list of public properties
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/PropertySummary"
    post:
      summary: Submit a new property listing
      tags: [Properties]
      security: [{ bearerAuth: [] }]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/PropertyCreate"
      responses:
        "201":
          description: Property submitted for approval
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/PropertyDetailed"
  /properties/{id}:
    get:
      summary: Get details for a single property
      tags: [Properties]
      parameters:
        - in: path
          name: id
          required: true
          schema: { type: integer }
      responses:
        "200":
          description: Detailed property information
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/PropertyDetailed"
        "404":
          description: Property not found

  # --- Admin Endpoints ---
  /admin/properties/pending:
    get:
      summary: Get all properties awaiting approval
      tags: [Admin]
      security: [{ bearerAuth: [] }]
      responses:
        "200":
          description: A list of pending properties
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/PropertySummary"
        "403":
          description: Forbidden (user is not an Admin)
  /admin/properties/{id}/status:
    patch:
      summary: Approve or reject a property
      tags: [Admin]
      security: [{ bearerAuth: [] }]
      parameters:
        - in: path
          name: id
          required: true
          schema: { type: integer }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/StatusUpdate"
      responses:
        "200":
          description: Property status updated
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/PropertyDetailed"

  # --- New Feature Endpoints ---
  /properties/{id}/favorite:
    post:
      summary: Add a property to favorites
      tags: [Favorites]
      security: [{ bearerAuth: [] }]
      parameters:
        - in: path
          name: id
          required: true
          schema: { type: integer }
      responses:
        "200":
          description: Property added to favorites
    delete:
      summary: Remove a property from favorites
      tags: [Favorites]
      security: [{ bearerAuth: [] }]
      parameters:
        - in: path
          name: id
          required: true
          schema: { type: integer }
      responses:
        "204":
          description: Property removed from favorites
  /properties/{id}/reviews:
    post:
      summary: Submit a review for a property
      tags: [Reviews]
      security: [{ bearerAuth: [] }]
      parameters:
        - in: path
          name: id
          required: true
          schema: { type: integer }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/ReviewCreate"
      responses:
        "201":
          description: Review submitted successfully
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ReviewResponse"
  /uploads/signed-url:
    get:
      summary: Get a secure URL for file uploads
      tags: [Uploads]
      security: [{ bearerAuth: [] }]
      parameters:
        - in: query
          name: fileName
          required: true
          schema: { type: string }
        - in: query
          name: fileType
          required: true
          schema: { type: string }
      responses:
        "200":
          description: Secure upload URL provided
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/SignedUrlResponse"

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
  schemas:
    # --- User Schemas ---
    UserRegister:
      type: object
      required: [fullName, email, password, role]
      properties:
        fullName: { type: string, example: "Budi Siregar" }
        email:
          { type: string, format: email, example: "budi.siregar@example.com" }
        password:
          { type: string, format: password, example: "SecurePassword123" }
        role: { type: string, enum: [PROPERTY_OWNER, TENANT] }
    UserLogin:
      type: object
      properties:
        email: { type: string, format: email }
        password: { type: string, format: password }
    LoginResponse:
      type: object
      properties:
        accessToken: { type: string }
    UserResponse:
      type: object
      properties:
        id: { type: integer }
        fullName: { type: string }
        email: { type: string }
        phone: { type: string, nullable: true }
        country: { type: string, nullable: true }
        profilePictureUrl: { type: string, nullable: true }
        role: { type: string, enum: [PROPERTY_OWNER, TENANT, ADMIN] }
    UserProfileUpdate:
      type: object
      properties:
        fullName: { type: string }
        phone: { type: string }
        country: { type: string }
        profilePictureUrl: { type: string }

    # --- Property & Project Schemas ---
    ProjectSummary:
      type: object
      properties:
        id: { type: integer }
        projectName: { type: string }
    PropertyCreate:
      type: object
      required: [title, listingType, propertyType, sizeSqft]
      properties:
        title: { type: string }
        description: { type: string }
        listingType: { type: string, enum: [SALE, RENT, BOTH] }
        propertyType:
          { type: string, enum: [APARTMENT, HOUSE, PENTHOUSE, STUDIO] }
        rentalPrice: { type: number, nullable: true }
        salePrice: { type: number, nullable: true }
        maintenanceFee: { type: number, nullable: true }
        sizeSqft: { type: integer }
        bedrooms: { type: integer }
        bathrooms: { type: integer }
        furnishingStatus:
          {
            type: string,
            enum: [UNFURNISHED, PARTIALLY_FURNISHED, FULLY_FURNISHED],
          }
        projectId: { type: integer, nullable: true }
        ownershipDocumentUrl:
          {
            type: string,
            format: uri,
            description: "URL from signed-url endpoint",
          }
    PropertySummary:
      type: object
      properties:
        id: { type: integer }
        title: { type: string }
        rentalPrice: { type: number, nullable: true }
        salePrice: { type: number, nullable: true }
        bedrooms: { type: integer }
        bathrooms: { type: integer }
        sizeSqft: { type: integer }
        imageUrl: { type: string, format: uri } # Primary image
    PropertyDetailed:
      allOf:
        - $ref: "#/components/schemas/PropertySummary"
        - type: object
          properties:
            description: { type: string }
            listingType: { type: string, enum: [SALE, RENT, BOTH] }
            propertyType:
              { type: string, enum: [APARTMENT, HOUSE, PENTHOUSE, STUDIO] }
            furnishingStatus:
              {
                type: string,
                enum: [UNFURNISHED, PARTIALLY_FURNISHED, FULLY_FURNISHED],
              }
            status:
              {
                type: string,
                enum: [PENDING, APPROVED, REJECTED, RENTED, SOLD],
              }
            images:
              type: array
              items:
                type: object
                properties:
                  id: { type: integer }
                  imageUrl: { type: string }
            reviews:
              type: array
              items:
                $ref: "#/components/schemas/ReviewResponse"
    StatusUpdate:
      type: object
      required: [status]
      properties:
        status: { type: string, enum: [APPROVED, REJECTED] }

    # --- Review & Favorite Schemas ---
    ReviewCreate:
      type: object
      required: [rating]
      properties:
        rating: { type: integer, minimum: 1, maximum: 5 }
        comment: { type: string, nullable: true }
    ReviewResponse:
      type: object
      properties:
        id: { type: integer }
        rating: { type: integer }
        comment: { type: string, nullable: true }
        author:
          type: object
          properties:
            fullName: { type: string }
            profilePictureUrl: { type: string, nullable: true }
        createdAt: { type: string, format: date-time }

    # --- Upload Schema ---
    SignedUrlResponse:
      type: object
      properties:
        uploadUrl:
          { type: string, description: "The pre-signed URL to PUT the file to" }
        fileUrl:
          {
            type: string,
            description: "The final URL of the file after upload",
          }

    ErrorResponse:
      type: object
      properties:
        message: { type: string }
```
