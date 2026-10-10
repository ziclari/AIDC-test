# AnyCompanyRead - Services Catalog

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
| Reviewers | Engineering, Architecture |
| Next Review | Human approval |

---

## 1. Overview

This document catalogs all services in the AnyCompanyRead system, organized by type.

**Service Categories:**
- **Application Services:** Core business logic services
- **Infrastructure Services:** Supporting services (auth, logging, database)
- **Integration Services:** External provider services
- **Local Services:** Local-only implementations
- **Mock Services:** Mock implementations for local development

---

## 2. Application Services

### 2.1 Authentication Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `AuthService` | `modules/auth/services/AuthService.js` | User model, Session model, TokenService, EmailService | User registration, login, password reset, MFA |
| `TokenService` | `modules/auth/services/TokenService.js` | Config | JWT token generation and validation |

#### AuthService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `register(userData)` | `userData: object` | `Promise<User>` | Create new user account |
| `login(email, password)` | `email: string`, `password: string` | `Promise<LoginResult>` | Authenticate user |
| `logout(userId)` | `userId: string` | `Promise<void>` | Invalidate user session |
| `refreshToken(refreshToken)` | `refreshToken: string` | `Promise<TokenResult>` | Generate new access token |
| `requestPasswordReset(email)` | `email: string` | `Promise<void>` | Initiate password reset |
| `completePasswordReset(resetToken, newPassword)` | `resetToken: string`, `newPassword: string` | `Promise<void>` | Complete password reset |
| `verifyMFA(userId, code)` | `userId: string`, `code: string` | `Promise<void>` | Verify MFA code |
| `enableMFA(userId, secret)` | `userId: string`, `secret: string` | `Promise<void>` | Enable MFA |
| `disableMFA(userId)` | `userId: string` | `Promise<void>` | Disable MFA |

#### TokenService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `generateToken(userId, role)` | `userId: string`, `role: string` | `Promise<string>` | Generate access token |
| `generateRefreshToken(userId)` | `userId: string` | `Promise<string>` | Generate refresh token |
| `verifyToken(token)` | `token: string` | `Promise<object>` | Verify access token |
| `verifyRefreshToken(token)` | `token: string` | `Promise<object>` | Verify refresh token |

#### Service Dependencies

```
AuthService
├── User model (PostgreSQL)
├── Session model (PostgreSQL)
├── TokenService
│   └── config (JWT secret)
└── EmailService (external - SendGrid/Mailgun)
```

#### Configuration

| Environment Variable | Purpose |
|---------------------|---------|
| `JWT_SECRET` | Secret key for JWT token signing |
| `JWT_EXPIRES_IN` | Token expiry time (default: 24h) |
| `REFRESH_TOKEN_EXPIRES_IN` | Refresh token expiry time (default: 7d) |
| `EMAIL_SERVICE` | Email provider (sendgrid, mailgun) |
| `EMAIL_API_KEY` | API key for email provider |

---

### 2.2 Catalog Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `CatalogService` | `modules/catalog/services/CatalogService.js` | Book model, Publisher model | Book CRUD operations |
| `SearchService` | `modules/catalog/services/SearchService.js` | CatalogService, Database | Full-text search using PostgreSQL |

#### CatalogService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `searchBooks(query, filters)` | `query: string`, `filters: object` | `Promise<Book[]>` | Search books |
| `getBookByISBN(isbn)` | `isbn: string` | `Promise<Book>` | Get book by ISBN |
| `getBookAuthors(isbn)` | `isbn: string` | `Promise<Author[]>` | Get book authors |
| `getBookReviews(isbn)` | `isbn: string` | `Promise<Review[]>` | Get book reviews |
| `getCategories()` | None | `Promise<Category[]>` | Get all categories |
| `getPublishers()` | None | `Promise<Publisher[]>` | Get all publishers |

#### SearchService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `fullTextSearch(query, filters)` | `query: string`, `filters: object` | `Promise<Book[]>` | PostgreSQL FTS search |
| `buildSearchQuery(query, filters)` | `query: string`, `filters: object` | `Promise<object>` | Build SQL query |
| `rankResults(results, query)` | `results: object[]`, `query: string` | `Promise<object[]>` | Rank by relevance |

#### Service Dependencies

```
CatalogService
└── Book model (PostgreSQL)

SearchService
├── CatalogService
└── Database (PostgreSQL FTS)
```

---

### 2.3 Cart Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `CartService` | `modules/cart/services/CartService.js` | Cart model, CartItem model | Cart operations |

