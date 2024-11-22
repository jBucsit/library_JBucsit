# Library API Documentation

## Description
This system is a web API application built with the Slim Framework and uses JWT (JSON Web Token) for authentication and authorization. The API allows users to manage a library database with the following core functionality:

- **User Management**: Registration, authentication, profile management, and account deletion.
- **Author Management**: Add, update, view, and delete authors.
- **Book Management**: Add, update, view, and delete books.
- **Book-Author Relationships**: Create, update, delete, and view relationships between books and authors.

## Tools and Software Used

- **PHP**: A robust server-side scripting language for developing dynamic and secure APIs.
- **Slim Framework**: A lightweight PHP framework for building RESTful web services.
- **JWT (JSON Web Token)**: For stateless and secure authentication and authorization.
- **MySQL**: A relational database for managing users, authors, books, and their relationships.
- **JSON**: A lightweight data format for communication between clients and the server.

## Features

### Authentication and Security
- **JWT Authentication**: The API uses JWT to authenticate and authorize users.
- **Token Expiry and Rotation**: Tokens expire after 1 hour and new tokens are generated after each successful operation.
- **Middleware Protection**: All routes are protected by JWT middleware, ensuring that only authorized users can access certain endpoints.
- **Used Token Management**: Tokens are invalidated after use via `$_SESSION['used_tokens']`.

### Core Functionalities

#### User Management
- **User Registration**: Users can register with a username and password.
- **User Authentication**: Users can log in to get a JWT token for authentication.
- **User Profile Management**: Users can view and update their profiles.
- **Delete Users**: Users can delete their account, including associated data.

#### Author Management
- **Add Author**: Allows adding a new author to the database.
- **Update Author**: Allows updating an author's information.
- **View Authors**: Fetch a list of all authors.
- **Delete Author**: Remove an author from the database.

#### Book Management
- **Add Book**: Add a new book to the system, associating it with an author.
- **Update Book**: Update the details of a specific book.
- **View Books**: View a list of all books in the library.
- **Delete Book**: Remove a book from the database.

#### Book-Author Relationships
- **Create Book-Author Relationship**: Link books to authors in the `book_authors` table.
- **Update Book-Author Relationship**: Update the relationship between a book and an author.
- **Delete Book-Author Relationship**: Remove a specific relationship between a book and an author.
- **View Book-Author Relationships**: Retrieve the current relationships between books and authors.

## Authentication Flow

### JWT Authentication
- All sensitive routes (e.g., create, update, delete operations) are protected using JWT tokens.
- The token must be included in the `Authorization` header with the prefix `Bearer` (e.g., `Authorization: Bearer <token>`).
- A valid token is required for making requests to all protected endpoints.

### Token Management
- **Token Expiration**: JWT tokens expire after one hour from issuance.
- **Token Rotation**: After each successful operation, a new JWT token is issued to replace the old one.
- **Used Tokens**: Invalid tokens are tracked via `$_SESSION['used_tokens']` to prevent re-use.

---
## API Endpoints

### 1a. User Authentication or Login
- **Endpoint:** `/user/authenticate`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows users to authenticate by providing their username and password. Upon successful authentication, a JWT token is generated and returned.

- **Authentication:**  
  No authentication is required to access this endpoint. However, the endpoint will provide a JWT token if the authentication is successful.

- **Request Body (JSON):**
    ```json
    {
        "username": "<Username>",
        "password": "<Password>"
    }
    ```

- **Response:**
    - **On Success (Authentication Successful):**
        ```json
        {
            "status": "success",
            "token": "<JWT_Token>",
            "data": null
        }
        ```

    - **On Failure (Authentication Failed):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "Authentication Failed!"
            }
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 2a. Create New User
- **Endpoint:** `/user/register`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows the creation of a new user by providing a username and password. The username and password must be at least 5 characters long.

- **Authentication:**  
  No authentication is required to access this endpoint.

