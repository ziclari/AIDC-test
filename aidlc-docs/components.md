# AnyCompanyRead - Components Catalog

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

This document catalogs all components in the AnyCompanyRead system, organized by type and responsibility.

**Component Categories:**
- **Frontend Components:** React components for the user interface
- **Backend Components:** Express modules and services
- **Database Components:** PostgreSQL tables and views
- **Integration Components:** External provider interfaces and implementations

---

## 2. Frontend Components

### 2.1 Component Hierarchy

```
App
├── Layout
│   ├── Header
│   ├── Navigation
│   ├── MainContent
│   └── Footer
├── Auth
│   ├── Login
│   ├── Register
│   ├── ForgotPassword
│   ├── ResetPassword
│   └── MFASetup
├── Catalog
│   ├── SearchBar
│   ├── ProductGrid
│   ├── ProductCard
│   ├── ProductDetail
│   ├── FilterPanel
│   └── CategoryNav
├── Cart
│   ├── CartSummary
│   ├── CartItem
│   ├── CheckoutForm
│   └── PaymentForm
├── Order
│   ├── OrderHistory
│   ├── OrderDetail
│   └── OrderTracking
├── User
│   ├── Profile
│   ├── AddressBook
│   ├── EmailPreferences
│   └── ReviewList
├── Digital
│   ├── EBookReader
│   ├── AudiobookPlayer
│   └── DigitalDelivery
└── common
    ├── Button
    ├── Input
    ├── Modal
    ├── Toast
    └── LoadingSpinner
```

### 2.2 Frontend Component Catalog

#### Layout Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `App` | `src/App.js` | Root component, route configuration | None |
| `Header` | `src/components/layout/Header.js` | Site header with logo, navigation | `Navigation` |
| `Navigation` | `src/components/layout/Navigation.js` | Top navigation bar, user menu | `useAuth` |
| `MainContent` | `src/components/layout/MainContent.js` | Main content wrapper, breadcrumbs | None |
| `Footer` | `src/components/layout/Footer.js` | Site footer, links, copyright | None |

#### Auth Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `Login` | `src/pages/auth/Login.js` | User login form, JWT token handling | `useAuth` |
| `Register` | `src/pages/auth/Register.js` | User registration form | `useAuth` |
| `ForgotPassword` | `src/pages/auth/ForgotPassword.js` | Password reset request form | `useAuth` |
| `ResetPassword` | `src/pages/auth/ResetPassword.js` | Password reset completion form | `useAuth` |
| `MFASetup` | `src/pages/auth/MFASetup.js` | MFA configuration, QR code display | `useAuth` |

#### Catalog Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `SearchBar` | `src/components/features/catalog/SearchBar.js` | Search input with autocomplete | `useSearch` |
| `ProductGrid` | `src/components/features/catalog/ProductGrid.js` | Grid layout for product cards | None |
| `ProductCard` | `src/components/features/catalog/ProductCard.js` | Individual product card display | `useCart` |
| `ProductDetail` | `src/pages/catalog/ProductDetail.js` | Full product information page | `useCatalog`, `useCart` |
| `FilterPanel` | `src/components/features/catalog/FilterPanel.js` | Advanced filter controls | None |
| `CategoryNav` | `src/components/features/catalog/CategoryNav.js` | Category navigation sidebar | None |

#### Cart Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `CartSummary` | `src/components/features/cart/CartSummary.js` | Cart overview with totals | `useCart` |
| `CartItem` | `src/components/features/cart/CartItem.js` | Individual cart item row | `useCart` |
| `CheckoutForm` | `src/components/features/cart/CheckoutForm.js` | Checkout form (guest or user) | `useCart`, `useAuth` |
| `PaymentForm` | `src/components/features/cart/PaymentForm.js` | Payment method selection/form | `usePayment` |

