# e-commerce-website
# ShopEase — Full-Stack E-Commerce Website

A complete, functional full-stack e-commerce application built with **React.js**, **Node.js**, **Express.js**, and **MongoDB**. Users can register, log in, browse/search products, manage a cart, place orders, and track them. Admins can manage products, orders, and view users from a dedicated admin panel.

---

## 1. Technologies Used

- **Frontend:** React.js (Vite), React Router, Axios, plain CSS
- **Backend:** Node.js, Express.js
- **Database:** MongoDB (via Mongoose), hosted on MongoDB Atlas
- **Auth:** JWT (JSON Web Tokens) + bcrypt password hashing
- **API Testing:** Postman
- **Dev Tool:** VS Code

---

## 2. Project Structure

```
ecommerce-project/
├── client/                # React frontend (Vite)
│   ├── src/
│   │   ├── components/    # Navbar, ProductCard, PrivateRoute, AdminRoute, Loader
│   │   ├── pages/         # Home, Login, Register, Products, ProductDetails, Cart, Checkout, MyOrders, admin/*
│   │   ├── context/       # AuthContext, CartContext
│   │   ├── services/      # api.js (Axios instance + endpoint functions)
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   ├── index.html
│   ├── vite.config.js
│   ├── package.json
│   └── .env.example
│
├── server/                 # Express backend
│   ├── models/             # User, Product, Order, Cart (Mongoose schemas)
│   ├── routes/              # authRoutes, productRoutes, cartRoutes, orderRoutes, userRoutes
│   ├── controllers/         # business logic for each route group
│   ├── middleware/          # auth (JWT), admin, errorHandler
│   ├── config/db.js          # MongoDB connection
│   ├── server.js              # app entry point
│   ├── seed.js                  # inserts sample products + admin/test users
│   ├── .env.example
│   └── package.json
│
└── README.md
```

---

## 3. Prerequisites

