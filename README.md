# CareEase Laundry API

A comprehensive RESTful API for a laundry service management system built with Node.js, Express, and MongoDB. This API provides complete functionality for users, admins, and delivery personnel to manage laundry orders, payments, and service delivery.

## 🚀 Project Overview

CareEase Laundry API is a full-featured backend service that powers a laundry delivery platform. It handles user authentication, order management, payment processing, delivery coordination, and administrative functions.

### Key Features

- **Multi-role Authentication**: Support for regular users, admins, and delivery personnel
- **Order Management**: Complete order lifecycle from placement to delivery
- **Payment Integration**: Razorpay payment gateway integration
- **Pincode Validation**: Service availability checking by location
- **Dynamic Pricing**: Configurable pricing for different wash types
- **Delivery Management**: Order assignment and status tracking
- **Admin Dashboard**: Comprehensive admin controls and analytics

## 🛠️ Technology Stack

- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: MongoDB with Mongoose ODM
- **Authentication**: JWT (JSON Web Tokens)
- **Payment**: Razorpay integration
- **Security**: bcrypt for password hashing
- **Logging**: Winston
- **Deployment**: Vercel-ready configuration

## 📋 Prerequisites

Before running this project, ensure you have:

- Node.js (v14 or higher)
- MongoDB database (local or cloud)
- Razorpay account (for payment processing)
- Git

## 🚀 Installation & Setup

### 1. Clone the Repository

```bash
git clone <repository-url>
cd laundary-api
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Environment Configuration

Create a `.env` file in the root directory:

```env
PORT=4000
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/careease
JWT_SECRET=your_jwt_secret_key
RAZORPAY_KEY_ID=your_razorpay_key_id
RAZORPAY_KEY_SECRET=your_razorpay_secret
```

### 4. Database Setup

The application uses the following MongoDB collections:
- `users` - User accounts
- `userprofiles` - User profile information
- `deliveryboyprofiles` - Delivery personnel profiles
- `orders` - Order records
- `pincodes` - Serviceable pincodes
- `notavailables` - Non-serviceable pincodes
- `contactus` - Contact form submissions

### 5. Start the Application

```bash
# Development mode
npm start

# Production mode
npm start
```

The API will be available at `http://localhost:4000`

## 📚 API Documentation

### Base URL
```
http://localhost:4000/api
```

### Authentication

Most endpoints require JWT authentication. Include the token in the Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

The API also supports cookie-based authentication for web applications.

---

## 👤 User Endpoints

### 1. Check Service Availability

**POST** `/api/check-pincode`

Check if laundry service is available in a specific pincode.

**Request Body:**
```json
{
  "pincode": "110001"
}
```

**Response:**
```json
{
  "message": "Service available"
}
```

### 2. User Registration

**POST** `/api/signup`

Register a new user account.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securepassword123"
}
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### 3. User Login

**POST** `/api/login`

Authenticate user and receive access token.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securepassword123"
}
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "profileCompleted": true,
  "user": {
    "firstname": "John",
    "lastname": "Doe",
    "email": "user@example.com",
    "phone": "9876543210",
    "address": "123 Main St",
    "pincode": "110001",
    "city": "New Delhi",
    "state": "Delhi"
  }
}
```

### 4. Profile Management

#### Create/Update Profile
**POST** `/api/profile`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "firstname": "John",
  "lastname": "Doe",
  "email": "user@example.com",
  "phone": "9876543210",
  "address": "123 Main St",
  "pincode": "110001",
  "city": "New Delhi",
  "state": "Delhi"
}
```

#### Get Profile
**GET** `/api/profile`

**Headers:** `Authorization: Bearer <token>`

### 5. Order Management

#### Get All Orders
**GET** `/api/orders`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "orders": [
    {
      "_id": "order_id",
      "washType": "premium",
      "items": {
        "shirt": 2,
        "pant": 1,
        "tshirt": 3,
        "dress": 0,
        "cottonDress": 1
      },
      "total": 320,
      "orderDate": "16-06-2025",
      "pickupSlot": "6–8 AM",
      "status": "Pending",
      "payment": {
        "status": "pending",
        "paymentId": null,
        "method": "razorpay"
      }
    }
  ],
  "total": 320
}
```

#### Place New Order
**POST** `/api/order`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "washType": "premium",
  "items": {
    "shirt": 2,
    "pant": 1,
    "tshirt": 3,
    "dress": 0,
    "cottonDress": 1
  },
  "pickupSlot": "6–8 AM",
  "orderDate": "Thu Jun 26 2025",
  "address": "123 Main Street",
  "city": "New Delhi",
  "state": "Delhi",
  "pincode": "110001"
}
```

