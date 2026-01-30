# 🛒 ScanGo - Complete Full-Stack Self-Checkout System

## 📖 Table of Contents
1. [Overview](#overview)
2. [Features](#features)
3. [Tech Stack](#tech-stack)
4. [Prerequisites](#prerequisites)
5. [Installation](#installation)
6. [Running the Application](#running-the-application)
7. [API Documentation](#api-documentation)
8. [How It Works](#how-it-works)
9. [Project Structure](#project-structure)
10. [Environment Variables](#environment-variables)

---

## 🎯 Overview

**ScanGo** is a smart self-checkout shopping application that eliminates billing queues at shopping malls. Users can scan items, manage their cart, apply discounts, make payments, and exit the store seamlessly using QR codes.

---

## ✨ Features

### User Features
- ✅ User registration & JWT authentication
- 📱 Barcode scanning simulation
- 🛒 Real-time cart management
- 🎁 Discount code application
- 💳 Multiple payment methods (UPI/Card/Wallet)
- 📄 Digital bill generation
- 🔐 Exit QR code for security gates
- 📊 Transaction history
- 👤 User profile management

### Technical Features
- 🔒 JWT-based secure authentication
- 🗄️ MongoDB database with Mongoose
- 🎨 Modern responsive UI (dark theme)
- 🔄 Context API for state management
- 🌐 RESTful API architecture
- 📦 QR code generation
- ⚡ Real-time cart updates

---

## 🛠️ Tech Stack

### Frontend
- **React** (with Vite)
- **React Router** v6 (routing)
- **Context API** (state management)
- **Axios** (HTTP client)
- **qrcode.react** (QR generation)

### Backend
- **Node.js**
- **Express.js** (REST API)
- **MongoDB** (database)
- **Mongoose** (ODM)
- **JWT** (authentication)
- **bcryptjs** (password hashing)
- **qrcode** (QR generation)

---

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

1. **Node.js** (v16 or higher)
   - Download from: https://nodejs.org/
   - Verify: `node --version`

2. **MongoDB** (v5 or higher)
   - Download from: https://www.mongodb.com/try/download/community
   - Or use MongoDB Atlas (cloud): https://www.mongodb.com/cloud/atlas
   - Verify: `mongod --version`

3. **npm** or **yarn** (comes with Node.js)
   - Verify: `npm --version`

---

## 🚀 Installation

### Step 1: Clone or Create Project Structure

Create the following folder structure:
```
scango/
├── backend/
└── frontend/
```

### Step 2: Backend Setup

Navigate to backend folder:
```bash
cd backend
```

Initialize npm:
```bash
npm init -y
```

Install dependencies:
```bash
npm install express mongoose bcryptjs jsonwebtoken dotenv cors qrcode uuid
npm install --save-dev nodemon
```

Create all backend files (copy from code provided above):
- `server.js`
- `config/db.js`
- `models/` (User.js, Product.js, Cart.js, Order.js)
- `controllers/` (all controller files)
- `routes/` (all route files)
- `middleware/authMiddleware.js`
- `utils/generateQR.js`
- `.env`

### Step 3: Frontend Setup

Navigate to frontend folder:
```bash
cd frontend
```

Create Vite React app:
```bash
npm create vite@latest . -- --template react
```

Install dependencies:
```bash
npm install react-router-dom axios qrcode.react
```

Create all frontend files (copy from code provided):
- `src/App.jsx`
- `src/main.jsx`
- `src/index.css`
- `src/components/` (Navbar, PrivateRoute)
- `src/pages/` (all page files)
- `src/context/` (AuthContext, CartContext)
- `vite.config.js`

---

## ▶️ Running the Application

### Step 1: Start MongoDB

**Option A - Local MongoDB:**
```bash
mongod
```

**Option B - MongoDB Atlas (Cloud):**
1. Create account at https://www.mongodb.com/cloud/atlas
2. Create a cluster
3. Get connection string
4. Update `.env` file with your connection string

### Step 2: Configure Environment Variables

Create `.env` file in **backend/** folder:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/scango
JWT_SECRET=your_super_secret_jwt_key_change_in_production_2024
NODE_ENV=development
```

**If using MongoDB Atlas**, replace `MONGO_URI`:
```env
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/scango?retryWrites=true&w=majority
```

### Step 3: Seed Database (Optional but Recommended)

After starting the backend, seed products:

```bash
# Make a POST request to:
POST http://localhost:5000/api/products/seed
```

Or use curl:
```bash
curl -X POST http://localhost:5000/api/products/seed
```

Or in browser, install a REST client extension and POST to that URL.

### Step 4: Start Backend Server

In **backend/** folder:

```bash
# Development mode (auto-reload)
npm run dev

# Production mode
npm start
```

You should see:
```
✅ MongoDB Connected: localhost
🚀 Server running on port 5000
```

### Step 5: Start Frontend

In **frontend/** folder:

```bash
npm run dev
```

You should see:
```
  VITE v5.0.11  ready in 500 ms

  ➜  Local:   http://localhost:3000/
  ➜  Network: use --host to expose
```

### Step 6: Access Application

Open browser and go to:
```
http://localhost:3000
```

---

## 📡 API Documentation

### Base URL
```
http://localhost:5000/api
```

### Authentication Endpoints

#### Register User
```http
POST /auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "phone": "+91 9876543210"
}

Response:
{
  "_id": "...",
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "+91 9876543210",
  "token": "JWT_TOKEN"
}
```

#### Login User
```http
POST /auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}

Response:
{
  "_id": "...",
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "+91 9876543210",
  "token": "JWT_TOKEN"
}
```

#### Get Profile
```http
GET /auth/profile
Authorization: Bearer JWT_TOKEN

Response:
{
  "_id": "...",
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "+91 9876543210"
}
```

### Product Endpoints

#### Get All Products
```http
GET /products
Authorization: Bearer JWT_TOKEN

Response:
[
  {
    "_id": "...",
    "name": "Samsung Galaxy S24",
    "barcode": "PROD-001",
    "price": 79999,
    "category": "Electronics",
    "image": "...",
    "stock": 50
  },
  ...
]
```

#### Generate Random Barcode
```http
GET /products/generate-barcode
Authorization: Bearer JWT_TOKEN

Response:
{
  "barcode": "SCAN-A1B2C3D4",
  "originalBarcode": "PROD-001",
  "product": { /* product object */ }
}
```

#### Get Product by Barcode
```http
GET /products/scan/:barcode
Authorization: Bearer JWT_TOKEN

Response:
{
  "_id": "...",
  "name": "Samsung Galaxy S24",
  "barcode": "PROD-001",
  "price": 79999,
  ...
}
```

### Cart Endpoints

#### Get Cart
```http
GET /cart
Authorization: Bearer JWT_TOKEN

Response:
{
  "_id": "...",
  "user": "...",
  "items": [
    {
      "_id": "...",
      "product": { /* populated product */ },
      "quantity": 2,
      "price": 79999
    }
  ],
  "totalPrice": 159998,
  "discount": 0
}
```

#### Add to Cart
```http
POST /cart/add
Authorization: Bearer JWT_TOKEN
Content-Type: application/json

{
  "productId": "...",
  "quantity": 1
}

Response:
{ /* updated cart */ }
```

#### Update Cart Item
```http
PUT /cart/update/:itemId
Authorization: Bearer JWT_TOKEN
Content-Type: application/json

{
  "quantity": 3
}

Response:
{ /* updated cart */ }
```

#### Remove from Cart
```http
DELETE /cart/remove/:itemId
Authorization: Bearer JWT_TOKEN

Response:
{ /* updated cart */ }
```

#### Apply Discount
```http
POST /cart/discount
Authorization: Bearer JWT_TOKEN
Content-Type: application/json

{
  "discountCode": "SAVE10"
}

Response:
{
  "cart": { /* updated cart */ },
  "message": "Discount of ₹8000 applied!"
}
```

Available discount codes:
- `SAVE10` - 10% off
- `SAVE20` - 20% off
- `FLAT100` - ₹100 off
- `FLAT200` - ₹200 off

### Order Endpoints

#### Create Order
```http
POST /orders/create
Authorization: Bearer JWT_TOKEN
Content-Type: application/json

{
  "paymentMethod": "UPI",
  "transactionId": "TXN-123456"
}

Response:
{
  "_id": "...",
  "user": "...",
  "items": [...],
  "totalPrice": 159998,
  "discount": 8000,
  "finalPrice": 151998,
  "paymentMethod": "UPI",
  "transactionId": "TXN-123456",
  "paymentStatus": "Completed",
  "status": "Processing"
}
```

#### Get All Orders
```http
GET /orders
Authorization: Bearer JWT_TOKEN

Response:
[
  { /* order 1 */ },
  { /* order 2 */ },
  ...
]
```

#### Get Order by ID
```http
GET /orders/:id
Authorization: Bearer JWT_TOKEN

Response:
{ /* order object */ }
```

#### Generate QR Codes
```http
POST /orders/:id/generate-qr
Authorization: Bearer JWT_TOKEN

Response:
{
  "billQR": "data:image/png;base64,...",
  "exitQR": "data:image/png;base64,..."
}
```

#### Mark Order as Exited
```http
PUT /orders/:id/exit
Authorization: Bearer JWT_TOKEN

Response:
{
  "_id": "...",
  "status": "Exited",
  ...
}
```

### Payment Endpoints

#### Process Payment
```http
POST /payment/process
Authorization: Bearer JWT_TOKEN
Content-Type: application/json

{
  "orderId": "...",
  "paymentMethod": "UPI",
  "upiId": "user@upi"
}

Response:
{
  "success": true,
  "transactionId": "TXN-A1B2C3D4E5F6",
  "message": "Payment successful",
  "orderId": "..."
}
```

#### Verify Payment
```http
GET /payment/verify/:transactionId
Authorization: Bearer JWT_TOKEN

Response:
{
  "verified": true,
  "order": { /* order object */ }
}
```

---

## 🔍 How It Works

### Authentication Flow

1. **User Registration**:
   - User fills registration form
   - Backend hashes password using bcrypt
   - Creates user in database
   - Generates JWT token
   - Returns user data + token

2. **User Login**:
   - User provides email/password
   - Backend verifies credentials
   - Compares hashed password
   - Generates JWT token
   - Token valid for 30 days

3. **Protected Routes**:
   - Frontend stores token in localStorage
   - Axios automatically sends token in headers
   - Backend middleware verifies token
   - Extracts user from token
   - Attaches user to request object

### Shopping Flow

1. **Splash Screen** (3 seconds):
   - Animated loading screen
   - Auto-redirects to Home

2. **Home Dashboard**:
   - Quick access to all features
   - Scan, Cart, Offers, Profile

3. **Scan Items**:
   - Click "Generate Barcode"
   - Backend returns random product
   - Displays product details
   - Add to cart

4. **Cart Management**:
   - View all items
   - Adjust quantities (+/-)
   - Remove items
   - See live total calculation
   - Apply discounts

5. **Offers & Discounts**:
   - View available offers
   - Enter/apply discount codes
   - Discount reflected in cart

6. **Checkout**:
   - Review order summary
   - Create order in database
   - Proceed to payment

7. **Payment**:
   - Choose payment method (UPI/Card/Wallet)
   - Enter payment details
   - Simulate payment (95% success rate)
   - Generate transaction ID

8. **Payment Success**:
   - Confirmation animation
   - Auto-redirect to bill

9. **Bill Page**:
   - Display complete invoice
   - Order details
   - Item list
   - Amount breakdown
   - Generate bill QR code
   - Proceed to exit

10. **Exit QR**:
    - Display exit QR code
    - User shows to security
    - Mark order as exited
    - EAS tag deactivation message

11. **Profile**:
    - View user info
    - Transaction history
    - View past bills
    - Logout

### Database Schema Relationships

```
User (1) ----< Cart (1)
User (1) ----< Order (Many)
Product (1) ----< CartItem (Many)
Product (1) ----< OrderItem (Many)
Cart (1) ----< CartItem (Many)
Order (1) ----< OrderItem (Many)
```

### Context API State Management

**AuthContext**:
- Stores: user, token, isAuthenticated
- Methods: login(), register(), logout()
- Persists token in localStorage
- Auto-configures Axios headers

**CartContext**:
- Stores: cart, cartCount, totals
- Methods: addToCart(), updateQuantity(), removeItem(), applyDiscount()
- Auto-fetches cart on login
- Real-time calculations

---

## 📂 Project Structure

### Backend Structure
```
backend/
├── config/
│   └── db.js                 # MongoDB connection
├── controllers/
│   ├── authController.js     # Register, login, profile
│   ├── cartController.js     # Cart CRUD operations
│   ├── orderController.js    # Order management
│   ├── paymentController.js  # Payment simulation
│   └── productController.js  # Product operations
├── middleware/
│   └── authMiddleware.js     # JWT verification
├── models/
│   ├── User.js              # User schema
│   ├── Product.js           # Product schema
│   ├── Cart.js              # Cart schema
│   └── Order.js             # Order schema
├── routes/
│   ├── authRoutes.js        # /api/auth routes
│   ├── cartRoutes.js        # /api/cart routes
│   ├── orderRoutes.js       # /api/orders routes
│   ├── paymentRoutes.js     # /api/payment routes
│   └── productRoutes.js     # /api/products routes
├── utils/
│   └── generateQR.js        # QR code utility
├── .env                     # Environment variables
├── package.json
└── server.js                # Express app entry
```

### Frontend Structure
```
frontend/
├── src/
│   ├── components/
│   │   ├── Navbar.jsx       # Navigation bar
│   │   ├── Navbar.css
│   │   └── PrivateRoute.jsx # Route protection
│   ├── context/
│   │   ├── AuthContext.jsx  # Auth state
│   │   └── CartContext.jsx  # Cart state
│   ├── pages/
│   │   ├── Login.jsx        # Login page
│   │   ├── Register.jsx     # Registration page
│   │   ├── Splash.jsx       # Splash screen
│   │   ├── Home.jsx         # Dashboard
│   │   ├── Scan.jsx         # Barcode scanning
│   │   ├── Cart.jsx         # Cart management
│   │   ├── Offers.jsx       # Discount codes
│   │   ├── Checkout.jsx     # Order review
│   │   ├── Payment.jsx      # Payment methods
│   │   ├── PaymentSuccess.jsx # Success screen
│   │   ├── Bill.jsx         # Invoice
│   │   ├── ExitQR.jsx       # Exit gate QR
│   │   ├── Profile.jsx      # User profile
│   │   ├── Auth.css         # Auth pages styles
│   │   ├── Splash.css
│   │   ├── Home.css
│   │   ├── Scan.css
│   │   ├── Cart.css
│   │   ├── Offers.css
│   │   ├── Checkout.css
│   │   ├── Payment.css
│   │   ├── PaymentSuccess.css
│   │   ├── Bill.css
│   │   ├── ExitQR.css
│   │   └── Profile.css
│   ├── App.jsx              # Router setup
│   ├── main.jsx             # React entry
│   └── index.css            # Global styles
├── index.html
├── package.json
└── vite.config.js
```

---

## 🔐 Environment Variables

### Backend (.env)

```env
# Server Configuration
PORT=5000
NODE_ENV=development

# Database
MONGO_URI=mongodb://localhost:27017/scango
# For MongoDB Atlas:
# MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/scango

# JWT Secret (Change this in production!)
JWT_SECRET=your_super_secret_jwt_key_change_in_production_2024
```

**Important**: Never commit `.env` file to version control!

Add to `.gitignore`:
```
.env
node_modules/
```

---

## 🧪 Testing the Application

### 1. Register a New User
- Go to http://localhost:3000/register
- Fill in details
- Click "Create Account"

### 2. Login
- Use registered credentials
- You'll be redirected to splash screen then home

### 3. Scan Items
- Click "Scan Items" or navigate to /scan
- Click "Generate Barcode" multiple times
- Each click generates a new barcode
- Add items to cart

### 4. Manage Cart
- View cart
- Adjust quantities
- Remove items
- Total updates automatically

### 5. Apply Discounts
- Go to Offers page
- Try codes: SAVE10, SAVE20, FLAT100, FLAT200
- Return to cart to see discount applied

### 6. Checkout & Payment
- Proceed to checkout
- Review order
- Select payment method
- Complete payment (95% success rate)

### 7. View Bill
- See invoice details
- Bill QR code displayed

### 8. Exit
- View exit QR
- Mark as exited
- Check profile for transaction history

---

## 🐛 Common Issues & Solutions

### Issue 1: MongoDB Connection Failed
**Error**: `MongooseServerSelectionError: connect ECONNREFUSED`

**Solution**:
- Ensure MongoDB is running: `mongod`
- Check connection string in `.env`
- For Atlas, ensure IP is whitelisted

### Issue 2: Port Already in Use
**Error**: `EADDRINUSE: address already in use :::5000`

**Solution**:
```bash
# Find process using port 5000
lsof -i :5000

# Kill the process
kill -9 <PID>

# Or change port in .env
PORT=5001
```

### Issue 3: CORS Error
**Error**: `Access-Control-Allow-Origin missing`

**Solution**:
- Ensure backend has `cors` installed
- Check `app.use(cors())` in server.js

### Issue 4: Token Not Working
**Error**: `Not authorized, token failed`

**Solution**:
- Clear localStorage
- Re-login
- Check if JWT_SECRET matches in .env

### Issue 5: Products Not Showing
**Solution**:
- Seed the database:
```bash
POST http://localhost:5000/api/products/seed
```

---

## 🚀 Deployment

### Backend Deployment (Heroku/Render)

1. Create account on Render.com
2. Create new Web Service
3. Connect GitHub repo
4. Set environment variables
5. Deploy

### Frontend Deployment (Vercel/Netlify)

1. Create account on Vercel.com
2. Import project
3. Build command: `npm run build`
4. Output directory: `dist`
5. Deploy

---

## 📚 Learning Points

### For Beginners

1. **JWT Authentication**:
   - Token-based auth
   - Stored in localStorage
   - Sent in headers

2. **Context API**:
   - Global state management
   - Avoid prop drilling
   - Share data across components

3. **Protected Routes**:
   - Verify authentication
   - Redirect if not logged in

4. **REST API Design**:
   - CRUD operations
   - HTTP methods
   - Status codes

5. **MongoDB Relationships**:
   - One-to-many
   - Populate references
   - Schema design

---

## 🎯 Next Steps / Enhancements

- [ ] Add real payment gateway (Razorpay/Stripe)
- [ ] Implement real barcode scanning (camera)
- [ ] Add product search
- [ ] Email notifications
- [ ] Admin panel
- [ ] Analytics dashboard
- [ ] Multiple stores support
- [ ] Wishlist feature
- [ ] Product reviews

---

## 📝 License

This project is for educational purposes.

---

## 👨‍💻 Support

For issues or questions:
1. Check this README
2. Review code comments
3. Test API endpoints with Postman

---

**Congratulations! You now have a complete full-stack self-checkout application! 🎉**
