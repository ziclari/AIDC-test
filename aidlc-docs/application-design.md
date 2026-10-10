# AnyCompanyRead - Application Design

**Version:** 1.0  
**Date:** October 9, 2026  
**AI-DLC Phase:** Design  
**Status:** Pending Human Approval

---

## Document Control

| Field | Value |
|-------|-------|
| Version | 1.0 |
| Last Updated | October 9, 2026 |
| Author | Kiro (AI-Driven Development Life Cycle) |
| Reviewers | Engineering, Product, Design |
| Next Review | Human approval |

---

## 1. Architecture Overview

### 1.1 Local-First Runtime

AnyCompanyRead is designed as a **locally runnable MVP** that requires no cloud infrastructure for development and demonstration:

| Component | Runtime | Infrastructure |
|-----------|---------|----------------|
| Frontend | React + Vite dev server | Local npm run dev |
| Backend | Node.js + Express | Local npm run dev |
| Database | PostgreSQL | Docker Compose (local) or direct connection |
| External Integrations | Node.js modules | Configurable via environment variables |

**Key Principle:** All core functionality must run without AWS accounts, cloud services, or paid external services. External integrations use provider interfaces with local mock implementations.

### 1.2 Technology Stack

| Layer | Technology | Purpose | Notes |
|-------|------------|---------|-------|
| Frontend | React 18 + Vite 5 | UI framework | Hot reload, modern bundling |
| State Management | React Query + Context API | Data fetching and state | Lightweight, server state focused |
| Styling | CSS Modules / Tailwind | Component styling | Local scoping or utility-first |
| Backend | Node.js 20 + Express 4 | API server | RESTful, modular monolith |
| Logging | pino or winston | Structured JSON logging | Console/file output for MVP |
| Database | PostgreSQL 15 | Data persistence | Docker or direct connection |
| Authentication | JWT + bcrypt/argon2 | Session management | Application-managed |
| API Testing | Jest + supertest | API test suite | 80%+ coverage target |

### 1.3 Architecture Pattern

**Modular Monolith with Domain-Driven Design**

- Single Express.js application
- One module per business domain (auth, catalog, cart, checkout, order, digital, etc.)
- Each module has its own models, services, routes, and domain logic
- Modules communicate via well-defined interfaces
- Single database shared by all modules (PostgreSQL)
- Deployment: single Node.js process

**Why Modular Monolith for MVP?**
- Simpler deployment and development environment
- Easier to understand and debug
- Clear domain boundaries enable future microservices decomposition
- Single codebase reduces operational complexity
- Sufficient for MVP scale (10K-100K users)

### 1.4 Deployment Architecture

#### Local Development
```
┌────────────────────────────────────────────────────────────────────────────┐
│                    Local Development Environment                           │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  ┌──────────────┐     ┌─────────────────┐     ┌──────────────────┐        │
│  │  React Frontend│────▶│  Express Backend│────▶│  PostgreSQL      │        │
│  │  (Vite Dev)    │     │  (npm run dev)  │     │  (Docker Compose)│        │
│  └──────────────┘     └─────────────────┘     └──────────────────┘        │
│                                            │                               │
│  ┌──────────────────┐     ┌────────────────▼─────────────────┐            │
│  │  Mock Providers  │     │  External Provider Interfaces    │            │
│  │  (local mock)    │     │  (configurable via env vars)     │            │
│  │  - MockPayment   │     │  - Stripe (production)           │            │
│  │  - Mock3PL       │     │  - 3PL Provider (production)     │            │
│  │  - MockDRM       │     │  - DRM Provider (production)     │            │
│  └──────────────────┘     └──────────────────────────────────┘            │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

#### Production Deployment (Future)
```
┌────────────────────────────────────────────────────────────────────────────┐
│                    Cloud Production Architecture                           │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  ┌──────────────┐     ┌─────────────────┐     ┌──────────────────┐        │
│  │  React Frontend│────▶│  Express Backend│────▶│  PostgreSQL      │        │
│  │  (S3 + CloudFront)│  │  (ECS/Fargate)  │     │  (Aurora RDS)    │        │
│  └──────────────┘     └─────────────────┘     └──────────────────┘        │
│                                            │                               │
│  ┌──────────────────┐     ┌────────────────▼─────────────────┐            │
│  │  External        │     │  AWS Services                    │            │
│  │  Provider        │     │  - CloudFront (CDN)              │            │
│  │  Interfaces      │     │  - CloudWatch (monitoring)       │            │
│  │  - Stripe        │     │  - Cognito (auth replacement)    │            │
│  │  - 3PL           │     │  - Parameter Store (config)      │            │
│  │  - DRM           │     │  - Secrets Manager               │            │
│  └──────────────────┘     └──────────────────────────────────┘            │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Frontend Architecture