**Features:**
- **Wash Types**: standard, premium, dry cleaning, delicate
- **Pickup Slots**: "6–8 AM", "5–7 PM", "emergency"
- **Emergency Pickup**: 30% additional charge
- **Future Booking**: Schedule orders for upcoming dates

### 6. Payment Integration

#### Create Razorpay Order
**POST** `/api/create-razorpay-order`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "orderId": "order_id"
}
```

**Response:**
```json
{
  "orderId": "order_id",
  "razorpayOrderId": "order_Lv1234567890",
  "amount": 20800,
  "currency": "INR"
}
```

#### Verify Payment
**POST** `/api/verify-razorpay-payment`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "orderId": "order_id",
  "razorpay_payment_id": "pay_Lv1234567890",
  "razorpay_order_id": "order_Lv1234567890",
  "razorpay_signature": "generated_signature"
}
```

### 7. Pricing Information

**GET** `/api/pricing`

**Response:**
```json
{
  "standard": {
    "shirt": 30,
    "pant": 40,
    "tshirt": 25,
    "dress": 50,
    "cottonDress": 60
  },
  "premium": {
    "shirt": 50,
    "pant": 60,
    "tshirt": 40,
    "dress": 80,
    "cottonDress": 100
  },
  "dry cleaning": {
    "shirt": 80,
    "pant": 90,
    "tshirt": 70,
    "dress": 120,
    "cottonDress": 140
  },
  "delicate": {
    "shirt": 100,
    "pant": 110,
    "tshirt": 90,
    "dress": 150,
    "cottonDress": 170
  }
}
```

### 8. Contact Support

**POST** `/api/contact-us`

**Request Body:**
```json
{
  "firstname": "John",
  "lastname": "Doe",
  "email": "john@example.com",
  "message": "I have a question about your service."
}
```

### 9. Account Management

#### Logout
**POST** `/api/logout`

**Headers:** `Authorization: Bearer <token>`

#### Delete Account
**DELETE** `/api/delete-account`

**Headers:** `Authorization: Bearer <token>`

---

## 🔧 Admin Endpoints

### Authentication

**POST** `/api/admin/login`

**Request Body:**
```json
{
  "email": "admin@example.com",
  "password": "adminpassword"
}
```

### User Management

#### View All Users
**GET** `/api/admin/users`

#### View All User Profiles
**GET** `/api/admin/user-profiles`

#### View User Profile by ID
**GET** `/api/admin/user-profile/:userId`

### Order Management

#### View All Orders
**GET** `/api/admin/orders`

**Query Parameters:**
- `washType` - Filter by wash type
- `city` - Filter by city
- `state` - Filter by state
- `date` - Filter by order date
- `userId` - Filter by user ID

#### Update Order Status
**PUT** `/api/admin/orders/:orderId/status`

**Request Body:**
```json
{
  "status": "Delivered"
}
```

**Available Statuses:** Pending, Picked Up, In Progress, Delivered

#### Assign Order to Delivery Boy
**PUT** `/api/admin/orders/:orderId/assign`

**Request Body:**
```json
{
  "deliveryBoyId": "delivery_boy_user_id"
}
```

### Pricing Management

#### Get Current Pricing
**GET** `/api/admin/pricing`

#### Update Pricing
**PUT** `/api/admin/pricing`

**Request Body:**
```json
{
  "washType": "premium",
  "prices": {
    "shirt": 55,
    "pant": 65,
    "tshirt": 45,
    "dress": 90,
    "cottonDress": 110
  }
}
```

#### Add New Wash Type
**POST** `/api/admin/wash-types`

**Request Body:**
```json
{
  "washType": "super deluxe",
  "prices": {
    "shirt": 120,
    "pant": 130,
    "tshirt": 110,
    "dress": 180,
    "cottonDress": 200
  }
}
```

#### Remove Wash Type
**DELETE** `/api/admin/wash-types/:washType`

### Delivery Boy Management

#### View All Delivery Boys
**GET** `/api/admin/delivery-boys`

#### Create/Update Delivery Boy Profile
**POST** `/api/admin/delivery-boy-profile`