#### Order Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `OrderHistory` | `src/pages/account/OrderHistory.js` | List of user's past orders | `useOrders` |
| `OrderDetail` | `src/pages/account/OrderDetail.js` | Detailed view of single order | `useOrders` |
| `OrderTracking` | `src/components/features/order/OrderTracking.js` | Shipment tracking display | `useOrders` |

#### User Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `Profile` | `src/pages/account/Profile.js` | User profile editing | `useUser` |
| `AddressBook` | `src/pages/account/AddressBook.js` | Manage shipping addresses | `useUser` |
| `EmailPreferences` | `src/pages/account/EmailPreferences.js` | Email notification settings | `useUser` |
| `ReviewList` | `src/components/features/user/ReviewList.js` | User's submitted reviews | `useUser` |

#### Digital Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `EBookReader` | `src/components/features/digital/EBookReader.js` | E-book reading interface | `useDigital` |
| `AudiobookPlayer` | `src/components/features/digital/AudiobookPlayer.js` | Audiobook streaming player | `useDigital` |
| `DigitalDelivery` | `src/components/features/digital/DigitalDelivery.js` | Digital order delivery status | `useDigital` |

#### Common Components

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `Button` | `src/components/common/Button.js` | Reusable button with variants | None |
| `Input` | `src/components/common/Input.js` | Form input with validation | None |
| `Modal` | `src/components/common/Modal.js` | Reusable modal dialog | None |
| `Toast` | `src/components/common/Toast.js` | Notification toast messages | None |
| `LoadingSpinner` | `src/components/common/LoadingSpinner.js` | Loading indicator | None |

### 2.3 Frontend Service Layer

| Service | File Path | Responsibility | Methods |
|---------|-----------|----------------|---------|
| `api` | `src/services/api.js` | Base axios instance, interceptors | `get`, `post`, `put`, `delete`, `setAuthToken` |
| `auth` | `src/services/auth.js` | Authentication API calls | `login`, `register`, `logout`, `refreshToken` |
| `catalog` | `src/services/catalog.js` | Catalog search and product API calls | `search`, `getBook`, `getReviews` |
| `cart` | `src/services/cart.js` | Cart operations API calls | `getCart`, `addItem`, `removeItem`, `updateQuantity` |
| `orders` | `src/services/orders.js` | Order history API calls | `getOrders`, `getOrder`, `getTracking` |
| `digital` | `src/services/digital.js` | Digital delivery API calls | `getLicense`, `streamBook` |
| `user` | `src/services/user.js` | User profile and preferences API calls | `getProfile`, `updateProfile`, `getAddresses` |

### 2.4 Frontend Custom Hooks

| Hook | File Path | Responsibility | Dependencies |
|------|-----------|----------------|--------------|
| `useAuth` | `src/hooks/useAuth.js` | Authentication state and operations | `api`, `auth` service |
| `useCart` | `src/hooks/useCart.js` | Cart state management | `api`, `cart` service |
| `useCatalog` | `src/hooks/useCatalog.js` | Catalog data fetching | `api`, `catalog` service |
| `useSearch` | `src/hooks/useSearch.js` | Search query management | `useCatalog` |
| `useOrders` | `src/hooks/useOrders.js` | Order history data | `api`, `orders` service |
| `useDigital` | `src/hooks/useDigital.js` | Digital content data | `api`, `digital` service |
| `useUser` | `src/hooks/useUser.js` | User profile data | `api`, `user` service |

---

## 3. Backend Components

### 3.1 Module Structure

Each backend module follows this structure:

```
modules/
├── [module-name]/
│   ├── models/          # Database models (Sequelize/Bookshelf)
│   ├── services/        # Business logic services
│   ├── routes/          # Express route handlers
│   ├── validators/      # Request validation schemas (Joi)
│   └── [module-name].js # Module entry point
```

### 3.2 Backend Module Catalog