### 2.1 Component Structure

```
src/
├── components/           # Reusable UI components
│   ├── common/          # Shared components (Button, Input, Modal)
│   ├── layout/          # Page layout (Header, Footer, Sidebar)
│   └── features/        # Feature-specific components
│       ├── auth/        # Login, Registration, MFA
│       ├── catalog/     # Product card, Search, Filters
│       ├── cart/        # Cart summary, Checkout form
│       └── digital/     # E-book reader, Audiobook player
├── pages/               # Page routes
│   ├── auth/            # Login, Register, ForgotPassword
│   ├── catalog/         # Home, ProductDetail, Search
│   ├── cart/            # Cart, Checkout
│   ├── account/         # Profile, OrderHistory, Addresses
│   └── digital/         # Reader, Player
├── hooks/               # Custom React hooks
│   ├── useAuth.js       # Authentication state
│   ├── useCart.js       # Cart state
│   └── useApi.js        # API calls
├── services/            # API service layer
│   ├── api.js           # Base axios instance
│   ├── auth.js          # Auth API calls
│   ├── catalog.js       # Catalog API calls
│   ├── cart.js          # Cart API calls
│   └── digital.js       # Digital delivery API calls
├── utils/               # Utility functions
│   ├── format.js        # Price, date formatting
│   ├── validation.js    # Form validation
│   └── constants.js     # App constants
├── App.js               # Main app component
├── main.js              # Entry point
└── index.html           # HTML template
```

### 2.2 Key Frontend Patterns

**State Management Strategy:**
- **Server state (API responses):** React Query for caching, refetching, pagination
- **Client state (UI state, cart):** Context API + useReducer
- **Global user state:** Separate context for auth/session

**Data Flow:**
```
User Action
    ↓
Component Handler
    ↓
Service Layer (api.js)
    ↓
Axios Instance (with auth token)
    ↓
Express Backend API
    ↓
Database (PostgreSQL)
    ↓
Response flows back through React Query cache
```

### 2.3 Authentication Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Input       │     │  Auth Hook       │     │  Auth Service    │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Submit login     │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Call API           │
        │                     ├────────────────────────►│
        │                     │                         │
        │                     │ 3. Validate & return  │
        │                     │◄────────────────────────┤
        │                     │    JWT token            │
        │                     │                         │
        │ 4. Store token      │                         │
        │◄────────────────────│                         │
        │                     │                         │
        │ 5. Redirect to      │                         │
        │    protected route  │                         │
        ├────────────────────►│                         │
```

**Token Storage:**
- JWT stored in memory (not localStorage) for XSS protection
- Session persisted via React Query cache
- Token refreshed before expiry via interceptor

### 2.4 Search & Catalog Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Search      │     │  Search Hook     │     │  Search Service  │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Input query      │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Build query params │
        │                     ├────────────────────────►│
        │                     │    (title, author,     │
        │                     │     filters, page)     │
        │                     │                         │
        │                     │ 3. PostgreSQL FTS     │
        │                     │    + GiST/GIN indexes │
        │                     ├────────────────────────►│
        │                     │    (PostgreSQL)        │
        │                     │                         │
        │                     │ 4. Return results     │
        │                     │◄────────────────────────┤
        │                     │    (JSON array)        │
        │                     │                         │
        │ 5. Display results  │                         │
        │◄────────────────────│                         │
        │                     │                         │
```

**Search Features:**
- PostgreSQL full-text search with `tsvector`/`tsquery`
- GiST/GIN indexes for <500ms response
- Typos tolerance via Levenshtein distance
- Relevance ranking by title, author, description

### 2.5 Cart & Checkout Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Cart        │     │  Cart Context    │     │  Cart Service    │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Add to cart      │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Validate & save    │
        │                     ├────────────────────────►│
        │                     │    (PostgreSQL)        │
        │                     │                         │
        │                     │ 3. Return updated cart│
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 4. Update UI        │                         │
        │◄────────────────────│                         │
