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

  
### 1A. User Authentication
  - **Endpoint:** `/user/authenticate`  
  - **Method:** `POST`  
  - **Description:** 
      Authenticates a user by verifying the provided username and password against the `users` table in the database. If the credentials are valid, a JWT token is generated and returned to the user.
  - **Sample Request(JSON):**
      ```json
          {
            "username": "user123",
            "password": "mypassword"
          }
      ```
  - **Response:**
      - **On Success**
          ```json
              {
                  "status": "success",
                  "token": "<JWT_TOKEN>",
                  "data": null
              }
          ```
      - **On Failure (Authenthication Failed):**
          ```json
              {
                  "status": "fail",
                  "data": {
                      "title": "Authentication Failed!"
                  }
              }
          ```
---
### 2A. Register a New User
- **Endpoint:** `/user/register`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows users to register by providing a `username` and `password`. The system validates the input, ensuring that the username is unique and that both the username and password meet the minimum length requirement of 5 characters. The credentials are securely stored in the database with the password hashed using SHA-256.

- **Sample Request (JSON):**
    ```json
    {
        "username": "example_user",
        "password": "mypassword"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "data": null
        }
        ```

    - **On Failure (Invalid Input):**
        ```json
        {
            "status": "fail",
            "data": "Username and password must be at least 5 characters long and not empty"
        }
        ```

    - **On Failure (Username Already Exists):**
        ```json
        {
            "status": "fail",
            "data": "Username already exists"
        }
        ```
---
### 3A. View All Users
- **Endpoint:** `/user/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint retrieves a list of all users from the system, displaying their `userid` and `username`. This operation is protected by a JWT token and only accessible to authenticated users. The server returns a new JWT token upon successful request, which can be used for subsequent API requests.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Sample Request:**
    - **Headers:**
      ```json

      ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "userid": 1,
                    "username": "user1"
                },
                {
                    "userid": 2,
                    "username": "user2"
                }
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
---
### 4A. Update User
- **Endpoint:** `/user/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows a user to update their `username` and `password`. The request payload should contain the user's ID, new username, and new password. The operation is protected by a JWT token, which will be rotated and returned upon successful request.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "userid": "user_id",
        "username": "new_username",
        "password": "new_password"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "User updated successfully"
        }
        ```

    - **On Failure (User Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "User with ID <user_id> does not exist."
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "Username and password must be at least 5 characters long and not empty"
        }
        ```
---
### Delete User
- **Endpoint:** `/user/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This API endpoint allows an administrator or a valid user to delete their account from the system. The request payload must include the `userid` of the user to be deleted. The operation is protected by a JWT token, which will be rotated and returned upon successful request.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "userid": "user_id"
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "User deleted successfully"
        }
        ```

    - **On Failure (User Does Not Exist):**
        ```json
        {
            "status": "fail",
            "data": "User with ID <user_id> does not exist."
        }
        ```

    - **On Failure (Validation Error):**
        ```json
        {
            "status": "fail",
            "data": "User ID must be provided."
        }
        ```
---
### 1B. Create a New Author
- **Endpoint:** `/authors/create`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows the creation of a new author in the system. The request payload must include the `name` of the author. The author name must be at least 5 characters long. The operation is protected by a JWT token, which will be rotated and returned upon successful request.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "name": "author_name"
    }
    ```

- **Response:**
    - **On Success:**
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
---
### 2B. Update Author Information
- **Endpoint:** `/authors/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows updating the information of an existing author. The request payload must include the `authorid` (the ID of the author to be updated) and the new `name` of the author. The name must be at least 5 characters long. The operation is protected by a JWT token, which will be rotated and returned upon a successful request.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "authorid": "author_id",
        "name": "new_author_name"
    }
    ```

- **Response:**
    - **On Success:**
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

    - **On Failure (Author Not Found):**
        ```json
        {
            "status": "fail",
            "data": "Author with ID <author_id> does not exist."
        }
        ```
---
### 3B. Get All Authors
- **Endpoint:** `/authors/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint retrieves a list of all authors from the database, including their `authorid` and `name`. The operation is protected by a JWT token, and the response will include a new token as well as the list of authors.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json

    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "authorid": 1,
                    "name": "Author Name 1"
                },
                {
                    "authorid": 2,
                    "name": "Author Name 2"
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
---
### 4B. Delete an Author
- **Endpoint:** `/authors/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This API endpoint allows for the deletion of an author from the database. The request requires the `authorid` of the author to be deleted. The operation is protected by a JWT token, and the response will include a new token as well as a confirmation message.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
      "authorid": 1
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Author deleted successfully"
        }
        ```

    - **On Failure (No Author ID Provided):**
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
            "data": "Author with ID <authorId> does not exist."
        }
        ```
