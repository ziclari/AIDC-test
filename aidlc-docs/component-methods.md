# AnyCompanyRead - Component Methods and Operations

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

This document catalogs all methods, operations, and interfaces across the AnyCompanyRead system, organized by component type.

**Method Categories:**
- **Frontend Methods:** React component functions, hooks, services
- **Backend Methods:** Express route handlers, service layer methods
- **Database Methods:** SQL queries and operations
- **Integration Methods:** Provider interface methods

---

## 2. Frontend Methods

### 2.1 Service Layer Methods

#### api.js (Base API Service)

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `get(url, config)` | `url: string`, `config: object` | `Promise<axios.Response>` | GET request |
| `post(url, data, config)` | `url: string`, `data: object`, `config: object` | `Promise<axios.Response>` | POST request |
| `put(url, data, config)` | `url: string`, `data: object`, `config: object` | `Promise<axios.Response>` | PUT request |
| `delete(url, config)` | `url: string`, `config: object` | `Promise<axios.Response>` | DELETE request |
| `setAuthToken(token)` | `token: string` | `void` | Set JWT token on axios instance |
| `clearAuthToken()` | None | `void` | Remove JWT token from axios instance |

**Example Usage:**
```javascript
const response = await api.get('/catalog/search', {
  params: { q: 'harry potter', format: 'physical' }
});
```

#### auth.js (Authentication Service)

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `login(email, password)` | `email: string`, `password: string` | `Promise<LoginResponse>` | User login |
| `register(userData)` | `userData: object` | `Promise<RegisterResponse>` | User registration |
| `logout()` | None | `Promise<void>` | User logout |
| `refreshToken(refreshToken)` | `refreshToken: string` | `Promise<TokenResponse>` | Refresh JWT token |
| `forgotPassword(email)` | `email: string` | `Promise<void>` | Request password reset |
| `resetPassword(token, password)` | `token: string`, `password: string` | `Promise<void>` | Complete password reset |
| `verifyMFA(userId, code)` | `userId: string`, `code: string` | `Promise<void>` | Verify MFA code |
| `enableMFA(userId, secret)` | `userId: string`, `secret: string` | `Promise<void>` | Enable MFA |
| `disableMFA(userId)` | `userId: string` | `Promise<void>` | Disable MFA |

**Response Types:**
```typescript
interface LoginResponse {
  token: string;
  refreshToken: string;
  expiresAt: string;
  user: {
    id: string;
    email: string;
    firstName: string;
    lastName: string;
  };
}

interface RegisterResponse {
  message: string;
}

interface TokenResponse {
  token: string;
  refreshToken: string;
  expiresAt: string;
}
```

#### catalog.js (Catalog Service)

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `search(query, filters)` | `query: string`, `filters: object` | `Promise<SearchResults>` | Search books |
| `getBookByISBN(isbn)` | `isbn: string` | `Promise<Book>` | Get book by ISBN |
| `getBookAuthors(isbn)` | `isbn: string` | `Promise<Author[]>` | Get book authors |
| `getBookReviews(isbn)` | `isbn: string` | `Promise<Review[]>` | Get book reviews |
| `getCategories()` | None | `Promise<Category[]>` | Get all categories |
| `getPublishers()` | None | `Promise<Publisher[]>` | Get all publishers |

**Response Types:**
```typescript
interface SearchResults {
  items: Book[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

interface Book {
  isbn: string;
  title: string;
  author: string;
  format: 'physical' | 'e-book' | 'audiobook';
  price: number;
  coverImage: string;
  description?: string;
}

interface Author {
  name: string;
  bio?: string;
}
```

#### cart.js (Cart Service)

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getCart()` | None | `Promise<Cart>` | Get user's cart |
| `addItem(bookId, quantity)` | `bookId: string`, `quantity: number` | `Promise<CartItem>` | Add item to cart |
| `removeItem(itemId)` | `itemId: string` | `Promise<void>` | Remove item from cart |
| `updateQuantity(itemId, quantity)` | `itemId: string`, `quantity: number` | `Promise<CartItem>` | Update item quantity |
| `checkout()` | None | `Promise<Order>` | Process checkout |

**Response Types:**
```typescript
interface Cart {
  id: string;
  userId: string;
  items: CartItem[];
  subtotal: number;
  estimatedTax: number;
  total: number;
}