**Request Body:**
```json
{
  "userId": "delivery_boy_user_id",
  "firstname": "Ravi",
  "lastname": "Kumar",
  "email": "ravi.delivery@example.com",
  "phone": "9876543210",
  "address": "456 Delivery Lane",
  "pincode": "110002",
  "city": "New Delhi",
  "state": "Delhi"
}
```

#### View Delivery Boy Profile
**GET** `/api/admin/delivery-boy-profile/:userId`

---

## 🚚 Delivery Boy Endpoints

### Authentication

**POST** `/api/delivery/login`

**Request Body:**
```json
{
  "email": "deliveryboy@example.com",
  "password": "password123"
}
```

### Order Management

#### Get Assigned Orders
**GET** `/api/delivery/orders`

**Headers:** `Authorization: Bearer <delivery_token>`

#### Update Order Status
**PUT** `/api/delivery/orders/:orderId/status`

**Headers:** `Authorization: Bearer <delivery_token>`

**Request Body:**
```json
{
  "status": "Delivered"
}
```

---

## 🗄️ Database Schema

### User Schema
```javascript
{
  email: String,
  password: String,
  isAdmin: Boolean,
  isDeliveryBoy: Boolean
}
```

### User Profile Schema
```javascript
{
  userId: ObjectId,
  firstname: String,
  lastname: String,
  email: String,
  phone: String,
  address: String,
  pincode: String,
  city: String,
  state: String
}
```

### Order Schema
```javascript
{
  userId: ObjectId,
  washType: String, // enum: ['standard', 'premium', 'dry cleaning', 'delicate']
  items: {
    shirt: Number,
    pant: Number,
    tshirt: Number,
    dress: Number,
    cottonDress: Number
  },
  total: Number,
  address: String,
  city: String,
  state: String,
  pincode: String,
  orderDate: String, // DD-MM-YYYY format
  pickupSlot: String, // enum: ['6–8 AM', '5–7 PM', 'emergency']
  status: String, // enum: ['Pending', 'Picked Up', 'In Progress', 'Delivered']
  deliveryBoyId: ObjectId,
  payment: {
    status: String, // enum: ['pending', 'paid']
    paymentId: String,
    method: String
  }
}
```

---

## 🔒 Security Features

- **JWT Authentication**: Secure token-based authentication
- **Password Hashing**: bcrypt for secure password storage
- **CORS Configuration**: Configured for specific domains
- **Input Validation**: Sanitized inputs and validation
- **HTTP-Only Cookies**: Secure cookie handling for web applications

## 🚀 Deployment

### Vercel Deployment

The project includes `vercel.json` configuration for easy deployment to Vercel:

```json
{
  "version": 2,
  "builds": [
    { "src": "index.js", "use": "@vercel/node" }
  ],
  "routes": [
    { "src": "/(.*)", "dest": "/index.js" }
  ]
}
```

### Environment Variables for Production

Set these environment variables in your deployment platform:

- `PORT` - Server port (default: 4000)
- `MONGODB_URI` - MongoDB connection string
- `JWT_SECRET` - Secret key for JWT signing
- `RAZORPAY_KEY_ID` - Razorpay public key
- `RAZORPAY_KEY_SECRET` - Razorpay secret key

## 📝 API Response Format

All API responses follow a consistent format:

### Success Response
```json
{
  "message": "Operation successful",
  "data": { ... }
}
```

### Error Response
```json
{
  "message": "Error description"
}
```

## 🔧 Development

### Project Structure
```
laundary-api/
├── controllers/
│   ├── admin/
│   │   └── admin.js
│   ├── delivery/
│   │   └── delivery.js
│   └── users/
│       ├── api.js
│       ├── contact.js
│       ├── orderapi.js
│       └── payment.js
├── middleware/
│   └── auth.js
├── models/
│   ├── ContactUs.js
│   ├── DeliveryBoyProfile.js
│   ├── NotAvailableSchema.js
│   ├── OrderSchema.js
│   ├── PincodeSchema.js
│   ├── pricing.js
│   ├── UserProfile.js
│   └── Users.js
├── index.js
├── package.json
├── vercel.json
└── README.md
```

### Adding New Features

1. **New Endpoints**: Add routes in appropriate controller files
2. **New Models**: Create schema files in the `models/` directory
3. **Middleware**: Add authentication or validation middleware
4. **Testing**: Test endpoints with tools like Postman or curl

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is licensed under the ISC License.

## 🆘 Support

For support and questions:
- Create an issue in the repository
- Contact the development team
- Check the API documentation above

---