#### Auth Module

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `User` | `modules/auth/models/User.js` | User model definition | Sequelize |
| `Session` | `modules/auth/models/Session.js` | Session model definition | Sequelize |
| `AuthService` | `modules/auth/services/AuthService.js` | User auth logic (register, login, MFA) | `TokenService`, `EmailService` |
| `TokenService` | `modules/auth/services/TokenService.js` | JWT generation and validation | `config` |
| `AuthService` | `modules/auth/services/AuthService.js` | User management | `User`, `Session` models |
| `routes/auth.js` | `modules/auth/routes/auth.js` | Auth endpoints (login, register, MFA) | `AuthService` |

**Auth Module Endpoints:**
- `POST /auth/register`
- `POST /auth/login`
- `POST /auth/logout`
- `POST /auth/refresh`
- `POST /auth/forgot-password`
- `POST /auth/reset-password`
- `POST /auth/mfa/verify`
- `POST /auth/mfa/enable`
- `POST /auth/mfa/disable`

#### Catalog Module

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `Book` | `modules/catalog/models/Book.js` | Book model definition | Sequelize |
| `Publisher` | `modules/catalog/models/Publisher.js` | Publisher model definition | Sequelize |
| `BookAuthor` | `modules/catalog/models/BookAuthor.js` | Book-author many-to-many relation | Sequelize |
| `BookGenre` | `modules/catalog/models/BookGenre.js` | Book-genre many-to-many relation | Sequelize |
| `Inventory` | `modules/catalog/models/Inventory.js` | Inventory tracking model | Sequelize |
| `CatalogService` | `modules/catalog/services/CatalogService.js` | Catalog CRUD operations | `Book`, `Publisher` models |
| `SearchService` | `modules/catalog/services/SearchService.js` | Full-text search using PostgreSQL | `CatalogService`, `db` |
| `routes/catalog.js` | `modules/catalog/routes/catalog.js` | Catalog endpoints (search, detail) | `CatalogService`, `SearchService` |

**Catalog Module Endpoints:**
- `GET /catalog/search`
- `GET /catalog/books/{isbn}`
- `GET /catalog/books/{isbn}/authors`
- `GET /catalog/books/{isbn}/reviews`
- `GET /catalog/categories`
- `GET /catalog/publishers`

#### Cart Module

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `Cart` | `modules/cart/models/Cart.js` | Cart model definition | Sequelize |
| `CartItem` | `modules/cart/models/CartItem.js` | CartItem model definition | Sequelize |
| `CartService` | `modules/cart/services/CartService.js` | Cart operations (add, remove, update) | `Cart`, `CartItem` models |

**Cart Module Endpoints:**
- `GET /cart`
- `POST /cart/items`
- `PUT /cart/items/{itemId}`
- `DELETE /cart/items/{itemId}`
- `POST /cart/checkout`

#### Checkout Module

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `CheckoutService` | `modules/checkout/services/CheckoutService.js` | Checkout orchestration | `CartService`, `PaymentProvider`, `OrderService` |
| `routes/checkout.js` | `modules/checkout/routes/checkout.js` | Checkout endpoints | `CheckoutService` |

**Checkout Module Endpoints:**
- `POST /checkout`

#### Order Module

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `Order` | `modules/order/models/Order.js` | Order model definition | Sequelize |
| `OrderItem` | `modules/order/models/OrderItem.js` | OrderItem model definition | Sequelize |
| `OrderService` | `modules/order/services/OrderService.js` | Order CRUD operations | `Order`, `OrderItem` models |
| `FulfillmentService` | `modules/order/services/FulfillmentService.js` | Order fulfillment orchestration | `OrderService`, `FulfillmentProvider` |
| `routes/orders.js` | `modules/order/routes/orders.js` | Order endpoints | `OrderService`, `FulfillmentService` |

**Order Module Endpoints:**
- `GET /orders`
- `GET /orders/{orderId}`
- `POST /orders/{orderId}/fulfillment/digital`
- `GET /orders/{orderId}/fulfillment/digital/{fileId}`
- `GET /orders/{orderId}/tracking`
- `POST /orders/{orderId}/returns`