interface CartItem {
  id: string;
  bookId: string;
  title: string;
  author: string;
  format: string;
  quantity: number;
  price: number;
}

interface Order {
  id: string;
  orderId: string;
  status: string;
  totalAmount: number;
  items: OrderItem[];
  shippingAddress: Address;
  billingAddress: Address;
}
```

#### orders.js (Orders Service)

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getOrders(userId)` | `userId: string` | `Promise<Order[]>` | Get user's order history |
| `getOrder(orderId)` | `orderId: string` | `Promise<Order>` | Get single order details |
| `getTracking(orderId)` | `orderId: string` | `Promise<TrackingInfo>` | Get order tracking |
| `returnOrder(orderId, reason)` | `orderId: string`, `reason: string` | `Promise<void>` | Initiate return |

**Response Types:**
```typescript
interface Order {
  id: string;
  orderId: string;
  status: 'pending' | 'processing' | 'shipped' | 'delivered' | 'cancelled';
  items: OrderItem[];
  totalAmount: number;
  shippingAddress: Address;
  billingAddress: Address;
  createdAt: string;
  shippedAt?: string;
  deliveredAt?: string;
}

interface OrderItem {
  bookId: string;
  title: string;
  format: string;
  quantity: number;
  unitPrice: number;
}

interface TrackingInfo {
  trackingNumber: string;
  carrier: string;
  status: string;
  estimatedDelivery?: string;
  history: TrackingEvent[];
}

interface TrackingEvent {
  timestamp: string;
  status: string;
  location: string;
}
```

#### digital.js (Digital Delivery Service)

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getLicense(orderId)` | `orderId: string` | `Promise<DRMLicense>` | Get DRM license for order |
| `streamBook(isbn)` | `isbn: string` | `Promise<StreamURL>` | Get streaming URL for audiobook |

**Response Types:**
```typescript
interface DRMLicense {
  license: string;  // JWT-based license
  expiresAt: string;
  bookId: string;
  userId: string;
  deviceLimit: number;
  downloadUrl: string;
}

interface StreamURL {
  url: string;
  license: string;
  expiresAt: string;
}
```

#### user.js (User Service)

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getProfile()` | None | `Promise<UserProfile>` | Get user profile |
| `updateProfile(data)` | `data: object` | `Promise<UserProfile>` | Update user profile |
| `getAddresses()` | None | `Promise<Address[]>` | Get user's addresses |
| `addAddress(address)` | `address: object` | `Promise<Address>` | Add new address |
| `updateAddress(addressId, data)` | `addressId: string`, `data: object` | `Promise<Address>` | Update address |
| `deleteAddress(addressId)` | `addressId: string` | `Promise<void>` | Delete address |
| `getReviews()` | None | `Promise<Review[]>` | Get user's reviews |
| `submitReview(orderId, rating, comment)` | `orderId: string`, `rating: number`, `comment: string` | `Promise<Review>` | Submit review |
| `updateReview(reviewId, data)` | `reviewId: string`, `data: object` | `Promise<Review>` | Update review |
| `deleteReview(reviewId)` | `reviewId: string` | `Promise<void>` | Delete review |
| `getEmailPreferences()` | None | `Promise<EmailPreferences>` | Get email preferences |
| `updateEmailPreferences(preferences)` | `preferences: object` | `Promise<EmailPreferences>` | Update email preferences |

**Response Types:**
```typescript
interface UserProfile {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  phone?: string;
  dateOfBirth?: string;
  preferences: {
    favoriteGenres?: string[];
    readingGoals?: string;
  };
}

interface Address {
  id: string;
  label: string;
  street: string;
  city: string;
  state: string;
  postalCode: string;
  country: string;
  isDefault: boolean;
}

interface Review {
  id: string;
  orderId: string;
  rating: number;
  comment?: string;
  helpfulCount: number;
  createdAt: string;
  updatedAt: string;
}

interface EmailPreferences {
  marketing: boolean;
  orderNotifications: boolean;
  recommendations: boolean;
}
```

---

### 2.2 Custom Hook Methods