#### CartService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getCart(userId)` | `userId: string` | `Promise<Cart>` | Get user's cart |
| `addToCart(userId, bookId, quantity)` | `userId: string`, `bookId: string`, `quantity: number` | `Promise<CartItem>` | Add item to cart |
| `updateCartItem(userId, itemId, quantity)` | `userId: string`, `itemId: string`, `quantity: number` | `Promise<CartItem>` | Update item quantity |
| `removeFromCart(userId, itemId)` | `userId: string`, `itemId: string` | `Promise<void>` | Remove item from cart |
| `clearCart(userId)` | `userId: string` | `Promise<void>` | Clear user's cart |
| `calculateTotals(cartItems)` | `cartItems: CartItem[]` | `Promise<object>` | Calculate subtotal, tax, total |

#### Service Dependencies

```
CartService
├── Cart model (PostgreSQL)
└── CartItem model (PostgreSQL)
```

---

### 2.4 Checkout Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `CheckoutService` | `modules/checkout/services/CheckoutService.js` | CartService, PaymentProvider, OrderService, FulfillmentProvider, DigitalDeliveryService | Checkout orchestration |

#### CheckoutService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `validateOrder(cart, shippingAddress)` | `cart: Cart`, `shippingAddress: object` | `Promise<boolean>` | Validate order |
| `processPayment(cart, paymentMethod)` | `cart: Cart`, `paymentMethod: object` | `Promise<PaymentResult>` | Process payment |
| `createOrder(cart, paymentResult, shippingAddress)` | `cart: Cart`, `paymentResult: object`, `shippingAddress: object` | `Promise<Order>` | Create order |
| `fulfillPhysicalOrder(order)` | `order: Order` | `Promise<void>` | Fulfill physical order |
| `fulfillDigitalOrder(order)` | `order: Order` | `Promise<void>` | Fulfill digital order |
| `sendOrderConfirmation(order)` | `order: Order` | `Promise<void>` | Send confirmation email |

#### Service Dependencies

```
CheckoutService
├── CartService
├── PaymentProvider (interface)
├── OrderService
├── FulfillmentProvider (interface)
└── DigitalDeliveryService
```

#### External Integrations

| Provider | Interface | Implementation | Purpose |
|----------|-----------|----------------|---------|
| Payment | `PaymentProvider` | `MockPaymentProvider` or `StripePaymentProvider` | Payment processing |
| Fulfillment | `FulfillmentProvider` | `Mock3PLProvider` or `Real3PLProvider` | Physical order fulfillment |

---

### 2.5 Order Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `OrderService` | `modules/order/services/OrderService.js` | Order model, OrderItem model | Order management |
| `FulfillmentService` | `modules/order/services/FulfillmentService.js` | OrderService, FulfillmentProvider | Order fulfillment orchestration |

#### OrderService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getOrderHistory(userId)` | `userId: string` | `Promise<Order[]>` | Get user's order history |
| `getOrderById(orderId)` | `orderId: string` | `Promise<Order>` | Get single order |
| `updateOrderStatus(orderId, status)` | `orderId: string`, `status: string` | `Promise<void>` | Update order status |
| `initiateReturn(orderId, reason)` | `orderId: string`, `reason: string` | `Promise<void>` | Initiate return |
| `getTracking(orderId)` | `orderId: string` | `Promise<TrackingInfo>` | Get tracking info |

#### FulfillmentService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `submitOrder(order)` | `order: Order` | `Promise<FulfillmentResult>` | Submit order to 3PL |
| `getTracking(trackingNumber)` | `trackingNumber: string` | `Promise<TrackingInfo>` | Get tracking info |
| `syncInventory()` | None | `Promise<void>` | Sync inventory from 3PL |

#### Service Dependencies

```
OrderService
├── Order model (PostgreSQL)
└── OrderItem model (PostgreSQL)

FulfillmentService
├── OrderService
└── FulfillmentProvider (interface)
```

---

### 2.6 Digital Delivery Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `DigitalDeliveryService` | `modules/digital/services/DigitalDeliveryService.js` | DigitalOrder model, DRMProvider | Digital content delivery |

#### DigitalDeliveryService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `generateLicense(order)` | `order: Order` | `Promise<DRMLicense>` | Generate DRM license |
| `validateLicense(license)` | `license: string` | `Promise<boolean>` | Validate license |
| `getStreamURL(isbn)` | `isbn: string` | `Promise<string>` | Get audiobook stream URL |
| `trackDownload(orderId)` | `orderId: string` | `Promise<void>` | Track digital download |

#### Service Dependencies