```

**Checkout Process:**
1. Guest or authenticated checkout
2. Shipping/billing address collection
3. Payment method selection
4. Order validation
5. Payment processing (Stripe or mock)
6. Order confirmation
7. Digital delivery (e-books/audiobooks)
8. 3PL order submission (physical books)

### 2.6 Digital Content Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Purchase    │     │  Digital Hook    │     │  Digital Service │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. DRM-protected    │                         │
        │    content delivery │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Fetch from S3/     │
        │                     │    local storage      │
        │                     ├────────────────────────►│
        │                     │                         │
        │                     │ 3. Return file URL    │
        │                     │    + DRM license      │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 4. Load in reader/  │                         │
        │    player           │                         │
        │◄────────────────────│                         │
```

**Digital Features:**
- E-book reader with progression tracking
- Audiobook streaming with playback control
- DRM license management
- Cross-device sync (reading position)

### 2.7 Mobile Responsiveness

- Responsive grid (mobile-first)
- Touch-friendly components
- Optimized images (WebP, lazy loading)
- Minimum 10KB JS bundle size

---

## 3. Backend Modular Monolith Architecture

### 3.1 Module Structure

```
src/
├── modules/             # Business modules
│   ├── auth/            # Authentication module
│   │   ├── models/      # User, Session models
│   │   ├── services/    # AuthService, TokenService
│   │   ├── routes/      # /auth/* endpoints
│   │   ├── validators/  # Validation schemas
│   │   └── auth.js      # Module entry point
│   ├── catalog/         # Catalog module
│   │   ├── models/      # Book, Publisher models
│   │   ├── services/    # CatalogService, SearchService
│   │   ├── routes/      # /catalog/* endpoints
│   │   └── catalog.js   # Module entry point
│   ├── cart/            # Cart module
│   │   ├── models/      # Cart, CartItem models
│   │   ├── services/    # CartService
│   │   ├── routes/      # /cart/* endpoints
│   │   └── cart.js      # Module entry point
│   ├── checkout/        # Checkout module
│   │   ├── services/    # CheckoutService
│   │   ├── routes/      # /checkout/* endpoints
│   │   └── checkout.js  # Module entry point
│   ├── order/           # Order module
│   │   ├── models/      # Order, OrderItem models
│   │   ├── services/    # OrderService, FulfillmentService
│   │   ├── routes/      # /orders/* endpoints
│   │   └── order.js     # Module entry point
│   ├── digital/         # Digital delivery module
│   │   ├── services/    # DigitalDeliveryService
│   │   ├── routes/      # /digital/* endpoints
│   │   └── digital.js   # Module entry point
│   └── user/            # User profile module
│       ├── models/      # Profile, Address models
│       ├── services/    # UserService, ReviewService
│       ├── routes/      # /users/* endpoints
│       └── user.js      # Module entry point
├── shared/              # Shared cross-module code
│   ├── db/              # Database connection
│   │   └── index.js     # PostgreSQL client
│   ├── middleware/      # Express middleware
│   │   ├── auth.js      # JWT verification
│   │   ├── error.js     # Error handling
│   │   └── logging.js   # Request logging
│   ├── config/          # Configuration
│   │   └── index.js     # Environment config
│   ├── utils/           # Utility functions
│   │   ├── format.js    # Price, date formatting
│   │   └── validation.js # Validation helpers
│   └── providers/       # External provider interfaces
│       ├── PaymentProvider.js
│       ├── PaymentProvider.interface.js
│       ├── PaymentProvider.mock.js
│       ├── FulfillmentProvider.js
│       ├── FulfillmentProvider.interface.js
│       ├── FulfillmentProvider.mock.js
│       ├── DRMProvider.js
│       ├── DRMProvider.interface.js
│       └── DRMProvider.mock.js
├── app.js               # Express app setup
├── server.js            # Entry point
└── routes.js            # Route aggregation
```

### 3.2 Module Responsibilities

**Auth Module**
- User registration with email verification
- Login/logout with JWT tokens
- MFA setup and verification
- Password reset flow
- Social login (Google, Facebook)
- Session management

**Catalog Module**
- Book search (PostgreSQL FTS)
- Product catalog display
- Advanced filtering
- Book detail pages
- Publisher metadata
- Edition tracking

**Cart Module**
- Cart CRUD operations
- Persistent cart (localStorage or DB)
- Cart item management
- Price calculation
- Gift options

**Checkout Module**
- Checkout validation
- Payment processing
- Order creation
- Digital delivery
- 3PL order submission
- Order confirmation

**Order Module**
- Order history
- Order tracking
- Order status updates
- Return processing
- Refund handling

**Digital Module**
- E-book delivery
- Audiobook delivery
- DRM license generation
- Reader/player integration
- Progress tracking

**User Module**
- Profile management
- Address book
- Review submission
- Email preferences
- Reading history