#### useAuth Hook

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `login(email, password)` | `email: string`, `password: string` | `Promise<void>` | Login user |
| `register(userData)` | `userData: object` | `Promise<void>` | Register user |
| `logout()` | None | `Promise<void>` | Logout user |
| `refreshToken()` | None | `Promise<void>` | Refresh auth token |
| `enableMFA(secret)` | `secret: string` | `Promise<void>` | Enable MFA |
| `disableMFA()` | None | `Promise<void>` | Disable MFA |
| `resetPassword(token, password)` | `token: string`, `password: string` | `Promise<void>` | Reset password |
| `isLoading` | None | `boolean` | Loading state |
| `isAuthenticated` | None | `boolean` | Authentication state |
| `user` | None | `User | null` | Current user |

#### useCart Hook

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `addItem(bookId, quantity)` | `bookId: string`, `quantity: number` | `Promise<void>` | Add item to cart |
| `removeItem(itemId)` | `itemId: string` | `Promise<void>` | Remove item from cart |
| `updateQuantity(itemId, quantity)` | `itemId: string`, `quantity: number` | `Promise<void>` | Update item quantity |
| `clearCart()` | None | `Promise<void>` | Clear cart |
| `checkout()` | None | `Promise<void>` | Checkout cart |
| `cart` | None | `Cart | null` | Current cart |
| `isLoading` | None | `boolean` | Loading state |
| `error` | None | `string | null` | Error message |

#### useCatalog Hook

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `search(query, filters)` | `query: string`, `filters: object` | `Promise<void>` | Search books |
| `getBookByISBN(isbn)` | `isbn: string` | `Promise<void>` | Get book by ISBN |
| `getBookAuthors(isbn)` | `isbn: string` | `Promise<void>` | Get book authors |
| `getBookReviews(isbn)` | `isbn: string` | `Promise<void>` | Get book reviews |
| `books` | None | `Book[] | null` | Search results |
| `book` | None | `Book | null` | Current book (by ISBN) |
| `authors` | None | `Author[] | null` | Book authors |
| `reviews` | None | `Review[] | null` | Book reviews |
| `isLoading` | None | `boolean` | Loading state |

#### useOrders Hook

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getOrders()` | None | `Promise<void>` | Get order history |
| `getOrder(orderId)` | `orderId: string` | `Promise<void>` | Get single order |
| `getTracking(orderId)` | `orderId: string` | `Promise<void>` | Get tracking info |
| `returnOrder(orderId, reason)` | `orderId: string`, `reason: string` | `Promise<void>` | Return order |
| `orders` | None | `Order[] | null` | Order history |
| `order` | None | `Order | null` | Current order |
| `isLoading` | None | `boolean` | Loading state |

#### useDigital Hook

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getLicense(orderId)` | `orderId: string` | `Promise<void>` | Get DRM license |
| `streamBook(isbn)` | `isbn: string` | `Promise<void>` | Get stream URL |
| `license` | None | `DRMLicense | null` | Current license |
| `isLoading` | None | `boolean` | Loading state |

---

### 2.3 React Component Methods

#### Component Event Handlers

**Common Event Handler Types:**
```javascript
// Form submission
const handleSubmit = (e) => {
  e.preventDefault();
  // validation logic
};

// Button click
const handleButtonClick = () => {
  // action logic
};

// Change input
const handleChange = (e) => {
  setState({ ...state, [e.target.name]: e.target.value });
};

// Fetch data on mount
useEffect(() => {
  fetch-data-function();
}, []);
```

**Component-Specific Handlers:**

| Component | Handler | Purpose |
|-----------|---------|---------|
| `Login` | `handleSubmit` | Process login form |
| `Register` | `handleSubmit` | Process registration form |
| `ProductDetail` | `handleAddToCart` | Add book to cart |
| `CartSummary` | `handleUpdateQuantity` | Update cart item quantity |
| `CartSummary` | `handleRemoveItem` | Remove cart item |
| `CheckoutForm` | `handleSubmit` | Process checkout |
| `EBookReader` | `handlePageChange` | Navigate between pages |
| `EBookReader` | `handleProgressSave` | Save reading position |
| `AudiobookPlayer` | `handlePlayPause` | Toggle playback |
| `AudiobookPlayer` | `handleSeek` | Seek to position |
| `ReviewList` | `handleHelpfulVote` | Vote helpful/not helpful |
| `Profile` | `handleSubmit` | Update profile |
| `AddressBook` | `handleAddAddress` | Add new address |
| `AddressBook` | `handleEditAddress` | Edit existing address |
| `AddressBook` | `handleDeleteAddress` | Delete address |
| `EmailPreferences` | `handleSubmit` | Update preferences |

