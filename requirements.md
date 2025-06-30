# Backend Feature Requirements Specification

This document details the functional and non-functional requirements for key backend features of the Airbnb clone.

## 1. User Authentication

### 1.1. User Registration

**API Endpoint:**
* `POST /api/v1/auth/register`

**Input Specifications:**
* `username`: String, unique, min 3 chars, max 50 chars.
* `email`: String, unique, valid email format, max 100 chars.
* `password`: String, min 8 chars, max 255 chars, must contain at least one uppercase letter, one lowercase letter, one number, and one special character.
* `first_name`: String, optional, max 50 chars.
* `last_name`: String, optional, max 50 chars.
* `phone_number`: String, optional, valid phone number format, max 20 chars.
* `role`: String, optional, default 'guest', enum: ['guest', 'host'].

**Output Specifications (Success):**
* **Status Code:** `201 Created`
* **Body:**
    ```json
    {
      "message": "User registered successfully",
      "user_id": "uuid-of-new-user",
      "username": "newuser",
      "email": "newuser@example.com",
      "role": "guest",
      "token": "JWT_token_string"
    }
    ```

**Output Specifications (Failure):**
* **Status Code:** `400 Bad Request` (Validation errors, duplicate username/email)
* **Status Code:** `500 Internal Server Error`
* **Body (Error):**
    ```json
    {
      "error": "Error description"
    }
    ```

**Validation Rules:**
* Username, email, password are required.
* Username and email must be unique in the database.
* Email must be a valid email format.
* Password complexity rules as defined in input spec.
* Role must be 'guest' or 'host'.

**Performance Criteria:**
* Response time: Max 200ms for 90% of requests.
* Throughput: Support 500 concurrent registration requests.

### 1.2. User Login

**API Endpoint:**
* `POST /api/v1/auth/login`

**Input Specifications:**
* `username_or_email`: String, min 3 chars, max 100 chars.
* `password`: String, min 8 chars, max 255 chars.

**Output Specifications (Success):**
* **Status Code:** `200 OK`
* **Body:**
    ```json
    {
      "message": "Login successful",
      "user_id": "uuid-of-user",
      "username": "loggedinuser",
      "email": "loggedinuser@example.com",
      "role": "host",
      "token": "JWT_token_string",
      "expires_in": 3600 // seconds
    }
    ```

**Output Specifications (Failure):**
* **Status Code:** `401 Unauthorized` (Invalid credentials)
* **Status Code:** `400 Bad Request` (Validation errors)
* **Status Code:** `500 Internal Server Error`
* **Body (Error):**
    ```json
    {
      "error": "Invalid username/email or password"
    }
    ```

**Validation Rules:**
* Username/email and password are required.
* Credentials must match a registered user.

**Performance Criteria:**
* Response time: Max 150ms for 90% of requests.
* Throughput: Support 1000 concurrent login requests.

---

## 2. Property Management

### 2.1. Create Property Listing

**API Endpoint:**
* `POST /api/v1/properties` (Requires Host authentication)

**Input Specifications:**
* `title`: String, min 5 chars, max 255 chars.
* `description`: String, min 20 chars.
* `address`: String, max 255 chars.
* `city`: String, max 100 chars.
* `state`: String, optional, max 100 chars.
* `zip_code`: String, optional, max 20 chars.
* `country`: String, max 100 chars.
* `latitude`: Decimal (10,7), optional, valid geographic coordinate.
* `longitude`: Decimal (10,7), optional, valid geographic coordinate.
* `price_per_night`: Decimal (10,2), greater than 0.
* `number_of_guests`: Integer, greater than 0.
* `number_of_beds`: Integer, optional, greater than or equal to 0.
* `number_of_bedrooms`: Integer, optional, greater than or equal to 0.
* `number_of_bathrooms`: Decimal (3,1), optional, greater than or equal to 0.
* `amenities`: Array of Strings (e.g., ["WiFi", "Pool", "Parking"]), optional.
* `property_type`: String, enum: ['apartment', 'house', 'villa', 'condo', 'cabin', 'other'].
* `images`: Array of Strings (image URLs, expected from separate image upload service), optional.

**Output Specifications (Success):**
* **Status Code:** `201 Created`
* **Body:**
    ```json
    {
      "message": "Property created successfully",
      "property_id": "uuid-of-new-property",
      "title": "Cozy Apartment in City Center",
      "host_id": "uuid-of-host"
    }
    ```

**Output Specifications (Failure):**
* **Status Code:** `400 Bad Request` (Validation errors)
* **Status Code:** `401 Unauthorized` (Not authenticated as host)
* **Status Code:** `500 Internal Server Error`
* **Body (Error):**
    ```json
    {
      "error": "Error description"
    }
    ```

**Validation Rules:**
* Host authentication required.
* Title, description, address, city, country, price_per_night, number_of_guests, property_type are required.
* `price_per_night` and `number_of_guests` must be positive.
* `property_type` must be one of the defined enums.
* Latitude/longitude must be valid ranges.

**Performance Criteria:**
* Response time: Max 500ms for 90% of requests (due to potential image processing/storage).
* Throughput: Support 100 concurrent listing creation requests.

### 2.2. Get Property Details

**API Endpoint:**
* `GET /api/v1/properties/{property_id}`

**Input Specifications:**
* `property_id`: String (UUID) from URL path.