### 3.3 Shared Services

**Authentication Service**
- `generateToken(userId, role)` - Create JWT token
- `verifyToken(token)` - Validate JWT token
- `hashPassword(password)` - Hash password using bcrypt/argon2
- `verifyPassword(password, hash)` - Verify password hash
- `createSession(userId, sessionId)` - Create session record
- `invalidateSession(sessionId)` - Invalidate session

**Database Service**
- `connect()` - Establish PostgreSQL connection
- `query(sql, params)` - Execute query
- `transaction(callback)` - Transaction wrapper
- `disconnect()` - Close connection

**Logging Service**
- `info(message, context)` - Info log
- `error(message, error, context)` - Error log
- `warn(message, context)` - Warning log
- `debug(message, context)` - Debug log (development only)

**Configuration Service**
- Load environment variables
- Validate required config
- Provide typed config objects

---

## 4. Database Architecture

### 4.1 PostgreSQL Schema Overview

```sql
-- Users
users (id, email, password_hash, first_name, last_name, created_at, updated_at)
user_profiles (id, user_id, phone, date_of_birth, preferences)
sessions (id, user_id, token_hash, expires_at, created_at)

-- Catalog
publishers (id, name, description, website)
books (id, isbn, title, description, publisher_id, publication_date, format, price, stock_quantity)
book_authors (book_id, author_name)
book_genres (book_id, genre)
inventory (book_id, warehouse_id, quantity)

-- Cart & Orders
carts (id, user_id, session_id, created_at, updated_at)
cart_items (id, cart_id, book_id, quantity, price_at_addition)
orders (id, user_id, guest_email, status, total_amount, shipping_address, billing_address, created_at)
order_items (id, order_id, book_id, quantity, unit_price)

-- Digital Delivery
digital_orders (order_id, file_url, drm_license, delivery_status, downloaded_at)

-- Reviews
reviews (id, user_id, order_id, rating, comment, helpful_count, created_at, updated_at)

-- Addresses
addresses (id, user_id, label, street, city, state, postal_code, country, is_default)

-- Email Preferences
email_preferences (user_id, marketing, order_notifications, recommendations, created_at, updated_at)
```

### 4.2 Indexes for Performance

```sql
-- Full-text search
CREATE INDEX idx_books_search ON books USING GIN (to_tsvector('english', title || ' ' || description));

-- User lookup
CREATE INDEX idx_users_email ON users(email);

-- Cart lookup
CREATE INDEX idx_carts_user_id ON carts(user_id);
CREATE INDEX idx_cart_items_cart_id ON cart_items(cart_id);

-- Order lookup
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);

-- Review lookup
CREATE INDEX idx_reviews_user_id ON reviews(user_id);
CREATE INDEX idx_reviews_order_id ON reviews(order_id);

-- Inventory lookup
CREATE INDEX idx_inventory_book_id ON inventory(book_id);
```

### 4.3 Data Consistency

- All related data in single database for simplicity
- Foreign key constraints for referential integrity
- Transactions for multi-step operations (checkout, order creation)
- Soft deletes for orders and reviews (preserve history)

---

## 5. REST API Architecture

### 5.1 API Design Principles

- RESTful resource-based design
- Consistent endpoint structure
- Standard HTTP methods (GET, POST, PUT, DELETE)
- JSON request/response bodies
- Proper HTTP status codes
- Error response format

### 5.2 Authentication

- JWT tokens in `Authorization: Bearer <token>` header
- Refresh tokens for session extension
- Token stored in memory (not localStorage)
- Protected routes require valid token

### 5.3 Endpoint Categories

```
Authentication:
  POST   /auth/register
  POST   /auth/login
  POST   /auth/logout
  POST   /auth/refresh
  POST   /auth/forgot-password
  POST   /auth/reset-password
  POST   /auth/mfa/verify
  POST   /auth/mfa/enable
  POST   /auth/mfa/disable

Catalog:
  GET    /catalog/search?q={query}&page={page}&limit={limit}&format={format}&genre={genre}&price_min={min}&price_max={max}
  GET    /catalog/books/{isbn}
  GET    /catalog/books/{isbn}/authors
  GET    /catalog/books/{isbn}/reviews
  GET    /catalog/categories
  GET    /catalog/publishers

Cart:
  GET    /cart
  POST   /cart/items
  PUT    /cart/items/{itemId}
  DELETE /cart/items/{itemId}
  POST   /cart/checkout

Orders:
  GET    /orders
  GET    /orders/{orderId}
  POST   /orders/{orderId}/fulfillment/digital
  GET    /orders/{orderId}/fulfillment/digital/{fileId}
  GET    /orders/{orderId}/tracking
  POST   /orders/{orderId}/returns

Digital:
  GET    /digital/orders/{orderId}/license
  GET    /digital/books/{isbn}/stream

User:
  GET    /users/me
  PUT    /users/me
  GET    /users/me/profile
  PUT    /users/me/profile
  GET    /users/me/addresses
  POST   /users/me/addresses
  PUT    /users/me/addresses/{addressId}
  DELETE /users/me/addresses/{addressId}
  GET    /users/me/reviews
  POST   /users/me/reviews
  PUT    /users/me/reviews/{reviewId}
  DELETE /users/me/reviews/{reviewId}
  GET    /users/me/email-preferences
  PUT    /users/me/email-preferences
```

