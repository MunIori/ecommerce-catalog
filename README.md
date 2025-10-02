# E-commerce Product Catalog

Backend challenge built with **Django REST Framework**.

## 📝 Assumptions & Design Decisions

- Used Django’s default User model for simplicity.
- JWT chosen for stateless authentication (via `djangorestframework-simplejwt`).
- Blacklist enabled for logout functionality.
- Products require an existing category.
- Pagination not implemented (not required), but DRF can be extended easily.

## 🚀 Project Setup

### ⚙️ Requirements

- Python 3.11+
- pip + virtualenv

### 💻 Instalattion

```bash
git clone https://github.com/MunIori/ecommerce-catalog.git
cd ecommerce-catalog
python -m venv venv
source venv\bin\activate # en Windows: venv\Scripts\activate
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver

## 📖 API Documentation

Interactive documentation is available for easy testing and exploration of the API endpoints:

- **Swagger UI**: [http://127.0.0.1:8000/swagger/](http://127.0.0.1:8000/swagger/)  
  Provides an interactive interface to test endpoints and see request/response examples.

- **Redoc**: [http://127.0.0.1:8000/redoc/](http://127.0.0.1:8000/redoc/)  
  Provides a clean, structured view of all endpoints, models, and schemas.

## 🔑 Authentication Endpoints

| Action          | Endpoint                    | Notes                       |
|-----------------|-----------------------------|-----------------------------|
| Register        | `POST /api/register/`       | Create a new user           |
| Login (JWT)     | `POST /api/login/`          | Returns access & refresh tokens |
| Refresh token   | `POST /api/token/refresh/`  | Get a new access token      |
| Logout          | `POST /api/logout/`         | Blacklists refresh token    |

## 🗂️ Catalog Models

### Category

| Field      | Type        | Description                       |
|------------|-------------|-----------------------------------|
| name       | CharField   | Unique name of the category        |
| description| TextField   | Optional description               |
| slug       | SlugField   | Unique slug generated from name    |
| created_at | DateTimeField | Timestamp when created           |
| updated_at | DateTimeField | Timestamp when updated           |

### Product

| Field      | Type          | Description                                     |
|------------|---------------|-------------------------------------------------|
| name       | CharField     | Name of the product                             |
| description| TextField     | Optional description                            |
| price      | DecimalField  | Must be a positive number                       |
| stock      | PositiveIntegerField | Available stock                             |
| category   | ForeignKey (nullable) | Related category (kept as null if category deleted) |
| created_at | DateTimeField | Timestamp when created                          |
| updated_at | DateTimeField | Timestamp when updated                          |

## 📦 Catalog Endpoints

### Categories

| Method    | Endpoint                 | Notes      |
|-----------|--------------------------|------------|
| GET       | `/api/categories/`       | Public     |
| POST      | `/api/categories/`       | Admin only |
| GET       | `/api/categories/{id}/`  | Public     |
| PUT/PATCH | `/api/categories/{id}/`  | Admin only |
| DELETE    | `/api/categories/{id}/`  | Admin only |

### Products

| Method    | Endpoint                 | Notes                                           |
|-----------|--------------------------|-------------------------------------------------|
| GET       | `/api/products/`         | Public. Supports `?category=<id>` and `?search=<term>` |
| POST      | `/api/products/`         | Admin only                                      |
| GET       | `/api/products/{id}/`    | Public                                          |
| PUT/PATCH | `/api/products/{id}/`    | Admin only                                      |
| DELETE    | `/api/products/{id}/`    | Admin only                                      |

#### Notes on Product Serializer
When creating/updating a product:
- Use `category_id` (write-only) to assign the category.
- The response will include the full `category` object (read-only).

**Example request:**
```json
{
  "name": "Mouse Gamer",
  "price": 120.50,
  "stock": 15,
  "category_id": 1
}
```

**Example response:**
```
{
  "id": 1,
  "name": "Mouse Gamer",
  "price": "120.50",
  "stock": 15,
  "category": {
    "id": 1,
    "name": "Electronics",
    "slug": "electronics"
  },
  "created_at": "2025-10-01T22:40:00Z"
}
```