```
DigitalDeliveryService
├── DigitalOrder model (PostgreSQL)
└── DRMProvider (interface)
```

---

### 2.7 User Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `UserService` | `modules/user/services/UserService.js` | UserProfile model, Address model, Review model | User profile management |

#### UserService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getProfile(userId)` | `userId: string` | `Promise<UserProfile>` | Get user profile |
| `updateProfile(userId, data)` | `userId: string`, `data: object` | `Promise<UserProfile>` | Update user profile |
| `getAddresses(userId)` | `userId: string` | `Promise<Address[]>` | Get user's addresses |
| `addAddress(userId, address)` | `userId: string`, `address: object` | `Promise<Address>` | Add address |
| `updateAddress(userId, addressId, data)` | `userId: string`, `addressId: string`, `data: object` | `Promise<Address>` | Update address |
| `deleteAddress(userId, addressId)` | `userId: string`, `addressId: string` | `Promise<void>` | Delete address |
| `getReviews(userId)` | `userId: string` | `Promise<Review[]>` | Get user's reviews |
| `submitReview(userId, orderId, rating, comment)` | `userId: string`, `orderId: string`, `rating: number`, `comment: string` | `Promise<Review>` | Submit review |
| `updateReview(userId, reviewId, data)` | `userId: string`, `reviewId: string`, `data: object` | `Promise<Review>` | Update review |
| `deleteReview(userId, reviewId)` | `userId: string`, `reviewId: string` | `Promise<void>` | Delete review |

#### Service Dependencies

```
UserService
├── UserProfile model (PostgreSQL)
├── Address model (PostgreSQL)
└── Review model (PostgreSQL)
```

---

## 3. Infrastructure Services

### 3.1 Database Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `db` | `shared/db/index.js` | `pg` library | PostgreSQL connection pool |

#### db Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `connect()` | None | `Promise<void>` | Establish connection pool |
| `query(sql, params)` | `sql: string`, `params: any[]` | `Promise<QueryResult>` | Execute SQL query |
| `transaction(callback)` | `callback: Function` | `Promise<void>` | Transaction wrapper |
| `disconnect()` | None | `Promise<void>` | Close connection pool |

---

### 3.2 Configuration Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `config` | `shared/config/index.js` | None | Environment configuration loader |

#### Configuration Values

| Environment Variable | Purpose | Required |
|---------------------|---------|----------|
| `NODE_ENV` | Environment (development, production) | Yes |
| `PORT` | Server port | Yes |
| `DATABASE_URL` | PostgreSQL connection string | Yes |
| `JWT_SECRET` | Secret for JWT signing | Yes |
| `JWT_EXPIRES_IN` | Access token expiry | Yes |
| `REFRESH_TOKEN_EXPIRES_IN` | Refresh token expiry | Yes |
| `PAYMENT_PROVIDER` | Payment provider (mock, stripe) | Yes |
| `FULFILLMENT_PROVIDER` | Fulfillment provider (mock, real-3pl) | Yes |
| `DRM_PROVIDER` | DRM provider (mock, real-drm) | Yes |
| `STRIPE_API_KEY` | Stripe API key | No (mock only) |
| `3PL_API_KEY` | 3PL API key | No (mock only) |
| `DRM_API_KEY` | DRM API key | No (mock only) |

---

### 3.3 Logging Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `logger` | `shared/middleware/logging.js` | `pino` library | Structured JSON logging |

#### Logger Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `info(message, context)` | `message: string`, `context: object` | `void` | Info level log |
| `error(message, error, context)` | `message: string`, `error: object`, `context: object` | `void` | Error level log |
| `warn(message, context)` | `message: string`, `context: object` | `void` | Warning level log |
| `debug(message, context)` | `message: string`, `context: object` | `void` | Debug level log |

---

### 3.4 Email Service

| Service | File Path | Dependencies | Purpose |
|---------|-----------|--------------|---------|
| `emailService` | `shared/services/EmailService.js` | Email provider SDK | Email sending |

#### EmailService Methods

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `sendEmail(to, subject, template, data)` | `to: string`, `subject: string`, `template: string`, `data: object` | `Promise<void>` | Send email |

#### Provider Options

| Provider | SDK | Configuration |
|----------|-----|---------------|
| SendGrid | `@sendgrid/mail` | `SENDGRID_API_KEY` |
| Mailgun | `mailgun-js` | `MAILGUN_API_KEY`, `MAILGUN_DOMAIN` |

---

## 4. Integration Services