- **Request Body (JSON):**
    ```json
    {
        "username": "<Username>",
        "password": "<Password>"
    }
    ```

- **Response:**
    - **On Success (User Created):**
        ```json
        {
            "status": "success",
            "data": null
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Username and password must be at least 5 characters long and not empty"
        }
        ```

    - **On Failure (Username Exists):**
        ```json
        {
            "status": "fail",
            "data": "Username already exists"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 3a. View User
- **Endpoint:** `/user/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint retrieves a list of users from the database. The request must be authenticated with a valid JWT token.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body:**  
  No request body is needed for this endpoint.

- **Response:**
    - **On Success (Users Retrieved):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "userid": "<User_ID>",
                    "username": "<Username>"
                },
                ...
            ]
        }
        ```

    - **On Failure (No Users Found):**
        ```json
        {
            "status": "fail",
            "data": "No users found"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---
### 4a. Update a User
- **Endpoint:** `/user/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows updating a user's details by providing the user ID, new username, and new password. The username and password must be at least 5 characters long. The request must be authenticated with a valid JWT token.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "userid": "<User_ID>",
        "username": "<New_Username>",
        "password": "<New_Password>"
    }
    ```

- **Response:**
    - **On Success (User Updated):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "User updated successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Username and password must be at least 5 characters long and not empty"
        }
        ```

    - **On Failure (User Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "User with ID <User_ID> does not exist."
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 5a. Delete a User
- **Endpoint:** `/user/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This API endpoint allows the deletion of a user by providing the user ID. The request must be authenticated with a valid JWT token.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "userid": "<User_ID>"
    }
    ```

- **Response:**
    - **On Success (User Deleted):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "User deleted successfully"
        }
        ```

    - **On Failure (User ID Not Provided):**
        ```json
        {
            "status": "fail",
            "data": "User ID must be provided."
        }
        ```

    - **On Failure (User Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "User with ID <User_ID> does not exist."
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 1b. Create a New Author
- **Endpoint:** `/authors/create`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows creating a new author by providing the author's name. The name must be more than 4 characters long.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "name": "<Author_Name>"
    }
    ```

- **Response:**
    - **On Success (Author Created):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Author created successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Author name must be more than 4 characters and cannot be blank."
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 2b. Update Author Information
- **Endpoint:** `/authors/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows updating an author's information by providing the author ID and new name. The name must be more than 4 characters long. 

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "authorid": "<Author_ID>",
        "name": "<New_Name>"
    }
    ```

- **Response:**
    - **On Success (Author Updated):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Author updated successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Author name must be more than 4 characters and cannot be blank."
        }
        ```

    - **On Failure (Author Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Author with ID <Author_ID> does not exist."
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 3b. Get All Authors
- **Endpoint:** `/authors/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint retrieves a list of all authors from the database.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body:**  
  No request body is needed for this endpoint.

- **Response:**
    - **On Success (Authors Retrieved):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "authorid": "<Author_ID>",
                    "name": "<Author_Name>"
                },
                ...
            ]
        }
        ```

    - **On Failure (No Authors Found):**
        ```json
        {
            "status": "fail",
            "data": "No authors found"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 4b. Delete an Author
- **Endpoint:** `/authors/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This API endpoint allows the deletion of an author by providing the author ID.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "authorid": "<Author_ID>"
    }
    ```

- **Response:**
    - **On Success (Author Deleted):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Author deleted successfully"
        }
        ```

    - **On Failure (Author ID Not Provided):**
        ```json
        {
            "status": "fail",
            "data": "Author ID must be provided."
        }
        ```

    - **On Failure (Author Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Author with ID <Author_ID> does not exist."
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---


### 1c. Create a New Book
- **Endpoint:** `/books/create`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows creating a new book by providing the book's title and author ID. The title must be more than 4 characters long.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "title": "<Book_Title>",
        "authorid": "<Author_ID>"
    }
    ```

- **Response:**
    - **On Success (Book Created):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book created successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Book title must be more than 4 characters and cannot be blank."
        }
        ```

    - **On Failure (Author Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Author not found"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 2c. Get All Books
- **Endpoint:** `/books/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint retrieves a list of all books from the database along with their authors.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body:**  
  No request body is needed for this endpoint.

- **Response:**
    - **On Success (Books Retrieved):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "bookid": "<Book_ID>",
                    "title": "<Book_Title>",
                    "authorid": "<Author_ID>",
                    "author_name": "<Author_Name>"
                },
                ...
            ]
        }
        ```

    - **On Failure (No Books Found):**
        ```json
        {
            "status": "fail",
            "data": "No books found"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 3c. Update Book Information
- **Endpoint:** `/books/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows updating a book's information by providing the book ID, new title, and new author ID. The title must be more than 4 characters long.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "bookid": "<Book_ID>",
        "title": "<New_Title>",
        "authorid": "<New_Author_ID>"
    }
    ```

- **Response:**
    - **On Success (Book Updated):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book updated successfully"
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Book title must be more than 4 characters and cannot be blank."
        }
        ```

    - **On Failure (Book Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Book with ID <Book_ID> does not exist."
        }
        ```

    - **On Failure (Author Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Author not found"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### 4c. Delete a Book
- **Endpoint:** `/books/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This API endpoint allows the deletion of a book by providing the book ID.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "bookid": "<Book_ID>"
    }
    ```

- **Response:**
    - **On Success (Book Deleted):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book deleted successfully"
        }
        ```

    - **On Failure (Book Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Book with ID <Book_ID> does not exist."
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---





### Create a New Book-Author Relationship
- **Endpoint:** `/book_authors/create`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows creating a new relationship between a book and an author by providing the book ID and author ID.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "bookid": "<Book_ID>",
        "authorid": "<Author_ID>"
    }
    ```

- **Response:**
    - **On Success (Relationship Created):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book-author relationship created successfully"
        }
        ```

    - **On Failure (Book Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Book does not exist"
        }
        ```

    - **On Failure (Author Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Author does not exist"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### Get All Book-Author Relationships
- **Endpoint:** `/book_authors/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint retrieves a list of all book-author relationships from the database, including details of the books and authors.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body:**  
  No request body is needed for this endpoint.