### 5.4 Request/Response Examples

**Login Request:**
```json
POST /auth/login
{
  "email": "user@example.com",
  "password": "securePassword123"
}

Response (200):
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresAt": "2026-10-09T12:00:00Z",
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe"
  }
}
```

**Search Request:**
```json
GET /catalog/search?q=harry+potter&page=1&limit=20&format=physical&e-book

Response (200):
{
  "items": [
    {
      "isbn": "978-0743273565",
      "title": "Harry Potter and the Sorcerer's Stone",
      "author": "J.K. Rowling",
      "format": "physical",
      "price": 19.99,
      "coverImage": "https://cdn.example.com/books/978-0743273565.jpg"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "totalPages": 1
  }
}
```

**Error Response:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email or password",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address"
      }
    ]
  }
}
```

---

## 6. Authentication Flow

### 6.1 Registration Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Form        │     │  Auth Hook       │     │  AuthService     │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Submit form      │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Validate & hash    │
        │                     ├────────────────────────►│
        │                     │    password            │
        │                     │                         │
        │                     │ 3. Create user record │
        │                     │    (PostgreSQL)        │
        │                     │                         │
        │                     │ 4. Send verification  │
        │                     │    email               │
        │                     ├────────────────────────►│
        │                     │    (Email service)     │
        │                     │                         │
        │                     │ 5. Return success     │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 6. Show success     │                         │
        │◄────────────────────│                         │
```

### 6.2 Login Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Credentials │     │  Auth Hook       │     │  AuthService     │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Submit login     │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Verify password    │
        │                     ├────────────────────────►│
        │                     │    (bcrypt/argon2)     │
        │                     │                         │
        │                     │ 3. Create JWT token   │
        │                     │    + session record    │
        │                     │                         │
        │                     │ 4. Return token       │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 5. Store in memory  │                         │
        │◄────────────────────│                         │
```

### 6.3 Token Refresh Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Frontend    │     │  Frontend        │     │  Backend         │
│  Interceptor │     │  Auth Hook       │     │  AuthService     │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. API call with    │                         │
        │    expired token    │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Return 401         │
        │                     │◄────────────────────────┤
        │                     │    (Unauthorized)      │
        │                     │                         │
        │                     │ 3. Call refresh       │
        │                     ├────────────────────────►│
        │                     │    endpoint            │
        │                     │                         │
        │                     │ 4. Return new tokens  │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 5. Retry original   │                         │
        │    API call         │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 6. Return original    │
        │                     │    response            │
        │                     │◄────────────────────────┤
```

### 6.4 MFA Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  MFA Token   │     │  MFA Hook        │     │  AuthService     │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Enable MFA       │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Generate secret    │
        │                     ├────────────────────────►│
        │                     │    (TOTP)              │
        │                     │                         │
        │                     │ 3. Return QR code     │
        │                     │    data                │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 4. User scans QR    │                         │
        │    code             │                         │
        │◄────────────────────│                         │
        │                     │                         │
        │ 5. Submit token     │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 6. Verify token       │
        │                     ├────────────────────────►│
        │                     │                         │
        │                     │ 7. Enable MFA         │
        │                     │    (PostgreSQL)        │
        │                     │                         │
        │                     │ 8. Return success     │
        │                     │◄────────────────────────┤
```

---

## 7. Catalog/Search Flow

### 7.1 Search Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Search Box  │     │  Catalog Hook    │     │  SearchService   │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. User types       │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Build tsquery      │
        │                     ├────────────────────────►│
        │                     │    (PostgreSQL FTS)    │
        │                     │                         │
        │                     │ 3. Query with indexes │
        │                     ├────────────────────────►│
        │                     │    (GiST/GIN)          │
        │                     │                         │
        │                     │ 4. Return results     │
        │                     │◄────────────────────────┤
        │                     │    (ranked)            │
        │                     │                         │
        │ 5. Display results  │                         │
        │◄────────────────────│                         │
```

