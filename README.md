# Ultimate Member WordPress Plugin REST API

## Overview

This REST API provides a standardized interface for interacting with UltimateMember membership data in WordPress. It allows developers to programmatically manage users, profiles, custom fields, forms, submissions, and roles through HTTP requests.

The API follows WordPress REST API conventions and is designed for custom integrations, external applications, and headless WordPress setups.

> Notice: This project was developed with the assistance of AI tools.

### Base URL
```
https://your-wordpress-site.com/wp-json/um/v1
```

### Authentication
The API supports two authentication methods:

1. **WordPress Nonce (X-WP-Nonce header)**: For authenticated requests from logged-in WordPress users.
2. **Bearer Token**: For external applications using JWT tokens.

All endpoints require authentication unless otherwise noted.

## Endpoints

### Users

#### GET /users
Retrieve a paginated list of UltimateMember users.

**Parameters:**
- `page` (integer, optional): Page number (default: 1)
- `per_page` (integer, optional): Items per page (default: 20, max: 100)
- `role` (string, optional): Filter by role

**Response (200):**
```json
{
  "total": 204,
  "per_page": 20,
  "page": 1,
  "users": [
    {
      "id": 123,
      "username": "jdoe",
      "email": "jdoe@example.com",
      "display_name": "John Doe",
      "first_name": "John",
      "last_name": "Doe",
      "roles": ["subscriber", "um_member"],
      "status": "active",
      "registration_date": "2025-11-06T14:19:00Z",
      "profile_url": "https://example.com/members/jdoe",
      "avatar_url": "https://example.com/wp-content/uploads/avatar.jpg",
      "meta": {}
    }
  ]
}
```

#### POST /users
Create a new UltimateMember user.

**Request Body:**
```json
{
  "username": "jdoe",
  "email": "jdoe@example.com",
  "password": "securepassword",
  "first_name": "John",
  "last_name": "Doe",
  "roles": ["subscriber"],
  "profile_fields": {
    "job_title": "Engineer"
  }
}
```

**Response (201):** User object as above.

#### GET /users/{user_id}
Retrieve details for a specific user.

**Parameters:**
- `user_id` (integer, required): User ID

**Response (200):** User object.

#### PATCH /users/{user_id}
Update an existing user.

**Request Body:**
```json
{
  "email": "newemail@example.com",
  "display_name": "Updated Name",
  "roles": ["editor"],
  "status": "inactive"
}
```

**Response (200):** Updated user object.

#### DELETE /users/{user_id}
Delete a user.

**Parameters:**
- `user_id` (integer, required): User ID

**Response (204):** No content.

### Profiles

#### GET /profiles/{user_id}
Retrieve profile data for a user, including custom fields.

**Parameters:**
- `user_id` (integer, required): User ID

**Response (200):**
```json
{
  "id": 123,
  "username": "jdoe",
  "email": "jdoe@example.com",
  "display_name": "John Doe",
  "first_name": "John",
  "last_name": "Doe",
  "roles": ["subscriber", "um_member"],
  "status": "active",
  "registration_date": "2025-11-06T14:19:00Z",
  "profile_url": "https://example.com/members/jdoe",
  "avatar_url": "https://example.com/wp-content/uploads/avatar.jpg",
  "meta": {},
  "fields": {
    "job_title": "Senior Engineer",
    "location": "Berlin",
    "biography": "Experienced developer and community mentor."
  },
  "member_since": "2022-03-01"
}
```

#### PATCH /profiles/{user_id}
Update profile fields and metadata.

**Request Body:**
```json
{
  "display_name": "Updated Display Name",
  "profile_fields": {
    "job_title": "Lead Engineer",
    "location": "Munich"
  },
  "meta": {
    "custom_meta": "value"
  }
}
```

**Response (200):** Updated profile object.

### Fields

#### GET /fields
List all UltimateMember profile fields.

**Response (200):**
```json
[
  {
    "key": "um_first_name",
    "label": "First Name",
    "type": "text",
    "form_id": 12,
    "required": true,
    "order": 1,
    "config": {}
  }
]
```