#### Digital Module

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `DigitalOrder` | `modules/digital/models/DigitalOrder.js` | Digital delivery tracking | Sequelize |
| `DigitalDeliveryService` | `modules/digital/services/DigitalDeliveryService.js` | Digital content delivery | `DigitalOrder`, `DRMProvider` |
| `routes/digital.js` | `modules/digital/routes/digital.js` | Digital endpoints | `DigitalDeliveryService` |

**Digital Module Endpoints:**
- `GET /digital/orders/{orderId}/license`
- `GET /digital/books/{isbn}/stream`

#### User Module

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `UserProfile` | `modules/user/models/UserProfile.js` | User profile model | Sequelize |
| `Address` | `modules/user/models/Address.js` | Shipping address model | Sequelize |
| `Review` | `modules/user/models/Review.js` | Review/rating model | Sequelize |
| `EmailPreference` | `modules/user/models/EmailPreference.js` | Email preference model | Sequelize |
| `UserService` | `modules/user/services/UserService.js` | User profile operations | `UserProfile`, `Address`, `Review` models |
| `routes/users.js` | `modules/user/routes/users.js` | User endpoints | `UserService` |

**User Module Endpoints:**
- `GET /users/me`
- `PUT /users/me`
- `GET /users/me/profile`
- `PUT /users/me/profile`
- `GET /users/me/addresses`
- `POST /users/me/addresses`
- `PUT /users/me/addresses/{addressId}`
- `DELETE /users/me/addresses/{addressId}`
- `GET /users/me/reviews`
- `POST /users/me/reviews`
- `PUT /users/me/reviews/{reviewId}`
- `DELETE /users/me/reviews/{reviewId}`
- `GET /users/me/email-preferences`
- `PUT /users/me/email-preferences`

### 3.3 Shared Backend Components

#### Database Service

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `db` | `shared/db/index.js` | PostgreSQL connection pool | `pg` |

#### Middleware

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `auth` | `shared/middleware/auth.js` | JWT verification | `TokenService` |
| `error` | `shared/middleware/error.js` | Global error handler | None |
| `logging` | `shared/middleware/logging.js` | Request logging (pino) | `pino` |

#### Configuration

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `config` | `shared/config/index.js` | Environment configuration loader | None |

#### Provider Interfaces (Integration)

| Component | File Path | Responsibility | Implementations |
|-----------|-----------|----------------|-----------------|
| `PaymentProvider` | `shared/providers/PaymentProvider.interface.js` | Payment provider interface | `MockPaymentProvider`, `StripePaymentProvider` |
| `FulfillmentProvider` | `shared/providers/FulfillmentProvider.interface.js` | Fulfillment provider interface | `Mock3PLProvider`, `Real3PLProvider` |
| `DRMProvider` | `shared/providers/DRMProvider.interface.js` | DRM provider interface | `MockDRMProvider`, `RealDRMProvider` |

#### Provider Implementations

| Component | File Path | Responsibility | Dependencies |
|-----------|-----------|----------------|--------------|
| `MockPaymentProvider` | `shared/providers/PaymentProvider.mock.js` | Local mock payment processing | None |
| `StripePaymentProvider` | `shared/providers/PaymentProvider.stripe.js` | Stripe payment processing | `stripe` SDK |
| `Mock3PLProvider` | `shared/providers/FulfillmentProvider.mock.js` | Local mock fulfillment | None |
| `Real3PLProvider` | `shared/providers/FulfillmentProvider.3pl.js` | Real 3PL API integration | HTTP client |
| `MockDRMProvider` | `shared/providers/DRMProvider.mock.js` | Local mock DRM | None |
| `RealDRMProvider` | `shared/providers/DRMProvider.drm.js` | Real DRM API integration | HTTP client |

---

## 4. Database Components

### 4.1 Database Table Catalog