---

## 3. Backend Methods

### 3.1 Express Route Handlers

#### Auth Routes

| Route | Method | Handler | Purpose |
|-------|--------|---------|---------|
| `/auth/register` | POST | `registerUser` | Create new user account |
| `/auth/login` | POST | `loginUser` | Authenticate user |
| `/auth/logout` | POST | `logoutUser` | Invalidate user session |
| `/auth/refresh` | POST | `refreshToken` | Generate new access token |
| `/auth/forgot-password` | POST | `requestPasswordReset` | Initiate password reset |
| `/auth/reset-password` | POST | `completePasswordReset` | Complete password reset |
| `/auth/mfa/verify` | POST | `verifyMFA` | Verify MFA code |
| `/auth/mfa/enable` | POST | `enableMFA` | Enable MFA for user |
| `/auth/mfa/disable` | POST | `disableMFA` | Disable MFA for user |

#### Catalog Routes

| Route | Method | Handler | Purpose |
|-------|--------|---------|---------|
| `/catalog/search` | GET | `searchBooks` | Search books with filters |
| `/catalog/books/{isbn}` | GET | `getBookDetails` | Get book by ISBN |
| `/catalog/books/{isbn}/authors` | GET | `getBookAuthors` | Get book authors |
| `/catalog/books/{isbn}/reviews` | GET | `getBookReviews` | Get book reviews |
| `/catalog/categories` | GET | `getCategories` | Get all categories |
| `/catalog/publishers` | GET | `getPublishers` | Get all publishers |

#### Cart Routes

| Route | Method | Handler | Purpose |
|-------|--------|---------|---------|
| `/cart` | GET | `getCart` | Get user's cart |
| `/cart/items` | POST | `addToCart` | Add item to cart |
| `/cart/items/{itemId}` | PUT | `updateCartItem` | Update cart item quantity |
| `/cart/items/{itemId}` | DELETE | `removeFromCart` | Remove item from cart |
| `/cart/checkout` | POST | `processCheckout` | Process cart checkout |

#### Order Routes

| Route | Method | Handler | Purpose |
|-------|--------|---------|---------|
| `/orders` | GET | `getOrderHistory` | Get user's order history |
| `/orders/{orderId}` | GET | `getOrderDetails` | Get single order details |
| `/orders/{orderId}/fulfillment/digital` | POST | `fulfillDigitalOrder` | Fulfill digital order |
| `/orders/{orderId}/fulfillment/digital/{fileId}` | GET | `getDigitalFile` | Download digital file |
| `/orders/{orderId}/tracking` | GET | `getOrderTracking` | Get order tracking info |
| `/orders/{orderId}/returns` | POST | `initiateReturn` | Initiate order return |

#### User Routes

| Route | Method | Handler | Purpose |
|-------|--------|---------|---------|
| `/users/me` | GET | `getMyProfile` | Get current user profile |
| `/users/me` | PUT | `updateMyProfile` | Update current user profile |
| `/users/me/profile` | GET | `getMyProfile` | Get user profile details |
| `/users/me/profile` | PUT | `updateMyProfile` | Update user profile details |
| `/users/me/addresses` | GET | `getMyAddresses` | Get user's addresses |
| `/users/me/addresses` | POST | `addMyAddress` | Add new address |
| `/users/me/addresses/{addressId}` | PUT | `updateMyAddress` | Update address |
| `/users/me/addresses/{addressId}` | DELETE | `deleteMyAddress` | Delete address |
| `/users/me/reviews` | GET | `getMyReviews` | Get user's reviews |
| `/users/me/reviews` | POST | `submitMyReview` | Submit new review |
| `/users/me/reviews/{reviewId}` | PUT | `updateMyReview` | Update review |
| `/users/me/reviews/{reviewId}` | DELETE | `deleteMyReview` | Delete review |
| `/users/me/email-preferences` | GET | `getMyEmailPreferences` | Get email preferences |
| `/users/me/email-preferences` | PUT | `updateMyEmailPreferences` | Update email preferences |