- [Node.js](https://nodejs.org/) v18+ installed
- A free [MongoDB Atlas](https://www.mongodb.com/cloud/atlas/register) account (or a local MongoDB install)
- [VS Code](https://code.visualstudio.com/) (or any editor)
- [Postman](https://www.postman.com/downloads/) (optional, for API testing)

---

## 4. MongoDB Atlas Setup

1. Go to [https://www.mongodb.com/cloud/atlas/register](https://www.mongodb.com/cloud/atlas/register) and create a free account.
2. Create a new **Cluster** (the free M0 tier is enough).
3. Under **Database Access**, create a database user with a username and password (save these — you'll need them).
4. Under **Network Access**, click **Add IP Address** → **Allow Access from Anywhere** (`0.0.0.0/0`) for local development.
5. Click **Connect** on your cluster → **Drivers** → copy the connection string. It looks like:
   ```
   mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/?retryWrites=true&w=majority
   ```
6. Replace `<username>` and `<password>` with your database user's credentials, and add a database name before the `?`, e.g. `/ecommerce?retryWrites=true`.

---

## 5. Environment Variables

### Backend (`server/.env`)

Create a file named `.env` inside the `server/` folder (copy from `server/.env.example`):

```
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/ecommerce?retryWrites=true&w=majority
JWT_SECRET=any_long_random_secret_string_here
PORT=5000
```

### Frontend (`client/.env`)

Create a file named `.env` inside the `client/` folder (copy from `client/.env.example`):

```
VITE_API_URL=http://localhost:5000/api
```

**Never commit your real `.env` files** — they contain secrets. Only `.env.example` should be committed.

---

## 6. Installation & Running the Project

Open the project folder in VS Code, then open **two terminals** (one for backend, one for frontend).

### Step 1 — Install & start the backend

```bash
cd server
npm install
```

Create your `.env` file as described above, then seed the database with sample products and demo accounts:

```bash
npm run seed
```

You should see output confirming 18 products and two demo accounts were created. Then start the server:

```bash
npm run dev
```

The backend will run at **http://localhost:5000**. You should see:
```
MongoDB connected: cluster0-xxxxx.mongodb.net
Server running on port 5000
```

### Step 2 — Install & start the frontend

In a **new terminal**:

```bash
cd client
npm install
```

Create your `.env` file as described above, then start the dev server:

```bash
npm run dev
```

The frontend will run at **http://localhost:5173**. Open that URL in your browser.

---

## 7. Demo Accounts (created by `npm run seed`)

| Role  | Email               | Password  |
|-------|---------------------|-----------|
| Admin | admin@example.com   | admin123  |
| User  | user@example.com    | user123   |

Log in with the admin account and visit `/admin` to manage products, orders, and users.

---

## 8. API Endpoints

Base URL: `http://localhost:5000/api`

### Auth
| Method | Endpoint             | Access | Description |
|--------|-----------------------|--------|--------------|
| POST   | `/auth/register`      | Public | Register a new user |
| POST   | `/auth/login`         | Public | Log in, returns JWT |

### Products
| Method | Endpoint              | Access       | Description |
|--------|------------------------|--------------|--------------|
| GET    | `/products`             | Public       | List products (supports `?search=`, `?category=`, `?sort=price_asc\|price_desc`) |
| GET    | `/products/:id`         | Public       | Get single product |
| POST   | `/products`              | Admin only  | Create product |
| PUT    | `/products/:id`          | Admin only  | Update product |
| DELETE | `/products/:id`          | Admin only  | Delete product |

### Cart (all require login)
| Method | Endpoint         | Description |
|--------|-------------------|--------------|
| GET    | `/cart`            | Get current user's cart |
| POST   | `/cart`              | Add item to cart `{ productId, quantity }` |
| PUT    | `/cart/:id`           | Update quantity of item (id = productId) `{ quantity }` |
| DELETE | `/cart/:id`            | Remove item from cart (id = productId) |

### Orders
| Method | Endpoint                | Access       | Description |
|--------|---------------------------|--------------|--------------|
| POST   | `/orders`                  | Logged in   | Place an order (checkout) |
| GET    | `/orders/myorders`          | Logged in  | Get logged-in user's orders |
| GET    | `/orders`                    | Admin only | Get all orders |
| PUT    | `/orders/:id/status`          | Admin only | Update order status `{ status }` |

### Users
| Method | Endpoint  | Access     | Description |
|--------|------------|------------|--------------|
| GET    | `/users`    | Admin only | List all users |

All protected routes require an `Authorization: Bearer <token>` header. The token is returned by `/auth/login` and `/auth/register`.

---

## 9. Testing with Postman

1. Open Postman and create a new request.
2. **Register:** `POST http://localhost:5000/api/auth/login` (or register) with a JSON body:
   ```json
   { "email": "admin@example.com", "password": "admin123" }
   ```
3. Copy the `token` value from the response.
4. For any protected route (e.g. `POST /api/products`), go to the **Authorization** tab → type **Bearer Token** → paste the token.
5. Send requests to any endpoint listed above to test them directly.

---

## 10. Common Errors & Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `MongoDB connection error` | Wrong `MONGO_URI`, wrong password, or IP not whitelisted | Double-check your Atlas connection string and username/password; make sure your IP is allowed under Network Access |
| `Not authorized, no token provided` | Missing `Authorization` header | Log in and make sure the frontend/Postman sends `Authorization: Bearer <token>` |
| `Not authorized as an admin` | Logged in as a regular user trying to hit an admin route | Log in with the admin account (`admin@example.com` / `admin123`) |
| `CORS error` in browser console | Frontend URL not allowed by backend | The backend already has `cors()` enabled for all origins; make sure the backend is actually running on port 5000 |
| `EADDRINUSE: port already in use` | Another process is using port 5000 or 5173 | Stop the other process, or change `PORT` in `server/.env` (and `VITE_API_URL` accordingly) |
| Frontend shows "Failed to load products" | Backend not running, or `VITE_API_URL` wrong | Make sure the backend terminal shows "Server running on port 5000" and `client/.env` points to it |
| `npm run seed` fails | `.env` not created yet, or wrong `MONGO_URI` | Create `server/.env` first with a valid connection string |

---

## 11. Notes

- Passwords are hashed with **bcrypt** before being stored — plain-text passwords are never saved.
- JWT tokens expire after 7 days; users are logged out automatically after that (they'll need to log in again).
- Stock is automatically reduced when an order is placed, and cart quantity is validated against available stock.
- The seed script (`npm run seed`) can be re-run any time to reset the database to its initial sample state. To only wipe data without reseeding, run `npm run seed -- -d` (or `node seed.js -d` from inside `server/`).