| Table | Purpose | Key Fields | Relationships |
|-------|---------|------------|---------------|
| `users` | User accounts | `id`, `email`, `password_hash`, `created_at` | One-to-many: profiles, sessions, carts, orders, reviews, addresses |
| `user_profiles` | User profile data | `id`, `user_id`, `phone`, `date_of_birth`, `preferences` | Belongs to: `users` |
| `sessions` | Active user sessions | `id`, `user_id`, `token_hash`, `expires_at` | Belongs to: `users` |
| `publishers` | Publisher information | `id`, `name`, `description`, `website` | One-to-many: `books` |
| `books` | Book catalog | `id`, `isbn`, `title`, `description`, `publisher_id`, `format`, `price`, `stock_quantity` | Many-to-many: `authors`, `genres`; One-to-many: `inventory`, `order_items`, `cart_items` |
| `book_authors` | Book-author relation | `book_id`, `author_name` | Many-to-one: `books` |
| `book_genres` | Book-genre relation | `book_id`, `genre` | Many-to-one: `books` |
| `inventory` | Inventory tracking | `book_id`, `warehouse_id`, `quantity` | Belongs to: `books` |
| `carts` | Shopping carts | `id`, `user_id`, `session_id`, `created_at`, `updated_at` | Belongs to: `users`; One-to-many: `cart_items` |
| `cart_items` | Cart items | `id`, `cart_id`, `book_id`, `quantity`, `price_at_addition` | Belongs to: `carts`, `books` |
| `orders` | Orders | `id`, `user_id`, `guest_email`, `status`, `total_amount`, `shipping_address`, `billing_address`, `created_at` | One-to-many: `order_items`, `digital_orders` |
| `order_items` | Order line items | `id`, `order_id`, `book_id`, `quantity`, `unit_price` | Belongs to: `orders`, `books` |
| `digital_orders` | Digital delivery tracking | `order_id`, `file_url`, `drm_license`, `delivery_status`, `downloaded_at` | Belongs to: `orders` |
| `reviews` | User reviews | `id`, `user_id`, `order_id`, `rating`, `comment`, `helpful_count`, `created_at` | Belongs to: `users`, `orders` |
| `addresses` | Shipping addresses | `id`, `user_id`, `label`, `street`, `city`, `state`, `postal_code`, `country`, `is_default` | Belongs to: `users` |
| `email_preferences` | Email notification settings | `user_id`, `marketing`, `order_notifications`, `recommendations`, `created_at` | Belongs to: `users` |

### 4.2 Database View Catalog

| View | Purpose | Query |
|------|---------|-------|
| `v_book_details` | Enhanced book info with author, format, price | `SELECT b.*, array_agg(a.author_name) as authors, COUNT(o.id) as sales_count FROM books b LEFT JOIN book_authors a ON b.id = a.book_id LEFT JOIN order_items o ON b.isbn = o.book_id GROUP BY b.id` |
| `v_order_summary` | Order summary with item count and total | `SELECT o.*, COUNT(oi.id) as item_count, SUM(oi.quantity * oi.unit_price) as item_total FROM orders o JOIN order_items oi ON o.id = oi.order_id GROUP BY o.id` |
| `v_user_orders` | User order history with status and dates | `SELECT u.id as user_id, u.email, o.* FROM users u JOIN orders o ON u.id = o.user_id` |
| `v_inventory_status` | Current inventory across warehouses | `SELECT i.book_id, b.title, SUM(i.quantity) as total_quantity FROM inventory i JOIN books b ON i.book_id = b.id GROUP BY i.book_id, b.title` |

### 4.3 Database Index Catalog