#### Digital Routes

| Route | Method | Handler | Purpose |
|-------|--------|---------|---------|
| `/digital/orders/{orderId}/license` | GET | `getDigitalLicense` | Get DRM license for order |
| `/digital/books/{isbn}/stream` | GET | `streamAudiobook` | Get audiobook stream URL |

---

### 3.2 Service Layer Methods

#### AuthService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `register(userData)` | `userData: object` | `Promise<User>` | Register new user |
| `login(email, password)` | `email: string`, `password: string` | `Promise<LoginResult>` | Authenticate user |
| `logout(userId)` | `userId: string` | `Promise<void>` | Invalidate user session |
| `refreshToken(refreshToken)` | `refreshToken: string` | `Promise<TokenResult>` | Generate new access token |
| `requestPasswordReset(email)` | `email: string` | `Promise<void>` | Initiate password reset |
| `completePasswordReset(resetToken, newPassword)` | `resetToken: string`, `newPassword: string` | `Promise<void>` | Complete password reset |
| `verifyMFA(userId, code)` | `userId: string`, `code: string` | `Promise<void>` | Verify MFA code |
| `enableMFA(userId, secret)` | `userId: string`, `secret: string` | `Promise<void>` | Enable MFA |
| `disableMFA(userId)` | `userId: string` | `Promise<void>` | Disable MFA |

#### TokenService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `generateToken(userId, role)` | `userId: string`, `role: string` | `Promise<string>` | Generate access token |
| `generateRefreshToken(userId)` | `userId: string` | `Promise<string>` | Generate refresh token |
| `verifyToken(token)` | `token: string` | `Promise<object>` | Verify access token |
| `verifyRefreshToken(token)` | `token: string` | `Promise<object>` | Verify refresh token |

#### CatalogService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `searchBooks(query, filters)` | `query: string`, `filters: object` | `Promise<Book[]>` | Search books |
| `getBookByISBN(isbn)` | `isbn: string` | `Promise<Book>` | Get book by ISBN |
| `getBookAuthors(isbn)` | `isbn: string` | `Promise<Author[]>` | Get book authors |
| `getBookReviews(isbn)` | `isbn: string` | `Promise<Review[]>` | Get book reviews |
| `getCategories()` | None | `Promise<Category[]>` | Get all categories |
| `getPublishers()` | None | `Promise<Publisher[]>` | Get all publishers |

#### SearchService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `fullTextSearch(query, filters)` | `query: string`, `filters: object` | `Promise<Book[]>` | PostgreSQL FTS search |
| `buildSearchQuery(query, filters)` | `query: string`, `filters: object` | `Promise<object>` | Build SQL query |
| `rankResults(results, query)` | `results: object[]`, `query: string` | `Promise<object[]>` | Rank by relevance |

#### CartService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getCart(userId)` | `userId: string` | `Promise<Cart>` | Get user's cart |
| `addToCart(userId, bookId, quantity)` | `userId: string`, `bookId: string`, `quantity: number` | `Promise<CartItem>` | Add item to cart |
| `updateCartItem(userId, itemId, quantity)` | `userId: string`, `itemId: string`, `quantity: number` | `Promise<CartItem>` | Update item quantity |
| `removeFromCart(userId, itemId)` | `userId: string`, `itemId: string` | `Promise<void>` | Remove item from cart |
| `clearCart(userId)` | `userId: string` | `Promise<void>` | Clear user's cart |
| `calculateTotals(cartItems)` | `cartItems: CartItem[]` | `Promise<object>` | Calculate subtotal, tax, total |

#### CheckoutService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `validateOrder(cart, shippingAddress)` | `cart: Cart`, `shippingAddress: object` | `Promise<boolean>` | Validate order |
| `processPayment(cart, paymentMethod)` | `cart: Cart`, `paymentMethod: object` | `Promise<PaymentResult>` | Process payment |
| `createOrder(cart, paymentResult, shippingAddress)` | `cart: Cart`, `paymentResult: object`, `shippingAddress: object` | `Promise<Order>` | Create order |
| `fulfillPhysicalOrder(order)` | `order: Order` | `Promise<void>` | Fulfill physical order |
| `fulfillDigitalOrder(order)` | `order: Order` | `Promise<void>` | Fulfill digital order |
| `sendOrderConfirmation(order)` | `order: Order` | `Promise<void>` | Send confirmation email |