### 7.2 Advanced Filters

- **Format filter:** `WHERE books.format IN ('physical', 'e-book', 'audiobook')`
- **Genre filter:** `JOIN book_genres ON books.id = book_genres.book_id WHERE book_genres.genre = ?`
- **Price range:** `WHERE books.price BETWEEN ? AND ?`
- **Publication date:** `WHERE books.publication_date >= ?`

### 7.3 Relevance Ranking

PostgreSQL full-text search ranking:
1. Exact title match (highest weight)
2. Author name match
3. Description match
4. ISBN match
5. Partial title match (lower weight)

---

## 8. Cart and Checkout Flow

### 8.1 Cart Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Add Item    │     │  Cart Hook       │     │  CartService     │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Click add        │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Validate stock     │
        │                     ├────────────────────────►│
        │                     │    (PostgreSQL)        │
        │                     │                         │
        │                     │ 3. Create/update      │
        │                     │    cart item           │
        │                     │                         │
        │                     │ 4. Return updated     │
        │                     │    cart                │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 5. Update UI        │                         │
        │◄────────────────────│                         │
```

### 8.2 Checkout Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Checkout    │     │  Checkout Hook   │     │  CheckoutService │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Submit checkout  │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Validate order     │
        │                     ├────────────────────────►│
        │                     │    (stock, price, etc.)│
        │                     │                         │
        │                     │ 3. Process payment    │
        │                     ├────────────────────────►│
        │                     │    (Stripe/Mock)       │
        │                     │                         │
        │                     │ 4. Create order       │
        │                     │    (PostgreSQL)        │
        │                     │                         │
        │                     │ 5. Submit to 3PL      │
        │                     ├────────────────────────►│
        │                     │    (Fulfillment API)   │
        │                     │                         │
        │                     │ 6. Return order       │
        │                     │    confirmation        │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 7. Show success     │                         │
        │◄────────────────────│                         │
```

### 8.3 Payment Integration

- **Payment Provider Interface:** `PaymentProvider`
- **Implementation:** `MockPaymentProvider` (local), `StripePaymentProvider` (production)
- **Configuration:** `PAYMENT_PROVIDER` env variable
- **Methods:** `charge(card, amount, currency)`, `refund(orderId)`

### 8.4 Digital Delivery

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Purchase    │     │  Digital Hook    │     │  DigitalService  │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Order complete   │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Generate DRM       │
        │                     ├────────────────────────►│
        │                     │    license             │
        │                     │                         │
        │                     │ 3. Return download    │
        │                     │    URL                 │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 4. Load in reader   │                         │
        │◄────────────────────│                         │
```

---

## 9. Payment Flow

### 9.1 Payment Processing Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  User        │     │  Frontend        │     │  Backend         │
│  Payment     │     │  Checkout Hook   │     │  CheckoutService │
│  Form        │     │                   │     │                   │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Submit payment   │                         │
        │    details          │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Validate & tokenize│
        │                     ├────────────────────────►│
        │                     │    (Stripe SDK)        │
        │                     │                         │
        │                     │ 3. Charge card        │
        │                     ├────────────────────────►│
        │                     │    (Stripe API)        │
        │                     │                         │
        │                     │ 4. Return charge      │
        │                     │    result              │
        │                     │◄────────────────────────┤
        │                     │                         │
        │                     │ 5. Create order       │
        │                     ├────────────────────────►│
        │                     │    (PostgreSQL)        │
        │                     │                         │
        │                     │ 6. Return order       │
        │                     │    confirmation        │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 7. Show success     │                         │
        │◄────────────────────│                         │
```

### 9.2 PCI Compliance

- No card data touches backend
- Stripe Elements or similar for secure card input
- Tokenization via payment provider
- PCI DSS Level 1 compliance via provider

---

## 10. Order and Fulfillment Flow

### 10.1 Order Fulfillment Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Backend     │     │  CheckoutService │     │  3PL Provider    │
│  Order       │     │                   │     │  Interface       │
│  Creation    │     │                   │     │                  │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Order created    │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Submit order       │
        │                     ├────────────────────────►│
        │                     │    (POST /orders)      │
        │                     │                         │
        │                     │ 3. Return order ID    │
        │                     │    + tracking          │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 4. Update order     │                         │
        │    with tracking    │                         │
        ├────────────────────►│                         │
        │                     │                         │