#### GET /fields/{field_key}
Retrieve a specific field definition.

**Parameters:**
- `field_key` (string, required): Field key

**Response (200):** Field definition object.

#### PATCH /fields/{field_key}
Update a field definition.

**Request Body:**
```json
{
  "label": "Updated Label",
  "required": false,
  "order": 2,
  "config": {
    "placeholder": "Enter value"
  }
}
```

**Response (200):** Updated field definition.

### Forms

#### GET /forms
List all UltimateMember forms.

**Response (200):**
```json
[
  {
    "id": 7,
    "title": "Membership Registration",
    "type": "registration",
    "status": "published",
    "description": "Register for membership with custom profile fields.",
    "fields": [
      {
        "key": "um_first_name",
        "label": "First Name",
        "type": "text",
        "form_id": 7,
        "required": true,
        "order": 1,
        "config": {}
      }
    ]
  }
]
```

#### GET /forms/{form_id}
Retrieve a specific form definition.

**Parameters:**
- `form_id` (integer, required): Form ID

**Response (200):** Form object.

### Submissions

#### GET /forms/{form_id}/submissions
List submissions for a specific form.

**Parameters:**
- `form_id` (integer, required): Form ID
- `page` (integer, optional): Page number (default: 1)
- `per_page` (integer, optional): Items per page (default: 20, max: 100)
- `status` (string, optional): Filter by status (e.g., "pending")

**Response (200):**
```json
{
  "total": 50,
  "page": 1,
  "per_page": 20,
  "submissions": [
    {
      "id": 477,
      "form_id": 7,
      "user_id": 123,
      "status": "pending",
      "created_at": "2026-04-19T12:34:56Z",
      "updated_at": "2026-04-20T08:10:00Z",
      "data": {
        "first_name": "John",
        "last_name": "Doe"
      }
    }
  ]
}
```

#### POST /forms/{form_id}/submissions
Submit a new form entry.

**Request Body:**
```json
{
  "user_id": 123,
  "data": {
    "first_name": "John",
    "last_name": "Doe",
    "email": "jdoe@example.com"
  }
}
```

**Response (201):** Submission object.

#### GET /submissions/{submission_id}
Retrieve details for a specific submission.

**Parameters:**
- `submission_id` (integer, required): Submission ID

**Response (200):** Submission object.

#### DELETE /submissions/{submission_id}
Delete a submission.

**Parameters:**
- `submission_id` (integer, required): Submission ID

**Response (204):** No content.

### Roles

#### GET /roles
List all membership roles.

**Response (200):**
```json
[
  {
    "id": "subscriber",
    "label": "Subscriber",
    "capabilities": {
      "read": true,
      "edit_posts": false
    }
  }
]
```

#### GET /roles/{role_id}
Retrieve metadata for a specific role.

**Parameters:**
- `role_id` (string, required): Role ID

**Response (200):** Role object.

## Error Handling

All endpoints return standard HTTP status codes. Error responses follow this format:

```json
{
  "code": "rest_invalid_param",
  "message": "Invalid request parameters.",
  "data": {}
}
```

Common error codes:
- `401`: Authentication required
- `404`: Resource not found
- `400`: Invalid request
- `500`: Internal server error

## Usage Examples

### JavaScript (Fetch API)
```javascript
// Get users
fetch('/wp-json/um/v1/users', {
  headers: {
    'X-WP-Nonce': wpApiSettings.nonce
  }
})
.then(response => response.json())
.then(data => console.log(data));
```

### PHP (WordPress)
```php
$request = new WP_REST_Request('GET', '/um/v1/users');
$request->set_param('per_page', 10);
$response = rest_do_request($request);
$data = $response->get_data();
```

## Notes

- This API specification defines the contract for the plugin. Actual implementation requires WordPress plugin development.
- Field keys and form IDs should match UltimateMember configuration.
- Pagination follows WordPress REST API standards.
- All dates are in ISO 8601 format.