#### OrderService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getOrderHistory(userId)` | `userId: string` | `Promise<Order[]>` | Get user's order history |
| `getOrderById(orderId)` | `orderId: string` | `Promise<Order>` | Get single order |
| `updateOrderStatus(orderId, status)` | `orderId: string`, `status: string` | `Promise<void>` | Update order status |
| `initiateReturn(orderId, reason)` | `orderId: string`, `reason: string` | `Promise<void>` | Initiate return |
| `getTracking(orderId)` | `orderId: string` | `Promise<TrackingInfo>` | Get tracking info |

#### FulfillmentService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `submitOrder(order)` | `order: Order` | `Promise<FulfillmentResult>` | Submit order to 3PL |
| `getTracking(trackingNumber)` | `trackingNumber: string` | `Promise<TrackingInfo>` | Get tracking info |
| `syncInventory()` | None | `Promise<void>` | Sync inventory from 3PL |

#### DigitalDeliveryService

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `generateLicense(order)` | `order: Order` | `Promise<DRMLicense>` | Generate DRM license |
| `validateLicense(license)` | `license: string` | `Promise<boolean>` | Validate license |
| `getStreamURL(isbn)` | `isbn: string` | `Promise<string>` | Get audiobook stream URL |
| `trackDownload(orderId)` | `orderId: string` | `Promise<void>` | Track digital download |

#### UserService

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

---

### 3.3 Provider Interface Methods

#### PaymentProvider Interface

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `charge(cardToken, amount, currency)` | `cardToken: string`, `amount: number`, `currency: string` | `Promise<ChargeResult>` | Charge payment |
| `refund(orderId, amount)` | `orderId: string`, `amount: number` | `Promise<RefundResult>` | Refund payment |
| `validateCard(cardDetails)` | `cardDetails: object` | `Promise<boolean>` | Validate card format |
| `getToken(cardDetails)` | `cardDetails: object` | `Promise<string>` | Get token for saved card |

#### FulfillmentProvider Interface

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `getInventory(bookIds)` | `bookIds: string[]` | `Promise<InventoryStatus[]>` | Get inventory status |
| `submitOrder(orderData)` | `orderData: object` | `Promise<SubmissionResult>` | Submit order to 3PL |
| `getTracking(trackingNumber)` | `trackingNumber: string` | `Promise<TrackingInfo>` | Get shipment tracking |
| `syncInventory()` | None | `Promise<void>` | Sync inventory from 3PL |

#### DRMProvider Interface

| Method | Parameters | Returns | Purpose |
|--------|------------|---------|---------|
| `generateLicense(bookId, userId)` | `bookId: string`, `userId: string` | `Promise<DRMLicense>` | Generate DRM license |
| `validateLicense(license)` | `license: string` | `Promise<boolean>` | Validate license |
| `revokeLicense(bookId, userId)` | `bookId: string`, `userId: string` | `Promise<void>` | Revoke license |

---

## 4. Database Methods (SQL)

### 4.1 Query Methods

#### Authentication Queries

| Query | Purpose |
|-------|---------|
| `SELECT * FROM users WHERE email = ?` | Find user by email |
| `INSERT INTO users (...) VALUES (...)` | Create new user |
| `SELECT * FROM sessions WHERE user_id = ? AND expires_at > NOW()` | Get active sessions |
| `INSERT INTO sessions (...) VALUES (...)` | Create new session |
| `DELETE FROM sessions WHERE id = ?` | Invalidate session |

#### Catalog Queries

| Query | Purpose |
|-------|---------|
| `SELECT * FROM books WHERE to_tsvector('english', title || ' ' || description) @@ to_tsquery(?)` | Full-text search |
| `SELECT * FROM books WHERE isbn = ?` | Get book by ISBN |
| `SELECT author_name FROM book_authors WHERE book_id = ?` | Get book authors |
| `SELECT * FROM reviews WHERE book_id = ?` | Get book reviews |
| `SELECT genre FROM book_genres WHERE book_id = ?` | Get book genres |
| `SELECT * FROM inventory WHERE book_id = ?` | Get inventory for book |