---
### 1C. Create a New Book
- **Endpoint:** `/books/create`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows for creating a new book record in the database. The request requires the `title` of the book and the `authorid` (the ID of the author) to associate the book with. The operation is protected by a JWT token, and the response will include a new token as well as a confirmation message.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "title": "The Great Gatsby",
        "authorid": 1
    }
    ```

- **Response:**
    - **On Success:**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book created successfully"
        }
        ```

    - **On Failure (Book Title Invalid):**
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
---
### 2C. Get All Books
- **Endpoint:** `/books/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint retrieves a list of all books from the database, along with the name of their respective authors. It is protected by JWT token authentication and will return a token upon successful request.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request:**
    - **Headers:**
      ```json

      ```

- **Response:**
    - **On Success (Books Retrieved):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "bookid": 1,
                    "title": "To Kill a Mockingbird",
                    "authorid": 1,
                    "author_name": "Harper Lee"
                },
                {
                    "bookid": 2,
                    "title": "1984",
                    "authorid": 2,
                    "author_name": "George Orwell"
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
---



### 3C. Update Book Information
- **Endpoint:** `/books/update`  
- **Method:** `PUT`  
- **Description:** Allows updating a book's information by providing the book ID, new title, and new author ID. The title must be more than 4 characters long. Requires a valid JWT token.
- **Authentication:** JWT token required.
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

### 4C. Delete a Book
- **Endpoint:** `/books/delete`  
- **Method:** `DELETE`  
- **Description:** Allows the deletion of a book by providing the book ID. Requires a valid JWT token.
- **Authentication:** JWT token required.
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


### 1D. Create a New Book-Author Relationship
- **Endpoint:** `/book_authors/create`  
- **Method:** `POST`  
- **Description:**  
  This API endpoint allows the creation of a new book-author relationship. The request requires a `bookid` and an `authorid`, which link a book to an author in the system. It is protected by a JWT token, which will be rotated and returned upon successful request.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "bookid": "book_id",
        "authorid": "author_id"
    }
    ```

- **Response:**
    - **On Success (Book-Author Relationship Created):**
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

    - **On Failure (Foreign Key Constraint Violation):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "Foreign key constraint violation",
                "details": "Error message from the database"
            }
        }
        ```
---
### 2D. Get All Book-Author Relationships
- **Endpoint:** `/book_authors/read`  
- **Method:** `GET`  
- **Description:**  
  This API endpoint fetches all book-author relationships from the database, including details about the books and authors associated with each relationship. It returns a list of these relationships along with the book title and author name.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The JWT token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
 
    ```

- **Response:**
    - **On Success (Book-Author Relationships Found):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": [
                {
                    "collectionid": "1",
                    "bookid": "1",
                    "authorid": "2",
                    "book_title": "Book Title",
                    "author_name": "Author Name"
                },
                {
                    "collectionid": "2",
                    "bookid": "3",
                    "authorid": "1",
                    "book_title": "Another Book",
                    "author_name": "Another Author"
                }
            ]
        }
        ```

    - **On Failure (No Book-Author Relationships Found):**
        ```json
        {
            "status": "fail",
            "data": "No book-author relationships found"
        }
        ```
---
### 3D. Update Book-Author Relationship
- **Endpoint:** `/book_authors/update`  
- **Method:** `PUT`  
- **Description:**  
  This API endpoint allows you to update the relationship between a book and an author in the `book_authors` table. The request requires the `collectionid`, `bookid`, and `authorid` in the request body. The relationship between the specified book and author will be updated for the given `collectionid`.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "collectionid": "collection_id",
        "bookid": "book_id",
        "authorid": "author_id"
    }
    ```

- **Response:**
    - **On Success (Book-Author Relationship Updated):**
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
                "details": "<Error_Message>"
            }
        }
        ```
---
### 4D. Delete Book-Author Relationship
- **Endpoint:** `/book_authors/delete`  
- **Method:** `DELETE`  
- **Description:**  
  This API endpoint allows you to delete an existing relationship between a book and an author in the `book_authors` table. The relationship to be deleted is identified by the `collectionid`. Once deleted, the specified book-author relationship will be removed from the database.

- **Authentication:**  
  This endpoint requires a valid JWT token for access. The token must be passed in the `Authorization` header as a Bearer token.

- **Request Body (JSON):**
    ```json
    {
        "collectionid": "collection_id"
    }
    ```

- **Response:**
    - **On Success (Book-Author Relationship Deleted):**
        ```json
        {
            "status": "success",
            "token": "<New_JWT_Token>",
            "data": "Book-author relationship deleted successfully"
        }
        ```

    - **On Failure (Database Error):**
        ```json
        {
            "status": "fail",
            "data": {
                "title": "<Error_Message>"
            }
        }
        ```
---