### 4.1 Payment Provider Interface

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `charge(cardToken, amount, currency)` | `cardToken: string`, `amount: number`, `currency: string` | `Promise<ChargeResult>` | Charge payment |
| `refund(orderId, amount)` | `orderId: string`, `amount: number` | `Promise<RefundResult>` | Refund payment |
| `validateCard(cardDetails)` | `cardDetails: object` | `Promise<boolean>` | Validate card format |
| `getToken(cardDetails)` | `cardDetails: object` | `Promise<string>` | Get token for saved card |

#### Implementation 1: MockPaymentProvider (Local)

| Method | Implementation |
|--------|----------------|
| `charge` | Returns mock transaction ID, simulates success |
| `refund` | Returns mock refund ID, simulates success |
| `validateCard` | Basic format validation (Luhn check) |
| `getToken` | Returns deterministic token for test card |

#### Implementation 2: StripePaymentProvider (Production)

| Method | Implementation |
|--------|----------------|
| `charge` | Uses Stripe Payments API |
| `refund` | Uses Stripe Refunds API |
| `validateCard` | Uses Stripe Card Validation |
| `getToken` | Uses Stripe Tokens API |

#### Configuration

```env
PAYMENT_PROVIDER=stripe
STRIPE_API_KEY=sk_live_xxxxxxxxxxxxxx
```

---

### 4.2 Fulfillment Provider Interface

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getInventory(bookIds)` | `bookIds: string[]` | `Promise<InventoryStatus[]>` | Get inventory status |
| `submitOrder(orderData)` | `orderData: object` | `Promise<SubmissionResult>` | Submit order to 3PL |
| `getTracking(trackingNumber)` | `trackingNumber: string` | `Promise<TrackingInfo>` | Get shipment tracking |
| `syncInventory()` | None | `Promise<void>` | Sync inventory from 3PL |

#### Implementation 1: Mock3PLProvider (Local)

| Method | Implementation |
|--------|----------------|
| `getInventory` | Returns mock inventory (100+ units per book) |
| `submitOrder` | Returns mock tracking number |
| `getTracking` | Returns mock shipment status |
| `syncInventory` | Logs inventory sync |

#### Implementation 2: Real3PLProvider (Production)

| Method | Implementation |
|--------|----------------|
| `getInventory` | Calls 3PL API for inventory |
| `submitOrder` | Submits order to 3PL API |
| `getTracking` | Calls 3PL API for tracking |
| `syncInventory` | Polls 3PL API every 15 minutes |

#### Configuration

```env
FULFILLMENT_PROVIDER=real-3pl
3PL_API_KEY=your_3pl_api_key
```

---

### 4.3 DRM Provider Interface

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `generateLicense(bookId, userId)` | `bookId: string`, `userId: string` | `Promise<DRMLicense>` | Generate DRM license |
| `validateLicense(license)` | `license: string` | `Promise<boolean>` | Validate license |
| `revokeLicense(bookId, userId)` | `bookId: string`, `userId: string` | `Promise<void>` | Revoke license |

#### Implementation 1: MockDRMProvider (Local)

| Method | Implementation |
|--------|----------------|
| `generateLicense` | Creates JWT with 24-hour expiry, device limit 3 |
| `validateLicense` | Validates JWT signature and expiry |
| `revokeLicense` | Adds license to revocation list in memory |

#### Implementation 2: RealDRMProvider (Production)

| Method | Implementation |
|--------|----------------|
| `generateLicense` | Calls DRM provider API |
| `validateLicense` | Calls DRM provider API |
| `revokeLicense` | Calls DRM provider API |

#### Configuration

```env
DRM_PROVIDER=real-drm
DRM_API_KEY=your_drm_api_key
```

---

## 5. Local Services

### 5.1 Local Mock Services

| Service | File Path | Purpose |
|---------|-----------|---------|
| `MockPaymentProvider` | `shared/providers/PaymentProvider.mock.js` | Local payment processing simulation |
| `Mock3PLProvider` | `shared/providers/FulfillmentProvider.mock.js` | Local fulfillment simulation |
| `MockDRMProvider` | `shared/providers/DRMProvider.mock.js` | Local DRM license generation |

### 5.2 Local Configuration

```env
# Use local mock implementations
PAYMENT_PROVIDER=mock
FULFILLMENT_PROVIDER=mock
DRM_PROVIDER=mock