#### Cart Queries

| Query | Purpose |
|-------|---------|
| `SELECT * FROM carts WHERE user_id = ?` | Get user's cart |
| `INSERT INTO carts (...) VALUES (...)` | Create new cart |
| `SELECT * FROM cart_items WHERE cart_id = ?` | Get cart items |
| `INSERT INTO cart_items (...) VALUES (...)` | Add cart item |
| `UPDATE cart_items SET quantity = ? WHERE id = ?` | Update cart item |
| `DELETE FROM cart_items WHERE id = ?` | Remove cart item |

#### Order Queries

| Query | Purpose |
|-------|---------|
| `SELECT * FROM orders WHERE user_id = ? ORDER BY created_at DESC` | Get order history |
| `SELECT * FROM orders WHERE id = ?` | Get single order |
| `INSERT INTO orders (...) VALUES (...)` | Create order |
| `SELECT * FROM order_items WHERE order_id = ?` | Get order items |
| `INSERT INTO order_items (...) VALUES (...)` | Add order item |
| `UPDATE orders SET status = ? WHERE id = ?` | Update order status |
| `SELECT * FROM digital_orders WHERE order_id = ?` | Get digital delivery info |

#### User Queries

| Query | Purpose |
|-------|---------|
| `SELECT * FROM user_profiles WHERE user_id = ?` | Get user profile |
| `UPDATE user_profiles SET ... WHERE user_id = ?` | Update user profile |
| `SELECT * FROM addresses WHERE user_id = ?` | Get user's addresses |
| `INSERT INTO addresses (...) VALUES (...)` | Add address |
| `UPDATE addresses SET ... WHERE id = ?` | Update address |
| `DELETE FROM addresses WHERE id = ?` | Delete address |
| `SELECT * FROM reviews WHERE user_id = ?` | Get user's reviews |
| `INSERT INTO reviews (...) VALUES (...)` | Submit review |
| `UPDATE reviews SET ... WHERE id = ?` | Update review |
| `DELETE FROM reviews WHERE id = ?` | Delete review |

### 4.2 Transaction Methods

| Operation | Queries |
|-----------|---------|
| **Checkout** | 1. Get cart, 2. Validate inventory, 3. Create order, 4. Create order items, 5. Clear cart |
| **Return** | 1. Get order, 2. Update order status, 3. Create refund record |
| **Review Submission** | 1. Get order, 2. Insert review, 3. Update review count |

---

## 5. Provider Implementation Methods

### 5.1 MockPaymentProvider Implementation

| Method | Implementation Details |
|--------|------------------------|
| `charge(cardToken, amount, currency)` | Returns mock transaction ID, simulates success |
| `refund(orderId, amount)` | Returns mock refund ID, simulates success |
| `validateCard(cardDetails)` | Validates card number format (Luhn check) |
| `getToken(cardDetails)` | Returns deterministic token for test card |

### 5.2 Mock3PLProvider Implementation

| Method | Implementation Details |
|--------|------------------------|
| `getInventory(bookIds)` | Returns mock inventory (100+ units per book) |
| `submitOrder(orderData)` | Returns mock tracking number, simulates success |
| `getTracking(trackingNumber)` | Returns mock shipment status (preparing, shipped, out for delivery) |
| `syncInventory()` | Logs inventory sync but no actual sync |

### 5.3 MockDRMProvider Implementation

| Method | Implementation Details |
|--------|------------------------|
| `generateLicense(bookId, userId)` | Creates JWT with 24-hour expiry, device limit 3 |
| `validateLicense(license)` | Validates JWT signature and expiry |
| `revokeLicense(bookId, userId)` | Adds license to revocation list in memory |

---

## 6. Method Dependencies

### 6.1 Frontend Method Dependencies

