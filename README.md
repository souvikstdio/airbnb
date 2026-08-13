# Airbnb Clone

A full-stack web application built with Node.js and Express, replicating core features of Airbnb. This project demonstrates CRUD operations, user authentication, image uploads, and real-time notifications.

## Project Overview

This Airbnb Clone application allows users to:
- **Create, view, update, and delete property listings**
- **Leave and manage reviews** on listings
- **Manage their own listings** with full authorization checks
- **Upload property images** to the cloud
- **Authenticate and maintain user sessions** securely

The application uses a modern tech stack with a clean separation of concerns through MVC architecture.

## Features

### Core Functionality
- ✅ **User Authentication**: Secure signup and login using Passport.js with local strategy
- ✅ **Listing Management**: Full CRUD operations for property listings
- ✅ **Reviews System**: Users can add and delete reviews with ratings (1-5 stars)
- ✅ **Image Uploads**: Direct integration with Cloudinary for storing property images
- ✅ **Authorization**: Ownership verification - only listing owners can edit/delete
- ✅ **Session Management**: Secure session handling with MongoDB store
- ✅ **Form Validation**: Server-side validation using Joi schema validation
- ✅ **User Feedback**: Flash messages for success and error notifications
- ✅ **Responsive UI**: EJS templates with CSS styling

## Tech Stack

### Backend
- **Runtime**: Node.js (v22.12.0)
- **Framework**: Express.js
- **Database**: MongoDB with Mongoose ODM
- **Authentication**: Passport.js with Local Strategy
- **Session Store**: connect-mongo

### Frontend
- **Template Engine**: EJS with ejs-mate
- **Styling**: CSS
- **Client-side Logic**: JavaScript

### External Services
- **Cloud Storage**: Cloudinary (image uploads)
- **Mapping**: Mapbox SDK

### Utilities
- **Validation**: Joi
- **File Upload**: multer with Cloudinary storage
- **Flash Messages**: connect-flash
- **Method Override**: Support for PUT/DELETE methods
- **Environment Variables**: dotenv

## Project Structure

```
airbnb-clone/
├── app.js                      # Main application entry point
├── package.json                # Project dependencies
├── middleware.js               # Custom middleware (auth, validation)
├── schema.js                   # Joi validation schemas
├── cloudConfig.js              # Cloudinary configuration
│
├── models/                     # MongoDB schemas
│   ├── listing.js              # Listing schema with relations
│   ├── review.js               # Review schema
│   └── user.js                 # User schema with passport integration
│
├── routes/                     # Express route handlers
│   ├── listing-route.js        # Listing endpoints
│   ├── review-route.js         # Review endpoints
│   └── user-route.js           # Authentication endpoints
│
├── controller/                 # Business logic
│   ├── listing-controller.js   # Listing operations
│   ├── review-controller.js    # Review operations
│   └── user-controller.js      # User authentication
│
├── utils/                      # Utility functions
│   ├── expressError.js         # Custom error class
│   └── wrapAsync.js            # Async error handler wrapper
│
├── views/                      # EJS templates
│   ├── layouts/                # Layout templates
│   ├── listings/               # Listing pages (index, show, new, edit)
│   ├── users/                  # Authentication pages (signup, login)
│   └── includes/               # Reusable components (navbar, footer)
│
├── public/                     # Static files
│   ├── css/                    # Stylesheets
│   └── js/                     # Client-side scripts
│
└── init/                       # Database initialization
    ├── index.js                # Initialization script runner
    └── data.js                 # Sample seed data
```

## Prerequisites

- **Node.js**: v22.12.0 or later
- **npm**: v10 or later
- **MongoDB**: Atlas account (cloud) or local MongoDB instance
- **Cloudinary**: Free account for image storage
- **Git**: For version control

## Installation

### 1. Clone the Repository
```bash
git clone <repository-url>
cd airbnb-clone
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Environment Configuration

Create a `.env` file in the root directory and add the following environment variables:

```env
# Database
ATLASDB_URL=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/?retryWrites=true&w=majority&appName=<app-name>

# Cloudinary (Image Storage)
CLOUD_NAME=<your-cloud-name>
CLOUD_API_KEY=<your-api-key>
CLOUD_API_SECRET=<your-api-secret>

# Session Security
SECRET=<your-secret-key-for-session-encryption>
```

### How to Get These Values:

**MongoDB Atlas URL:**
1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a cluster and database
3. Click "Connect" and get your connection string
4. Replace `<username>`, `<password>`, and cluster details

**Cloudinary Credentials:**
1. Sign up at [Cloudinary](https://cloudinary.com/)
2. Go to Dashboard and copy your:
   - Cloud Name
   - API Key
   - API Secret

**SESSION SECRET:**
- Any random string (e.g., use `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`)

## Running the Project

### Start the Application
```bash
node app.js
```

The application will:
- Connect to MongoDB
- Start listening on `http://localhost:3000`
- Display "listening on port 3000" in the console

### Access the Application
- Open your browser and navigate to: `http://localhost:3000`
- You'll be redirected to `/listings` (the home page)

## Database Initialization

To seed the database with sample listings:

```bash
node init/index.js
```

**Note**: This script will:
- Delete all existing listings
- Insert sample data from `init/data.js`
- Assign all listings to a default owner

## API Documentation

### Listing Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---|
| GET | `/listings` | Get all listings | No |
| GET | `/listings/new` | Show create listing form | Yes |
| POST | `/listings` | Create new listing | Yes |
| GET | `/listings/:id` | View listing details | No |
| GET | `/listings/:id/edit` | Show edit listing form | Yes* |
| PUT | `/listings/:id` | Update listing | Yes* |
| DELETE | `/listings/:id` | Delete listing | Yes* |