- **Response:**
    - **On Success (Relationships Retrieved):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "collectionid": "<Collection_ID>",
                    "bookid": "<Book_ID>",
                    "authorid": "<Author_ID>",
                    "book_title": "<Book_Title>",
                    "author_name": "<Author_Name>"
                },
                ...
            ]
        }
        ```

    - **On Failure (No Relationships Found):**
        ```json
        {
            "status": "fail",
            "data": "No book-author relationships found"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### Update Book-Author Relationship
- **Endpoint:** `/book_authors/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows updating an existing book-author relationship by providing the collection ID, new book ID, and new author ID.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "collectionid": "<Collection_ID>",
        "bookid": "<New_Book_ID>",
        "authorid": "<New_Author_ID>"
    }
    ```

- **Response:**
    - **On Success (Relationship Updated):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book-author relationship updated successfully"
        }
        ```

    - **On Failure (Book Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Book does not exist"
        }
        ```

    - **On Failure (Author Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "Author does not exist"
        }
        ```

    - **On Failure (Foreign Key Constraint Violation):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "Foreign key constraint violation",
                "details": "<Error_Details>"
            }
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

### Delete a Book-Author Relationship
- **Endpoint:** `/book_authors/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This API endpoint allows deleting an existing book-author relationship by providing the collection ID.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "collectionid": "<Collection_ID>"
    }
    ```

- **Response:**
    - **On Success (Relationship Deleted):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book-author relationship deleted successfully"
        }
        ```

    - **On Failure (Server Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```

---

