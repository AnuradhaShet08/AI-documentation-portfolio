# Create User

## Goal

Create or update a known user in Fullstory and associate that user with application activity, analytics, and session data.


# Target Audience

Primary:
Backend developers

Secondary:
Integration engineers

Assumptions:
Basic REST API knowledge


## When to Use This API

Use this endpoint when:

- A user signs up
- A user authenticates
- Customer profile information changes
- CRM data is synchronized


## Business Value

This endpoint helps organizations associate customer identities with product activity and analytics data.

By identifying users, teams can:

- Connect customer profiles to behavioral analytics
- Improve customer support investigations
- Analyze product adoption by customer segment
- Synchronize identity information across business systems
- Build more accurate reporting and customer insights

This capability forms the foundation for customer-level analytics and user-centric reporting within Fullstory.



## Do Not Use This API When

Do not use this endpoint for:

- Anonymous visitors
- Session-level tracking
- Temporary identifiers
- High-volume transient records



## Minimum Valid Request

```json
{
  "uid": "user-123"
}



# Endpoint

```http
POST /v2/users
```

Base URL:

```http
https://api.fullstory.com
```



# Authentication

Include your API key in the `Authorization` header.

```http
Authorization: Bearer <API_KEY>
```

Required permission level:

* Standard

## Authentication Failure Example

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or missing API key."
  }
}
```



# Quick Start

## Request Example

```bash
curl --request POST \
  --url https://api.fullstory.com/v2/users \
  --header 'Authorization: Bearer <API_KEY>' \
  --header 'Content-Type: application/json' \
  --header 'Idempotency-Key: user-sync-1024' \
  --data '{
    "uid": "xyz123",
    "display_name": "Daniel Falko",
    "email": "daniel.falko@example.com",
    "properties": {
      "pricing_plan": "paid",
      "popup_help": true,
      "total_spent": 14.55
    }
  }'
```

## Success Response

```json
{
  "id": "987654321"
}
```



# Request Headers

| Header          | Required | Description                              |
| --------------- | -------- | ---------------------------------------- |
| Authorization   | Yes      | API key used to authenticate the request |
| Content-Type    | Yes      | Must be `application/json`               |
| Idempotency-Key | No       | Makes the request safely retryable       |



# Request Body

```json
{
  "uid": "xyz123",
  "display_name": "Daniel Falko",
  "email": "daniel.falko@example.com",
  "properties": {
    "pricing_plan": "paid",
    "popup_help": true,
    "total_spent": 14.55
  }
}
```



# Request Fields

| Field        | Type   | Description                                  |
| ------------ | ------ | -------------------------------------------- |
| uid          | string | Application-specific identifier for the user |
| display_name | string | Human-readable name displayed in Fullstory   |
| email        | string | User email address                           |
| properties   | object | Additional metadata associated with the user |



# Field Constraints

| Field             | Constraint             |
| ----------------- | ---------------------- |
| uid               | Maximum 256 characters |
| display_name      | Maximum 256 characters |
| email             | Maximum 128 characters |
| Property name     | Maximum 512 characters |
| Property value    | Maximum 8192 bytes     |
| Unique properties | Maximum 500            |



# Property Naming Rules

Property names:

* Must contain only letters, numbers, and underscores (`_`)
* Must begin with a letter
* Support nested objects
* Must remain within length limits, including nested property paths

## Example

```json
{
  "properties": {
    "subscription_plan": "enterprise",
    "company": {
      "name": "Acme"
    }
  }
}
```



# Idempotency

This endpoint supports idempotent requests.

Use the `Idempotency-Key` header when retrying requests to prevent accidental duplicate operations.

Recommended for:

* Network interruptions
* Client-side timeouts
* Background synchronization jobs
* Distributed systems

## Example

```http
Idempotency-Key: user-sync-1024
```

When the same request is retried using the same idempotency key:

* Fullstory processes the request only once.
* Subsequent retries return the original result.



# Verify User Creation

After a successful request, use the returned `id` with the Get User endpoint to verify the user record.

You can also:

* Retrieve the user using the Get User endpoint.
* Search for the user using the List Users endpoint.
* Access the user's profile card through the returned `app_url`.



# User Lifecycle

The following sequence occurs after a successful request:

1. User record is created or updated.
2. User becomes retrievable through the Get User API.
3. Sessions and events become associated with the identified user.
4. User becomes searchable once activity exists.

## Important

A user will not immediately appear in search results or analytics reports until associated activity exists, such as:

* Sessions
* Events

This behavior is expected and does not indicate a failed request.



# Common Errors

| Status Code | Description             | Recommended Action                      |
| ----------- | ----------------------- | --------------------------------------- |
| 400         | Invalid request payload | Verify field values and constraints     |
| 401         | Authentication failed   | Verify API key and authorization header |
| 429         | Rate limit exceeded     | Retry using exponential backoff         |
| 500         | Internal server error   | Retry the request later                 |

## Example Validation Error

```json
{
  "error": {
    "code": "INVALID_EMAIL",
    "message": "The email format is invalid."
  }
}
```



# Retry Guidance

If a request fails due to a temporary issue:

1. Retry the request using the same `Idempotency-Key`.
2. Apply exponential backoff between retries.
3. Avoid generating a new idempotency key during retry attempts.

This approach prevents duplicate user creation and improves reliability.



# Python Example

```python
import requests
import json

url = "https://api.fullstory.com/v2/users"

payload = json.dumps({
    "uid": "xyz123",
    "display_name": "Daniel Falko",
    "email": "daniel.falko@example.com",
    "properties": {
        "pricing_plan": "paid",
        "popup_help": True,
        "total_spent": 14.55
    }
})

headers = {
    "Authorization": "Bearer <API_KEY>",
    "Content-Type": "application/json",
    "Idempotency-Key": "user-sync-1024"
}

response = requests.post(
    url,
    headers=headers,
    data=payload
)

print(response.status_code)
print(response.json())
```



# Common Use Cases

* Sync customer records from a CRM
* Associate authenticated users with Fullstory sessions
* Store customer subscription details
* Enrich user profiles with application metadata
* Link backend identity systems with analytics reporting



# Related APIs

* Get User
* List Users
* Update User
* Delete User



# Best Practices

* Use stable user identifiers for `uid`.
* Validate email addresses before submission.
* Use idempotency keys for retryable requests.
* Avoid sending anonymous user records.
* Include meaningful metadata in `properties`.
* Monitor rate limits in production environments.
* Verify user creation through Get User when required.