| Index | Table | Column(s) | Type | Purpose |
|-------|-------|-----------|------|---------|
| `idx_books_search` | `books` | `to_tsvector('english', title || ' ' || description)` | GIN | Full-text search |
| `idx_users_email` | `users` | `email` | B-tree | User lookup |
| `idx_carts_user_id` | `carts` | `user_id` | B-tree | Cart lookup |
| `idx_cart_items_cart_id` | `cart_items` | `cart_id` | B-tree | Cart item lookup |
| `idx_orders_user_id` | `orders` | `user_id` | B-tree | Order history lookup |
| `idx_orders_status` | `orders` | `status` | B-tree | Order status filtering |
| `idx_reviews_user_id` | `reviews` | `user_id` | B-tree | Review history lookup |
| `idx_reviews_order_id` | `reviews` | `order_id` | B-tree | Review by order lookup |
| `idx_inventory_book_id` | `inventory` | `book_id` | B-tree | Inventory lookup |

---

## 5. Integration Components

### 5.1 External Provider Interfaces

#### PaymentProvider Interface

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `charge` | `cardToken`, `amount`, `currency` | `Promise<ChargeResult>` | Charge a payment |
| `refund` | `orderId`, `amount` | `Promise<RefundResult>` | Refund a payment |
| `validateCard` | `cardDetails` | `Promise<boolean>` | Validate card format |
| `getToken` | `cardDetails` | `Promise<string>` | Get token for saved card |