**Output Specifications (Success):**
* **Status Code:** `200 OK`
* **Body:**
    ```json
    {
      "property_id": "uuid-of-property",
      "host_id": "uuid-of-host",
      "title": "Cozy Apartment in City Center",
      "description": "A beautiful and spacious apartment...",
      "address": "123 Main St",
      "city": "Anytown",
      "country": "USA",
      "price_per_night": 150.00,
      "number_of_guests": 4,
      // ... other property details
      "images": ["url1", "url2"],
      "amenities": ["WiFi", "Kitchen"],
      "created_at": "2024-01-01T10:00:00Z",
      "updated_at": "2024-01-01T10:00:00Z",
      "host_details": {
          "user_id": "uuid-of-host",
          "username": "hostuser",
          "first_name": "Host",
          "last_name": "User"
      }
    }
    ```

**Output Specifications (Failure):
* **Status Code:** `404 Not Found` (Property not found)
* **Status Code:** `500 Internal Server Error`
* **Body (Error):**
    ```json
    {
      "error": "Property not found"
    }
    ```

**Validation Rules:**
* `property_id` must be a valid UUID format.

**Performance Criteria:**
* Response time: Max 100ms for 95% of requests.
* Throughput: Support 2000 concurrent requests.

---

## 3. Booking System

### 3.1. Create Booking

**API Endpoint:**
* `POST /api/v1/bookings` (Requires Guest authentication)

**Input Specifications:**
* `property_id`: String (UUID), required.
* `check_in_date`: Date string (YYYY-MM-DD), required, future date.
* `check_out_date`: Date string (YYYY-MM-DD), required, after `check_in_date`.
* `total_guests`: Integer, required, positive, not exceeding property's `number_of_guests` capacity.

**Output Specifications (Success):
* **Status Code:** `201 Created`
* **Body:**
    ```json
    {
      "message": "Booking request received",
      "booking_id": "uuid-of-new-booking",
      "property_id": "uuid-of-property",
      "guest_id": "uuid-of-guest",
      "check_in_date": "2025-07-01",
      "check_out_date": "2025-07-07",
      "total_price": 900.00, // Calculated by backend
      "booking_status": "pending_payment"
    }
    ```

**Output Specifications (Failure):
* **Status Code:** `400 Bad Request` (Validation errors, dates overlap with existing booking, guest count exceeds capacity)
* **Status Code:** `401 Unauthorized` (Not authenticated as guest)
* **Status Code:** `404 Not Found` (Property not found)
* **Status Code:** `409 Conflict` (Dates unavailable)
* **Status Code:** `500 Internal Server Error`
* **Body (Error):**
    ```json
    {
      "error": "Error description (e.g., 'Dates are not available', 'Guest count exceeds property capacity')"
    }
    ```

**Validation Rules:**
* Guest authentication required.
* `property_id`, `check_in_date`, `check_out_date`, `total_guests` are required.
* `check_in_date` must be in the future.
* `check_out_date` must be after `check_in_date`.
* `total_guests` must be positive and not exceed the `property.number_of_guests`.
* Overlap check: Ensure no existing bookings for the specified `property_id` and `check_in_date`/`check_out_date` range.

**Performance Criteria:**
* Response time: Max 300ms for 90% of requests.
* Throughput: Support 300 concurrent booking requests.

### 3.2. Get User Bookings

**API Endpoint:**
* `GET /api/v1/users/{user_id}/bookings` (Requires authentication, user_id must match authenticated user or admin)

**Input Specifications:**
* `user_id`: String (UUID) from URL path.
* `status`: Query parameter (optional), e.g., `?status=confirmed`, enum: ['pending', 'confirmed', 'cancelled', 'completed'].

**Output Specifications (Success):**
* **Status Code:** `200 OK`
* **Body:**
    ```json
    [
      {
        "booking_id": "uuid-of-booking-1",
        "property_id": "uuid-of-property-A",
        "property_title": "Cozy Apartment",
        "check_in_date": "2025-07-01",
        "check_out_date": "2025-07-07",
        "total_guests": 2,
        "total_price": 900.00,
        "booking_status": "confirmed",
        "created_at": "2025-06-25T10:00:00Z"
      },
      {
        "booking_id": "uuid-of-booking-2",
        "property_id": "uuid-of-property-B",
        "property_title": "Luxury Villa",
        "check_in_date": "2025-08-01",
        "check_out_date": "2025-08-10",
        "total_guests": 6,
        "total_price": 2500.00,
        "booking_status": "pending",
        "created_at": "2025-06-20T14:30:00Z"
      }
    ]
    ```

**Output Specifications (Failure):**
* **Status Code:** `401 Unauthorized` (Not authenticated)
* **Status Code:** `403 Forbidden` (User tries to access bookings of another user without admin privileges)
* **Status Code:** `404 Not Found` (User not found)
* **Status Code:** `500 Internal Server Error`
* **Body (Error):**
    ```json
    {
      "error": "Error description"
    }
    ```

**Validation Rules:**
* Authentication required.
* Authorization: User ID in path must match authenticated user ID, or authenticated user must be an admin.
* `user_id` must be a valid UUID.
* `status` query parameter, if provided, must be one of the defined enums.

**Performance Criteria:**
* Response time: Max 200ms for 90% of requests.
* Throughput: Support 500 concurrent requests.