```

### 10.2 Inventory Sync

- **Schedule:** Every 15 minutes via cron or scheduled task
- **Method:** Poll 3PL API for inventory updates
- **Storage:** PostgreSQL `inventory` table
- **Graceful degradation:** Continue with inventory estimate if 3PL unavailable

---

## 11. Digital Content/DRM Flow

### 11.1 DRM Provider Interface

```
interface DRMProvider {
  generateLicense(bookId: string, userId: string): Promise<DRMLicense>;
  validateLicense(license: string): Promise<boolean>;
  revokeLicense(bookId: string, userId: string): Promise<void>;
}
```

### 11.2 Digital Delivery Flow

```
┌──────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Backend     │     │  DigitalService  │     │  DRM Provider    │
│  Order       │     │                   │     │  Interface       │
│  Completion  │     │                   │     │                  │
└──────────────┘     └──────────────────┘     └──────────────────┘
        │                     │                         │
        │ 1. Order complete   │                         │
        ├────────────────────►│                         │
        │                     │                         │
        │                     │ 2. Generate DRM       │
        │                     ├────────────────────────►│
        │                     │    license             │
        │                     │                         │
        │                     │ 3. Return license     │
        │                     │    + file URL          │
        │                     │◄────────────────────────┤
        │                     │                         │
        │ 4. Store delivery │
        │    record           │                         │
        ├────────────────────►│                         │
```

### 11.3 Mock DRM Provider

- Local file-based license generation
- Simple JWT-based license validation
- No external dependencies

---

## 12. Security

### 12.1 Authentication Security

- JWT tokens with 24-hour expiry
- Refresh tokens with 7-day expiry
- Password hashing using bcrypt (cost: 12) or argon2
- Session invalidation on logout
- Rate limiting for auth endpoints

### 12.2 Data Security

- TLS 1.3 for all traffic
- AES-256 encryption at rest (PostgreSQL TDE or disk encryption)
- Parameterized queries to prevent SQL injection
- Input validation on all endpoints
- XSS prevention via React's escaping + CSP headers

### 12.3 Payment Security

- PCI DSS Level 1 compliance via Stripe
- No card data stored
- Tokenization for saved payment methods

### 12.4 Access Control

- Role-based access control (RBAC)
- User roles: `customer`, `admin`
- Route-level protection
- Resource-level authorization

---

## 13. Error Handling

### 13.1 Error Response Format

```json
{
  "error": {
    "code": "STRING_CODE",
    "message": "Human-readable message",
    "details": [
      {
        "field": "field_name",
        "message": "Specific error for field"
      }
    ],
    "requestId": "uuid"
  }
}
```

### 13.2 HTTP Status Codes

| Code | Use Case |
|------|----------|
| 400 | Bad request (validation error) |
| 401 | Unauthorized (invalid/missing token) |
| 403 | Forbidden (insufficient permissions) |
| 404 | Not found |
| 409 | Conflict (duplicate resource) |
| 422 | Unprocessable entity (validation) |
| 429 | Rate limit exceeded |
| 500 | Internal server error |
| 503 | Service unavailable |

### 13.3 Error Codes

| Code | Description |
|------|-------------|
| `VALIDATION_ERROR` | Request validation failed |
| `AUTHENTICATION_ERROR` | Invalid or expired token |
| `AUTHORIZATION_ERROR` | Insufficient permissions |
| `NOT_FOUND` | Resource does not exist |
| `CONFLICT` | Resource already exists |
| `INTERNAL_ERROR` | Unexpected server error |
| `SERVICE_UNAVAILABLE` | External service unavailable |

---

## 14. Local Development Architecture

### 14.1 Development Environment

**Prerequisites:**
- Node.js 20+
- Docker Desktop (for PostgreSQL)
- npm or yarn

**Startup:**
```bash
# Start PostgreSQL
docker-compose up -d postgres

# Install dependencies
npm install

# Start development servers
npm run dev:frontend  # Vite dev server on :5173
npm run dev:backend   # Express server on :3000
```

### 14.2 Development Configuration

```env
# Backend (.env)
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/anycompanyread
JWT_SECRET=your-dev-secret-key
JWT_EXPIRES_IN=24h
PAYMENT_PROVIDER=mock
FULFILLMENT_PROVIDER=mock
DRM_PROVIDER=mock

