# 📚 Book Management System – Django REST API with JWT Authentication

This project is a secure RESTful API built with Django and Django REST Framework. It allows authenticated users to perform full CRUD operations on a collection of books using JWT (JSON Web Token) authentication.

---

## 🚀 Features

- 🔐 JWT-based authentication with access/refresh tokens
- 📖 CRUD operations: Create, Read, Update, Delete books
- ✅ Protected endpoints (only accessible to authenticated users)
- 🧪 Easily testable with Postman or cURL

---

## 🛠 Technologies Used

- Python
- Django
- Django REST Framework
- djangorestframework-simplejwt
- SQLite (default DB)

---

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone <your-repo-link>
cd your-project-directory
```

### 2. Create and Activate a Virtual Environment

```bash
python -m venv .venv
source .venv/bin/activate       # On Windows: .venv\Scripts\activate
```

### 3. Install Required Packages

```bash
pip install django djangorestframework djangorestframework-simplejwt
```

### 4. Start Django Project and App

```bash
django-admin startproject config .
django-admin startapp book
```

### 5. Define the Book Model

In `book/models.py`:

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    description = models.TextField()
    price = models.DecimalField(max_digits=6, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

### 6. Apply Migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

### 7. Create a Serializer

In `book/serializers.py`:

```python
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
```

### 8. Create API Views

In `book/views.py`:

```python
from rest_framework import generics, permissions
from .models import Book
from .serializers import BookSerializer

class BookListCreateView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [permissions.IsAuthenticated]

class BookRetrieveUpdateDestroyView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [permissions.IsAuthenticated]
```

### 9. Configure URLs

In `book/urls.py`:

```python
from django.urls import path
from .views import BookListCreateView, BookRetrieveUpdateDestroyView

urlpatterns = [
    path('books/', BookListCreateView.as_view(), name='book-list-create'),
    path('books/<int:pk>/', BookRetrieveUpdateDestroyView.as_view(), name='book-detail'),
]
```

In `config/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('book.urls')),
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

### 10. Configure Settings

In `config/settings.py`, ensure the following is added:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'book',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    )
}
```

### 11. Run the Development Server

```bash
python manage.py runserver
```

The API will be accessible at `http://localhost:8000`.

---

## 🔐 Authentication (JWT)

### Get Token

Make a `POST` request to `/api/token/` with credentials:

```json
{
  "username": "yourusername",
  "password": "yourpassword"
}
```

### Use Token

Add the token in your request headers:

```makefile
Authorization: Bearer <your_access_token>
```

---

## 📮 API Endpoints

| Method | Endpoint             | Description           |
|--------|----------------------|-----------------------|
| GET    | `/api/books/`        | List all books        |
| POST   | `/api/books/`        | Create a new book     |
| GET    | `/api/books/<id>/`   | Get book details      |
| PUT    | `/api/books/<id>/`   | Update book details   |
| DELETE | `/api/books/<id>/`   | Delete a book         |

---

## 🧪 Testing

Use Postman, cURL, or any REST client to test the API. Example:

```bash
curl -H "Authorization: Bearer <token>" http://localhost:8000/api/books/
```

---

## 👨‍💻 Author

Your Name – [your.email@example.com]

---

## 📄 License

This project is licensed under the MIT License.