# Development database
DATABASE_URL=postgresql://user:password@localhost:5432/anycompanyread
```

---

## 6. Service Dependencies Graph

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        Service Dependencies                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Application Services                                                       │
│  ┌────────────┐     ┌────────────┐     ┌────────────┐                     │
│  │  AuthService │────▶│ CatalogService│────▶│ CartService │               │
│  └────────────┘     └────────────┘     └────────────┘                     │
│         │                  │                  │                           │
│         │                  ▼                  ▼                           │
│         │            ┌────────────┐     ┌────────────┐                    │
│         └───────────▶│ CheckoutService│◀───│ FulfillmentService│          │
│                      └────────────┘     └────────────┘                    │
│                             │                  │                           │
│                             ▼                  ▼                           │
│                      ┌────────────┐     ┌────────────┐                    │
│                      │ OrderService │   │DigitalDeliveryService│          │
│                      └────────────┘     └────────────┘                    │
│                             │                                             │
│                             ▼                                             │
│                      ┌────────────┐                                       │
│                      │ UserService  │                                       │
│                      └────────────┘                                       │
│                                                                             │
│  Infrastructure Services                                                    │
│  ┌────────────┐     ┌────────────┐     ┌────────────┐                     │
│  │   db       │     │  config    │     │ logger     │                     │
│  └────────────┘     └────────────┘     └────────────┘                     │
│                                                                             │
│  Integration Services                                                       │
│  ┌────────────┐     ┌────────────┐     ┌────────────┐                     │
│  │PaymentProvider│  │FulfillmentProvider│  │ DRMProvider │               │
│  └────────────┘     └────────────┘     └────────────┘                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Service Lifecycle

### 7.1 Application Startup

```
1. Load configuration (config/index.js)
2. Connect to database (db/index.js)
3. Initialize logger (logging middleware)
4. Start Express server (app.js)
5. Load route handlers (routes.js)
6. Mount middleware (middleware chain)
```

### 7.2 Request Handling Flow

```
1. Request arrives at Express
2. Run middleware (logging, auth, etc.)
3. Match route
4. Execute route handler
5. Service layer processes request
6. Database query (if needed)
7. Response sent
```

### 7.3 Shutdown Flow

```
1. Stop accepting new requests
2. Close database connections
3. Close external provider connections
4. Flush pending logs
```

---

## 8. Service Configuration Summary

### 8.1 Environment Variables

| Category | Variables |
|----------|-----------|
| **App** | `NODE_ENV`, `PORT` |
| **Database** | `DATABASE_URL` |
| **Auth** | `JWT_SECRET`, `JWT_EXPIRES_IN`, `REFRESH_TOKEN_EXPIRES_IN` |
| **Email** | `EMAIL_SERVICE`, `EMAIL_API_KEY` |
| **Payments** | `PAYMENT_PROVIDER`, `STRIPE_API_KEY` |
| **Fulfillment** | `FULFILLMENT_PROVIDER`, `3PL_API_KEY` |
| **DRM** | `DRM_PROVIDER`, `DRM_API_KEY` |

### 8.2 Local Development Environment

```env
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/anycompanyread
JWT_SECRET=your-dev-secret-key
JWT_EXPIRES_IN=24h
REFRESH_TOKEN_EXPIRES_IN=7d
EMAIL_SERVICE=mailtrap
EMAIL_API_KEY=your-mailtrap-api-key
PAYMENT_PROVIDER=mock
FULFILLMENT_PROVIDER=mock
DRM_PROVIDER=mock
```

### 8.3 Production Environment

```env
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://user:password@rds-endpoint:5432/anycompanyread
JWT_SECRET=your-production-secret-key
JWT_EXPIRES_IN=24h
REFRESH_TOKEN_EXPIRES_IN=7d
EMAIL_SERVICE=sendgrid
EMAIL_API_KEY=your-sendgrid-api-key
PAYMENT_PROVIDER=stripe
STRIPE_API_KEY=sk_live_xxxxxxxxxxxxxx
FULFILLMENT_PROVIDER=real-3pl
3PL_API_KEY=your-3pl-api-key
DRM_PROVIDER=real-drm
DRM_API_KEY=your-drm-api-key
```

---

## 9. Service Testing

### 9.1 Service Testing Strategy

| Service Type | Testing Approach | Tools |
|--------------|------------------|-------|
| Application Services | Unit tests | Jest |
| Infrastructure Services | Integration tests | Jest + mock |
| External Providers | Integration tests | Jest + mock HTTP server |

### 9.2 Test Coverage Targets

| Component | Coverage Target |
|-----------|-----------------|
| Application Services | 80%+ |
| Infrastructure Services | 70%+ |
| External Provider Interfaces | 90%+ (contracts) |

---

*End of Services Catalog*
