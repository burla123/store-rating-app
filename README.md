# ⭐ RateMyStore — Full-Stack Store Rating Platform

A complete full-stack web application where users can submit ratings (1–5) for registered stores. Built with **Express.js**, **PostgreSQL** (via Sequelize ORM), and **React.js**.

---

## 🧱 Tech Stack

| Layer     | Technology                          |
|-----------|-------------------------------------|
| Backend   | Node.js + Express.js                |
| Database  | PostgreSQL + Sequelize ORM          |
| Frontend  | React.js + React Router v6          |
| Auth      | JWT (JSON Web Tokens) + bcryptjs    |
| Styling   | Custom CSS (Syne + DM Sans fonts)   |

---

## 👥 User Roles & Features

### System Administrator
- Dashboard: total users, stores, ratings
- Add/view users (admin, normal, store owner) with filters & sorting
- Add/view stores with filters & sorting
- View user detail (store owners show their store's average rating)
- Logout

### Normal User
- Register & login
- Browse all stores (search by name & address)
- Submit ratings (1–5 stars) for any store
- Modify previously submitted ratings
- Update password
- Logout

### Store Owner
- Login
- Dashboard: view all submitted ratings, average rating
- Update password
- Logout

---

## 📋 Form Validations

| Field    | Rule                                                        |
|----------|-------------------------------------------------------------|
| Name     | Min 20 characters, Max 60 characters                        |
| Address  | Max 400 characters                                          |
| Password | 8–16 chars, at least 1 uppercase letter, 1 special character |
| Email    | Standard email format                                        |

---

## 🚀 Getting Started

### Prerequisites
- Node.js v18+
- PostgreSQL 14+
- npm or yarn

---

### Option A: Manual Setup

#### 1. Clone the repo
```bash
git clone https://github.com/YOUR_USERNAME/store-rating-app.git
cd store-rating-app
```

#### 2. Setup the Database
Create a PostgreSQL database:
```sql
CREATE DATABASE store_rating_db;
```

#### 3. Configure Backend
```bash
cd backend
cp .env.example .env
# Edit .env with your DB credentials and JWT secret
npm install
npm run dev
```

The server auto-syncs Sequelize models and seeds a default admin on first run.

**Default Admin Credentials:**
```
Email:    admin@storerating.com
Password: Admin@1234
```

#### 4. Configure Frontend
```bash
cd ../frontend
cp .env.example .env
# Edit REACT_APP_API_URL if backend isn't on localhost:5000
npm install
npm start
```

App runs at: `http://localhost:3000`

---

### Option B: Docker Compose (Recommended)

```bash
git clone https://github.com/YOUR_USERNAME/store-rating-app.git
cd store-rating-app
docker-compose up --build
```

- Frontend: http://localhost:3000
- Backend API: http://localhost:5000/api
- PostgreSQL: localhost:5432

---

## 📁 Project Structure

```
store-rating-app/
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   └── database.js          # Sequelize connection
│   │   ├── controllers/
│   │   │   ├── authController.js    # Register, login, change password
│   │   │   ├── adminController.js   # Dashboard, users, stores (admin)
│   │   │   ├── storeController.js   # Store browsing & rating (user)
│   │   │   └── ownerController.js   # Store owner dashboard
│   │   ├── middleware/
│   │   │   └── auth.js              # JWT authenticate + role authorize
│   │   ├── models/
│   │   │   ├── User.js
│   │   │   ├── Store.js
│   │   │   ├── Rating.js
│   │   │   └── index.js             # Associations
│   │   ├── routes/
│   │   │   ├── auth.js
│   │   │   ├── admin.js
│   │   │   └── stores.js
│   │   └── index.js                 # App entry point + seeder
│   ├── Dockerfile
│   ├── .env.example
│   └── package.json
│
├── frontend/
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── Sidebar.jsx          # Role-based navigation
│   │   │   ├── ProtectedRoute.jsx   # Auth + role guard
│   │   │   └── StarRating.jsx       # Interactive star widget
│   │   ├── context/
│   │   │   └── AuthContext.jsx      # Global auth state
│   │   ├── pages/
│   │   │   ├── Login.jsx
│   │   │   ├── Register.jsx
│   │   │   ├── AdminDashboard.jsx
│   │   │   ├── AdminUsers.jsx
│   │   │   ├── AdminStores.jsx
│   │   │   ├── UserStores.jsx
│   │   │   ├── OwnerDashboard.jsx
│   │   │   └── Profile.jsx
│   │   ├── styles/
│   │   │   └── global.css
│   │   ├── utils/
│   │   │   └── api.js               # Axios instance + interceptors
│   │   ├── App.jsx
│   │   └── index.js
│   ├── Dockerfile
│   ├── .env.example
│   └── package.json
│
├── docker-compose.yml
├── .gitignore
└── README.md
```

---

## 🔌 API Endpoints

### Auth
| Method | Endpoint           | Access | Description         |
|--------|--------------------|--------|---------------------|
| POST   | /api/auth/register | Public | Register normal user|
| POST   | /api/auth/login    | Public | Login all roles     |
| PUT    | /api/auth/password | Any auth | Change password   |

### Admin
| Method | Endpoint              | Access | Description          |
|--------|-----------------------|--------|----------------------|
| GET    | /api/admin/dashboard  | Admin  | Stats counts         |
| GET    | /api/admin/users      | Admin  | List users (filter)  |
| GET    | /api/admin/users/:id  | Admin  | User detail          |
| POST   | /api/admin/users      | Admin  | Create user          |
| GET    | /api/admin/stores     | Admin  | List stores (filter) |
| POST   | /api/admin/stores     | Admin  | Create store         |

### Stores (User)
| Method | Endpoint                      | Access      | Description           |
|--------|-------------------------------|-------------|-----------------------|
| GET    | /api/stores                   | User, Admin | Browse stores         |
| POST   | /api/stores/:storeId/ratings  | User        | Submit/update rating  |
| GET    | /api/stores/my-dashboard      | Store Owner | Owner dashboard       |

---

## 🗄️ Database Schema

```
users          stores          ratings
─────────      ──────────      ────────────
id (PK)        id (PK)         id (PK)
name           name            userId (FK → users)
email          email           storeId (FK → stores)
password       address         rating (1-5)
address        ownerId (FK)    createdAt
role           createdAt       updatedAt
createdAt      updatedAt
updatedAt
```

**Constraints:**
- `ratings(userId, storeId)` → unique composite index (one rating per user per store)
- `users.email` → unique
- `stores.email` → unique

---

## 🔐 Security Features
- Passwords hashed with **bcryptjs** (salt rounds: 12)
- JWT tokens expire in **7 days**
- Role-based route protection on both frontend and backend
- Input validation via **express-validator** (backend) and custom validators (frontend)
- CORS configured for frontend origin only

---

## ⬆️ Push to GitHub

```bash
cd store-rating-app
git init
git add .
git commit -m "feat: initial full-stack store rating platform"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/store-rating-app.git
git push -u origin main
```

---

## 📝 License
MIT
