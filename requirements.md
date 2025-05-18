# Backend Feature Requirements for Airbnb Clone

## 1. User Authentication

### Description

Allows users (guests and hosts) to register, log in, and maintain authenticated sessions securely.

### API Endpoints

- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/auth/profile`
- `PUT /api/auth/profile`

### Input/Output Specifications

| Endpoint                | Input (JSON)                                                              | Output (JSON)                                                            |
| ----------------------- | ------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| POST /api/auth/register | `{ "email": "user@example.com", "password": "pass123", "role": "guest" }` | `{ "message": "User registered", "userId": "123" }`                      |
| POST /api/auth/login    | `{ "email": "user@example.com", "password": "pass123" }`                  | `{ "token": "JWT_TOKEN", "user": { ...profile } }`                       |
| GET /api/auth/profile   | (JWT token in header)                                                     | `{ "userId": "123", "email": "...", "role": "guest", "profile": {...} }` |
| PUT /api/auth/profile   | `{ "name": "New Name", "photoUrl": "...", "contact": "..." }`             | `{ "message": "Profile updated", "profile": {...} }`                     |

### Validation Rules

- Email must be a valid format.
- Password minimum length: 8 characters, with at least one number and one special character.
- Role must be either "guest" or "host".
- Profile updates validate correct formats for email, phone, and photo URL.

### Performance Criteria

- Registration and login requests must respond within 300ms.
- JWT tokens expire after 24 hours for security.
- Rate limit login attempts (e.g., max 5 attempts per 10 minutes) to prevent brute-force attacks.

---

## 2. Property Management

### Description

Hosts can create, update, and delete property listings with relevant details.

### API Endpoints

- `POST /api/properties`
- `GET /api/properties/{propertyId}`
- `PUT /api/properties/{propertyId}`
- `DELETE /api/properties/{propertyId}`
- `GET /api/properties` (with filters and pagination)

### Input/Output Specifications

| Endpoint                            | Input (JSON)                                                                                                                                   | Output (JSON)                                                |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| POST /api/properties                | `{ "title": "Cozy Apartment", "description": "...", "location": "City", "price": 100, "amenities": ["Wi-Fi", "Pool"], "availability": [...] }` | `{ "message": "Property created", "propertyId": "456" }`     |
| GET /api/properties/{propertyId}    | None                                                                                                                                           | `{ "propertyId": "456", "title": "...", "price": 100, ... }` |
| PUT /api/properties/{propertyId}    | Partial or full property fields to update                                                                                                      | `{ "message": "Property updated", "property": {...} }`       |
| DELETE /api/properties/{propertyId} | None                                                                                                                                           | `{ "message": "Property deleted" }`                          |
| GET /api/properties                 | Query params: `location`, `priceMin`, `priceMax`, `guests`, `amenities`, `page`, `limit`                                                       | `{ "properties": [...], "pagination": {...} }`               |

### Validation Rules

- Price must be a positive number.
- Title and description are required.
- Availability dates must not overlap.
- Amenities must be from a predefined list.
- Location must be a valid string (city, address).

### Performance Criteria

- Support pagination with page size default 10.
- Property retrieval should respond within 200ms.
- Ensure search/filter queries are optimized with proper indexing.

---

## 3. Booking System

### Description

Guests can book properties for specified dates; hosts can confirm or cancel bookings.

### API Endpoints

- `POST /api/bookings`
- `GET /api/bookings/{bookingId}`
- `PUT /api/bookings/{bookingId}/cancel`
- `GET /api/bookings/user/{userId}`

### Input/Output Specifications

| Endpoint                             | Input (JSON)                                                                                   | Output (JSON)                                                                      |
| ------------------------------------ | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| POST /api/bookings                   | `{ "propertyId": "456", "userId": "123", "startDate": "YYYY-MM-DD", "endDate": "YYYY-MM-DD" }` | `{ "message": "Booking created", "bookingId": "789", "status": "pending" }`        |
| GET /api/bookings/{bookingId}        | None                                                                                           | `{ "bookingId": "789", "property": {...}, "status": "confirmed", "dates": {...} }` |
| PUT /api/bookings/{bookingId}/cancel | None                                                                                           | `{ "message": "Booking cancelled", "status": "cancelled" }`                        |
| GET /api/bookings/user/{userId}      | None                                                                                           | `{ "bookings": [ {...}, {...} ] }`                                                 |

### Validation Rules

- Start date must be before end date.
- Booking dates must not overlap with existing confirmed bookings for the same property.
- Only guests can create bookings.
- Cancellation policy enforced (e.g., cancellations allowed up to 24 hours before start date).

### Performance Criteria

- Booking creation should complete within 500ms.
- Date validation logic must be efficient to avoid race conditions.
- Booking status updates should trigger notifications asynchronously.

---

# Summary

This requirements document provides the necessary technical and functional details to implement the core backend features for the Airbnb Clone project, covering key API routes, data formats, validation, and performance standards.

---