# Frontend (.env)
VITE_API_URL=http://localhost:3000
```

### 14.3 Mock Providers

| Provider | Local Implementation | Configuration |
|----------|---------------------|---------------|
| Payment | `MockPaymentProvider` | `PAYMENT_PROVIDER=mock` |
| Fulfillment | `Mock3PLProvider` | `FULFILLMENT_PROVIDER=mock` |
| DRM | `MockDRMProvider` | `DRM_PROVIDER=mock` |

### 14.4 Database Management

- **Migration tool:** `node-pg-migrate` or manual SQL
- **Seeding:** `db-seed` script
- **Reset:** `db-reset` script

---

## 15. Deployment Portability

### 15.1 Cloud Deployment (Future)

**Target:** AWS ECS/Fargate

**Configuration differences:**
```env
NODE_ENV=production
DATABASE_URL=postgresql://user:password@rds-endpoint:5432/anycompanyread
JWT_SECRET=env:JWT_SECRET  # From AWS Secrets Manager
PAYMENT_PROVIDER=stripe
FULFILLMENT_PROVIDER=real-3pl
DRM_PROVIDER=real-drm
```

### 15.2 Containerization

**Dockerfile:**
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

**Docker Compose:**
```yaml
version: '3.8'
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: anycompanyread
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

---

## 16. Technology Stack

| Component | Technology | Version | Notes |
|-----------|------------|---------|-------|
| Frontend | React | 18.x | UI framework |
| Frontend | Vite | 5.x | Build tool |
| Frontend | React Query | 4.x | Data fetching |
| Backend | Node.js | 20.x | Runtime |
| Backend | Express | 4.x | Web framework |
| Backend | pino | 8.x | Logging |
| Database | PostgreSQL | 15.x | Data storage |
| Auth | jsonwebtoken | 9.x | JWT handling |
| Auth | bcrypt | 5.x | Password hashing |
| Testing | Jest | 29.x | Unit tests |
| Testing | supertest | 6.x | API tests |
| Development | Docker Desktop | Latest | PostgreSQL |

---

## 17. Architectural Decisions and Trade-offs

### 17.1 Local-First Runtime

**Decision:** MVP runs locally without AWS infrastructure

**Trade-offs:**
- ✅ Lower barrier to entry (no AWS account needed)
- ✅ Faster local development (no cloud provisioning)
- ✅ Easier demonstration and testing
- ❌ Cloud deployment requires additional infrastructure setup
- ❌ Some AWS-native optimizations not available in MVP

**Mitigation:** Clean separation between local and cloud code paths

### 17.2 Modular Monolith

**Decision:** Single application with domain-based modules

**Trade-offs:**
- ✅ Simpler deployment and development
- ✅ Easier debugging and monitoring
- ✅ Clear domain boundaries for future decomposition
- ❌ Limited horizontal scaling per module
- ❌ Single point of failure (entire app)

**Mitigation:** Module boundaries designed for easy extraction

### 17.3 PostgreSQL Full-Text Search

**Decision:** Use PostgreSQL FTS for MVP search

**Trade-offs:**
- ✅ No additional infrastructure
- ✅ Single database for all data
- ✅ Good enough for 500K+ SKUs
- ❌ Less powerful than OpenSearch
- ❌ Limited advanced features (autocomplete, faceting)

**Mitigation:** PostgreSQL FTS with proper indexes meets MVP requirements

### 17.4 Application-managed Authentication

**Decision:** JWT tokens with bcrypt password hashing

**Trade-offs:**
- ✅ No AWS Cognito dependency
- ✅ Full control over auth flow
- ✅ Easier local development
- ❌ Need to implement password reset, MFA
- ❌ Missing Cognito's built-in security features

**Mitigation:** Well-established JWT patterns; cloud replacement easy

### 17.5 Mock Providers for External Integrations

**Decision:** Local mock implementations for payments, fulfillment, DRM

**Trade-offs:**
- ✅ No external credentials needed for MVP
- ✅ Faster local development
- ✅ Easier testing
- ❌ Production requires real provider integration
- ❌ Potential bugs from mock vs real provider differences

**Mitigation:** Clear provider interfaces; strict contract testing

---

## 18. Next Steps

1. **Design Phase - Component Design**
   - Create detailed component diagrams
   - Define API contracts (OpenAPI specification)
   - Design database schema in detail

2. **Design Phase - Implementation Planning**
   - Break down modules into implementation tasks
   - Identify dependencies between modules
   - Create Units of Work (UOW)

3. **Design Phase - UI/UX**
   - Wireframes for key user journeys
   - Design system documentation
   - Accessibility review

---

*End of Application Design Document*
