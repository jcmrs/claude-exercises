# API Conventions

## 1. URL Structure
- Base URL: `https://api.example.com/v1/`
- Use nouns for endpoints: `/users`, `/orders`
- Use plural nouns for collections: `/products`

## 2. HTTP Methods
- GET: Retrieve data
- POST: Create new resources
- PUT: Update existing resources
- DELETE: Remove resources

## 3. Request and Response Format
- Use JSON format for requests and responses.
- Content-Type: `application/json`

## 4. Status Codes
- 200 OK: Success
- 201 Created: Resource created
- 204 No Content: Successful deletion
- 400 Bad Request: Invalid request
- 404 Not Found: Resource not found
- 500 Internal Server Error: Unexpected error

## 5. Versioning
- Use versioning in the URL: `/v1/users`

## 6. Authentication
- Use Bearer tokens in Authorization header: `Authorization: Bearer <token>`

## 7. Pagination
- Use query parameters for pagination: `?page=1&limit=20`

## 8. Filtering and Sorting
- Use query parameters for filtering: `?status=active`
- Use query parameters for sorting: `?sort=created_at`