*Only the listing owner can edit/delete

### Review Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---|
| POST | `/listings/:id/reviews` | Add review to listing | Yes |
| DELETE | `/listings/:id/reviews/:reviewId` | Delete review | Yes* |

*Only the review author can delete

### User Authentication Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/signup` | Show signup form |
| POST | `/signup` | Register new user |
| GET | `/login` | Show login form |
| POST | `/login` | Authenticate user |
| GET | `/logout` | Logout and destroy session |

## Listing Schema

Each listing contains:
```javascript
{
  title: String (required),
  description: String,
  location: String (required),
  country: String (required),
  price: Number (required, min: 0),
  image: {
    url: String (Cloudinary URL),
    filename: String
  },
  owner: ObjectId (reference to User),
  reviews: [ObjectId] (references to Reviews)
}
```

## Review Schema

Each review contains:
```javascript
{
  rating: Number (1-5, required),
  comment: String (required),
  author: ObjectId (reference to User),
  createdAt: Date (auto-timestamp)
}
```

## User Schema

Each user contains:
```javascript
{
  username: String (provided by passport-local-mongoose),
  email: String (required),
  password: String (encrypted by passport-local-mongoose)
}
```

## Authentication Flow

1. **Signup**: User registers with username, email, and password
   - Password is hashed using passport-local-mongoose
   - User is automatically logged in after signup
   - Redirected to `/listings`

2. **Login**: User authenticates with username and password
   - Passport validates credentials
   - Session is created and stored in MongoDB
   - User is redirected to the originally requested page or `/listings`

3. **Logout**: User session is destroyed
   - Session cookie is cleared
   - User is redirected to `/listings`

## Authorization & Middleware

The application uses custom middleware to enforce authorization:

- **isLoggedIn**: Checks if user is authenticated
  - Redirects to login if not authenticated
  - Stores the originally requested URL for post-login redirect

- **isOwned**: Verifies user is the listing owner
  - Prevents unauthorized updates/deletions
  - Checks: `listing.owner === req.user._id`

- **isOwned_review**: Verifies user is the review author
  - Prevents unauthorized review deletions
  - Checks: `review.author === req.user._id`

- **validatelisting**: Server-side validation for listings
  - Uses Joi schema to validate all fields
  - Returns 400 error with specific messages if validation fails

- **validatereview**: Server-side validation for reviews
  - Validates rating (1-5) and comment required
  - Returns 400 error if validation fails

## Image Upload & Cloudinary

When creating or updating a listing with an image:

1. **File Submission**: User uploads image via form (`form-data`)
2. **Multer Processing**: Multer intercepts the file
3. **Cloudinary Storage**: Image is uploaded directly to Cloudinary
4. **URL Storage**: Cloudinary URL and filename are saved in database
5. **Display**: Image is served from Cloudinary CDN

**Allowed formats**: PNG, JPG, JPEG  
**Storage folder**: `airBnb clone` (on Cloudinary)

## Session Management

Sessions are stored in MongoDB using `connect-mongo`:

- **Storage**: MongoDB database specified in `ATLASDB_URL`
- **Session Secret**: Encrypted using `SECRET` environment variable
- **Expiration**: 7 days from last activity
- **Cookie Options**:
  - `httpOnly`: True (prevents client-side script access)
  - `maxAge`: 7 days
  - `expires`: 7 days

## Form Validation

### Listing Validation (Joi Schema)
```javascript
{
  title: String (required),
  description: String (required),
  location: String (required),
  country: String (required),
  price: Number (required, min: 0),
  image: String (optional)
}
```

### Review Validation (Joi Schema)
```javascript
{
  rating: Number (required, 1-5),
  comment: String (required)
}
```

## Error Handling

The application uses a custom error handling system:

- **expressError**: Custom error class with `status` and `message`
- **wrapAsync**: Wrapper for async route handlers to catch errors
- **Error Middleware**: Catches all errors and displays error page

**Error Response**: Renders `listings/error.ejs` with status and error message

## Troubleshooting

### "Connection refused" to MongoDB
- Verify `ATLASDB_URL` is correct
- Check MongoDB Atlas cluster is running
- Ensure IP whitelist includes your current IP

### Cloudinary upload fails
- Verify `CLOUD_NAME`, `CLOUD_API_KEY`, `CLOUD_API_SECRET` are correct
- Check image format is PNG, JPG, or JPEG
- Verify Cloudinary account is active and has upload quota

### "You must be logged in" when creating/editing
- This is expected - only authenticated users can manage listings
- Sign up or login first, then try again

### Images not displaying
- Check Cloudinary URL is accessible
- Verify image upload succeeded (check browser DevTools)
- Check Cloudinary account has storage quota remaining

### Session lost on page refresh
- Verify `SECRET` environment variable is set
- Check MongoDB connection is stable
- Ensure cookies are enabled in browser

## Development Notes

### Code Organization
- **Models**: Define database schemas and relationships
- **Routes**: Define URL patterns and middleware chains
- **Controllers**: Contain business logic and database queries
- **Middleware**: Handle authentication, validation, authorization
- **Views**: EJS templates for rendering HTML

### Key Libraries Used
- **mongoose**: Object modeling for MongoDB
- **passport-local-mongoose**: Adds password hashing and authentication methods to User model
- **multer**: File upload middleware
- **joi**: Schema validation library
- **ejs-mate**: Layout support for EJS templates

### Async Error Handling
Routes wrap async functions with `wrapAsync()` to catch promise rejections and pass them to the error middleware.


---

For questions or issues, refer to the source code comments or the project's Git history.