```
Auth Hooks
├── useAuth
│   ├── login (calls auth service)
│   ├── register (calls auth service)
│   └── logout (calls auth service)

Cart Hooks
├── useCart
│   ├── addItem (calls cart service)
│   ├── removeItem (calls cart service)
│   ├── updateQuantity (calls cart service)
│   └── checkout (calls cart service)

Catalog Hooks
├── useCatalog
│   ├── searchBooks (calls catalog service)
│   └── getBookByISBN (calls catalog service)

Order Hooks
├── useOrders
│   ├── getOrders (calls orders service)
│   └── getOrder (calls orders service)

Digital Hooks
└── useDigital
    ├── getLicense (calls digital service)
    └── streamBook (calls digital service)
```

### 6.2 Backend Method Dependencies

```
Route Handlers
├── Auth Routes
│   └── loginUser
│       ├── AuthService.login
│       │   ├── TokenService.generateToken
│       │   └── TokenService.generateRefreshToken
│       └── AuthService.logout
│           └── AuthService.logout

├── Catalog Routes
│   └── searchBooks
│       ├── SearchService.fullTextSearch
│       │   ├── SearchService.buildSearchQuery
│       │   └── SearchService.rankResults
│       └── CatalogService.searchBooks

├── Cart Routes
│   └── processCheckout
│       ├── CheckoutService.validateOrder
│       ├── CheckoutService.processPayment
│       │   └── PaymentProvider.charge
│       ├── CheckoutService.createOrder
│       │   ├── OrderService.createOrder
│       │   └── CartService.clearCart
│       └── CheckoutService.fulfillPhysicalOrder
│           └── FulfillmentService.submitOrder

├── Order Routes
│   └── initiateReturn
│       └── OrderService.initiateReturn
│           ├── OrderService.getOrderById
│           └── OrderService.updateOrderStatus

└── User Routes
    └── submitReview
        └── UserService.submitReview
            ├── UserService.getProfile
            └── OrderService.getOrderById
```

### 6.3 Provider Method Dependencies

```
Checkout Service
├── processPayment
│   └── PaymentProvider.charge (Stripe or Mock)
└── fulfillDigitalOrder
    └── DigitalDeliveryService.generateLicense
        └── DRMProvider.generateLicense (Mock or Real)

Digital Delivery Service
├── generateLicense
│   ├── DRMProvider.generateLicense
│   └── DigitalOrder.create
└── getStreamURL
    └── DRMProvider.getStreamURL (if applicable)

Fulfillment Service
├── submitOrder
│   └── FulfillmentProvider.submitOrder (3PL or Mock)
└── syncInventory
    └── FulfillmentProvider.getInventory (3PL or Mock)
```

---

## 7. Method Parameters and Return Types

### 7.1 Common Parameter Types

```typescript
// Query parameters
interface QueryParams {
  q?: string;
  page?: number;
  limit?: number;
  format?: string;
  genre?: string;
  price_min?: number;
  price_max?: number;
}

// Pagination response
interface Pagination {
  page: number;
  limit: number;
  total: number;
  totalPages: number;
}

// Address object
interface Address {
  street: string;
  city: string;
  state: string;
  postalCode: string;
  country: string;
  label?: string;
  isDefault?: boolean;
}
```

### 7.2 Common Return Types

```typescript
// API response
interface ApiResponse<T> {
  data: T;
  message?: string;
}

// Error response
interface ErrorResponse {
  error: {
    code: string;
    message: string;
    details?: Array<{ field: string; message: string }>;
    requestId: string;
  };
}

// Search result
interface SearchResult<T> {
  items: T[];
  pagination: Pagination;
}
```

---

## 8. Method Security

### 8.1 Authentication Requirements

| Method Category | Auth Required | Roles |
|-----------------|---------------|-------|
| User profile | Yes | customer |
| Order history | Yes | customer |
| Cart operations | Yes | customer |
| Checkout | Yes | customer |
| Review submission | Yes | customer |
| Catalog search | No | guest |
| Product details | No | guest |

### 8.2 Authorization Rules

| Method | Authorization |
|--------|---------------|
| `getMyProfile` | User can only access own profile |
| `updateMyProfile` | User can only update own profile |
| `getMyOrders` | User can only access own orders |
| `submitReview` | User can only review their own orders |
| `updateMyReview` | User can only update own reviews |
| `deleteMyReview` | User can only delete own reviews |

---

*End of Component Methods Document*