#### FulfillmentProvider Interface

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getInventory` | `bookIds` | `Promise<InventoryStatus[]>` | Get inventory status for books |
| `submitOrder` | `orderData` | `Promise<SubmissionResult>` | Submit order to 3PL |
| `getTracking` | `trackingNumber` | `Promise<TrackingInfo>` | Get shipment tracking |
| `syncInventory` | None | `Promise<void>` | Sync inventory from 3PL |

#### DRMProvider Interface

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `generateLicense` | `bookId`, `userId` | `Promise<DRMLicense>` | Generate DRM license for user |
| `validateLicense` | `license` | `Promise<boolean>` | Validate DRM license |
| `revokeLicense` | `bookId`, `userId` | `Promise<void>` | Revoke user's license |

### 5.2 External Provider Implementations

#### MockPaymentProvider

| Method | Implementation |
|--------|----------------|
| `charge` | Simulates successful charge, returns mock transaction ID |
| `refund` | Simulates successful refund |
| `validateCard` | Basic format validation |
| `getToken` | Returns deterministic token for test card |

#### Mock3PLProvider

| Method | Implementation |
|--------|----------------|
| `getInventory` | Returns mock inventory with 100+ units per book |
| `submitOrder` | Returns mock tracking number |
| `getTracking` | Returns mock shipment status |
| `syncInventory` | Logs sync but no actual sync |

#### MockDRMProvider

| Method | Implementation |
|--------|----------------|
| `generateLicense` | Creates JWT-based license with 24-hour expiry |
| `validateLicense` | Validates JWT signature and expiry |
| `revokeLicense` | Adds license to revocation list |

---

## 6. Component Dependencies

### 6.1 Frontend-to-Frontend Dependencies

| Component | Depends On | Reason |
|-----------|------------|--------|
| `Header` | `Navigation` | Navigation is part of header |
| `ProductDetail` | `useCart` | "Add to Cart" button |
| `ProductCard` | `useCart` | "Add to Cart" button |
| `CheckoutForm` | `useAuth` | Guest vs. registered user form |
| `CheckoutForm` | `useCart` | Cart items for checkout |
| `OrderDetail` | `useOrders` | Order information display |
| `OrderHistory` | `useOrders` | Order list display |
| `EBookReader` | `useDigital` | License and file URL |
| `AudiobookPlayer` | `useDigital` | License and stream URL |

### 6.2 Backend-to-Backend Dependencies

| Component | Depends On | Reason |
|-----------|------------|--------|
| `AuthService` | `User`, `Session` models | Data persistence |
| `AuthService` | `TokenService` | JWT token generation |
| `AuthService` | `EmailService` | Verification email |
| `CatalogService` | `Book`, `Publisher` models | Data persistence |
| `SearchService` | `CatalogService` | Base search functionality |
| `CheckoutService` | `CartService` | Cart retrieval |
| `CheckoutService` | `PaymentProvider` | Payment processing |
| `CheckoutService` | `OrderService` | Order creation |
| `CheckoutService` | `FulfillmentProvider` | Physical fulfillment |
| `DigitalDeliveryService` | `DigitalOrder` model | Delivery tracking |
| `DigitalDeliveryService` | `DRMProvider` | License generation |
| `UserService` | `UserProfile`, `Address`, `Review` models | Data persistence |

### 6.3 External Dependency Dependencies

| Component | External Service | Purpose |
|-----------|-----------------|---------|
| `StripePaymentProvider` | Stripe API | Payment processing |
| `Real3PLProvider` | 3PL API | Order fulfillment |
| `RealDRMProvider` | DRM API | Content protection |

---

## 7. Component Boundaries

### 7.1 Frontend Boundaries

**API Layer:**
- All external communication goes through `services/api.js`
- API service handles auth tokens, error formatting

**State Management:**
- React Query for server state (caching, refetching)
- Context API for client state (cart, user session)

**Routing:**
- React Router for navigation
- Protected routes require authentication

### 7.2 Backend Boundaries

**API Layer:**
- Express middleware chain
- Authentication middleware before protected routes

**Business Logic:**
- Services encapsulate domain logic
- Models handle data persistence

**External Integration:**
- Provider interfaces for pluggable external services
- Mock implementations for local development

### 7.3 Database Boundaries

**Schema:**
- Single database shared by all modules
- Foreign key constraints for integrity
- Soft deletes for orders and reviews

**Access:**
- Database service provides query interface
- Transaction support for multi-step operations

---

## 8. Component Communication

### 8.1 Frontend Communication

**Component-to-Component:**
- Props for parent-child
- Context API for cross-component sharing
- Events for child-to-parent

**Component-to-Service:**
- Custom hooks expose service methods
- React Query handles API calls

**Service-to-Service:**
- Import dependencies
- Shared utilities via `utils/` folder

### 8.2 Backend Communication

**Module-to-Module:**
- Direct imports between modules
- Service layer as boundary

**Database Communication:**
- `db.query()` for raw SQL
- Sequelize ORM for model operations
- Transactions for multi-table operations

**External Communication:**
- Provider interfaces for abstraction
- HTTP client (axios) for API calls

---

## 9. Component Lifecycle

### 9.1 Frontend Lifecycle

**Component Mount:**
1. `useEffect` hooks run
2. React Query fetches initial data
3. Event listeners attached

**Component Update:**
1. Props change triggers re-render
2. React Query refreshes data if stale
3. Event handlers execute

**Component Unmount:**
1. Event listeners removed
2. React Query cache retained
3. Cleanup functions run

### 9.2 Backend Lifecycle

**Request:**
1. Middleware runs (auth, logging)
2. Route handler executes
3. Service layer processes
4. Database query runs
5. Response sent

**Shutdown:**
1. Close database connections
2. Close external provider connections
3. Cleanup resources

---

## 10. Component Testing Strategy

### 10.1 Frontend Testing

| Component Type | Testing Approach | Tools |
|---------------|------------------|-------|
| UI Components | Unit tests for rendering | Jest + React Testing Library |
| Custom Hooks | Logic-only tests | Jest + React Testing Library |
| API Services | Integration tests | Jest + mock API |
| End-to-End | Full user journeys | Cypress |

### 10.2 Backend Testing

| Component Type | Testing Approach | Tools |
|---------------|------------------|-------|
| Models | Unit tests for validation | Jest |
| Services | Unit tests for business logic | Jest |
| Routes | Integration tests | Jest + supertest |
| Middleware | Unit tests for logic | Jest |
| External Providers | Integration tests | Jest + mock HTTP server |

### 10.3 Database Testing

| Approach | Tools |
|----------|-------|
| Schema validation | pg-format |
| Migration testing | node-pg-migrate |
| Seed data verification | Custom test scripts |

---

*End of Components Catalog*
