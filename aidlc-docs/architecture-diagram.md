# AnyCompanyRead - Architecture Diagram

**Version:** 1.0  
**Date:** October 9, 2026  
**AI-DLC Phase:** Architecture Diagram  
**Status:** Pending Human Approval

---

## Overview

This document contains the Mermaid architecture diagram for AnyCompanyRead, representing the approved local-first architecture.

---

## System Architecture Diagram

```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#1a1a2e', 'primaryTextColor': '#fff', 'primaryBorderColor': '#16213e', 'secondaryColor': '#0f3460', 'tertiaryColor': '#e94560'}}}%%
%%{init: {'flowchart': {'useMaxWidth': true, 'curve': 'linear'}}}%%

graph TB
    subgraph UserBrowser["User / Browser"]
        direction TB
        UC[User]
        RB[React Browser]
        UC -.->|1. HTTP Request| RB
    end

    subgraph Frontend["React + Vite Frontend"]
        direction TB
        SPA[SPA Application]
        Hooks[Custom Hooks]
        Services[Service Layer]
        
        SPA --> Hooks
        Hooks --> Services
        RB --> SPA
    end

    subgraph Backend["Express REST API + Modular Monolith"]
        direction TB
        Middle[Middleware Layer]
        Modules[Domain Modules]
        
        subgraph AuthModule["auth Module"]
            direction TB
            AuthModel["User, Session models"]
            AuthService["AuthService"]
            TokenService["TokenService"]
            AuthRoutes["Auth Routes"]
            AuthModel --> AuthService
            AuthService --> TokenService
            AuthService --> AuthRoutes
        end
        
        subgraph CatalogModule["catalog Module"]
            direction TB
            CatalogModel["Book, Publisher models"]
            CatalogService["CatalogService"]
            SearchService["SearchService"]
            CatalogRoutes["Catalog Routes"]
            CatalogModel --> CatalogService
            CatalogService --> SearchService
            CatalogService --> CatalogRoutes
        end
        
        subgraph CartModule["cart Module"]
            direction TB
            CartModel["Cart, CartItem models"]
            CartService["CartService"]
            CartRoutes["Cart Routes"]
            CartModel --> CartService
            CartService --> CartRoutes
        end
        
        subgraph CheckoutModule["checkout Module"]
            direction TB
            CheckoutService["CheckoutService"]
            CheckoutRoutes["Checkout Routes"]
            CheckoutService --> CheckoutRoutes
        end
        
        subgraph OrderModule["order Module"]
            direction TB
            OrderModel["Order, OrderItem models"]
            OrderService["OrderService"]
            FulfillmentService["FulfillmentService"]
            OrderRoutes["Order Routes"]
            OrderModel --> OrderService
            OrderService --> FulfillmentService
            OrderService --> OrderRoutes
        end
        
        subgraph DigitalModule["digital Module"]
            direction TB
            DigitalModel["DigitalOrder model"]
            DigitalService["DigitalDeliveryService"]
            DigitalRoutes["Digital Routes"]
            DigitalModel --> DigitalService
            DigitalService --> DigitalRoutes
        end
        
        subgraph UserModule["user Module"]
            direction TB
            UserModel["UserProfile, Address, Review models"]
            UserService["UserService"]
            UserRoutes["User Routes"]
            UserModel --> UserService
            UserService --> UserRoutes
        end
        
        Services --> Middle
        Middle --> Modules
        AuthRoutes --> Modules
        CatalogRoutes --> Modules
        CartRoutes --> Modules
        CheckoutService --> Modules
        OrderRoutes --> Modules
        DigitalRoutes --> Modules
        UserRoutes --> Modules
        
        CheckoutService --> AuthModule
        CheckoutService --> CartModule
        CheckoutService --> OrderModule
        CheckoutService --> DigitalModule
        
        OrderService --> OrderModel
        DigitalService --> DigitalModel
        UserService --> UserModel
    end

    subgraph Database["PostgreSQL"]
        direction TB
        DB[(PostgreSQL)]
        FTS[PostgreSQL FTS\n(tsearch2, GiST/GIN)]
        
        AuthModule -.->|Read/Write| DB
        CatalogModule -.->|Read/Write| DB
        CatalogModule -.->|Query| FTS
        CartModule -.->|Read/Write| DB
        CheckoutService -.->|Read/Write| DB
        OrderModule -.->|Read/Write| DB
        DigitalModule -.->|Read/Write| DB
        UserModule -.->|Read/Write| DB
    end

    subgraph IntegrationLayer["Integration Layer"]
        direction TB
        PaymentInterface[PaymentProvider\nInterface]
        FulfillmentInterface[FulfillmentProvider\nInterface]
        DRMInterface[DRMProvider\nInterface]
        
        subgraph LocalMock["Local Mock Providers"]
            direction TB
            MockPayment[MockPaymentProvider]
            MockFulfillment[Mock3PLProvider]
            MockDRM[MockDRMProvider]
        end
        
        subgraph OptionalProduction["Optional Production Providers"]
            direction TB
            Stripe["Stripe API"]
            Provider3PL["3PL Provider API"]
            ProviderDRM["DRM Provider API"]
        end
        
        CheckoutService --> PaymentInterface
        OrderService --> FulfillmentInterface
        DigitalService --> DRMInterface
        
        PaymentInterface -.->|Local| MockPayment
        PaymentInterface -.->|Production| Stripe
        
        FulfillmentInterface -.->|Local| MockFulfillment
        FulfillmentInterface -.->|Production| Provider3PL
        
        DRMInterface -.->|Local| MockDRM
        DRMInterface -.->|Production| ProviderDRM
    end

    classDef userFill:#e94560,stroke:#fff,stroke-width:2px,color:#fff;
    classDef frontendFill:#0f3460,stroke:#fff,stroke-width:2px,color:#fff;
    classDef backendFill:#16213e,stroke:#fff,stroke-width:2px,color:#fff;
    classDef dbFill:#1a1a2e,stroke:#fff,stroke-width:2px,color:#fff;
    classDef integrationFill:#0f3460,stroke:#fff,stroke-width:2px,color:#fff;
    classDef mockFill:#2a9d8f,stroke:#fff,stroke-width:2px,color:#fff;
    classDef productionFill:#e76f51,stroke:#fff,stroke-width:2px,color:#fff;
    classDef boundaryFill:none,stroke:none;
    classDef dashedStroke:stroke-dasharray: 5 5;

    class UC,RB frontendFill;
    class AuthModel,CatalogModel,CartModel,OrderModel,DigitalModel,UserModel dbFill;
    class AuthRoutes,CatalogRoutes,CartRoutes,CheckoutRoutes,OrderRoutes,DigitalRoutes,UserRoutes backendFill;
    class DB,FTS dbFill;
    class PaymentInterface,FulfillmentInterface,DRMInterface integrationFill;
    class MockPayment,MockFulfillment,MockDRM mockFill;
    class Stripe,Provider3PL,ProviderDRM productionFill;
    class UserBrowser,CatalogModule,AuthModule,CartModule,CheckoutModule,OrderModule,DigitalModule,UserModule boundaryFill;

    style Frontend fill:#0f3460,stroke:#fff,stroke-width:2px
    style Backend fill:#16213e,stroke:#fff,stroke-width:2px
    style Database fill:#1a1a2e,stroke:#fff,stroke-width:2px
    style IntegrationLayer fill:#0f3460,stroke:#fff,stroke-width:2px

    linkStyle 0 stroke:#e94560,stroke-width:2px;
    linkStyle 1 stroke:#0f3460,stroke-width:2px;
    linkStyle 2 stroke:#16213e,stroke-width:2px;
    linkStyle 3 stroke:#1a1a2e,stroke-width:2px;
    linkStyle 4 stroke:#0f3460,stroke-width:2px;
```

---

## Primary Request Flows

### 1. User Authentication Flow

```
User → React Frontend → AuthRoutes → AuthService → TokenService → PostgreSQL
                                    ↓
                            Mock/Stripe Payment
```

### 2. Search Flow

```
User → React Frontend → CatalogRoutes → SearchService → PostgreSQL FTS
                                        ↓
                                PostgreSQL
```

### 3. Cart Checkout Flow

```
User → React Frontend → CheckoutService → CartService → PostgreSQL
                                    ↓
                              PaymentProvider
                                    ↓
                              FulfillmentProvider
                                    ↓
                              OrderService → PostgreSQL
```

### 4. Digital Delivery Flow

```
User → React Frontend → DigitalRoutes → DigitalService → DRMProvider
```

### 5. User Profile Flow

```
User → React Frontend → UserRoutes → UserService → PostgreSQL
```

---

## Component Boundaries

### Frontend Boundary
- React + Vite application
- Client-side routing
- State management via React Query and Context API
- No server-side processing

### Backend Boundary
- Single Express.js application
- Modular monolith with domain boundaries
- All modules deployed together

### Database Boundary
- PostgreSQL for all data persistence
- PostgreSQL FTS for search functionality

### External Provider Boundary
- Provider interfaces for pluggable implementations
- Mock implementations for local development
- Production implementations via environment configuration

---

## External Provider Boundaries

### Payment Provider
| Environment | Implementation |
|-------------|----------------|
| Local | `MockPaymentProvider` |
| Production | `StripePaymentProvider` |

### Fulfillment Provider
| Environment | Implementation |
|-------------|----------------|
| Local | `Mock3PLProvider` |
| Production | `Real3PLProvider` |

### DRM Provider
| Environment | Implementation |
|-------------|----------------|
| Local | `MockDRMProvider` |
| Production | `RealDRMProvider` |

---

## Local vs Optional External Integrations

### Local-Only (No External Dependencies)
- React + Vite frontend
- Node.js + Express backend
- PostgreSQL database (Docker or local)
- MockPaymentProvider
- Mock3PLProvider
- MockDRMProvider

### Optional External (Require Configuration)
- Stripe API (for payment processing)
- 3PL Provider API (for fulfillment)
- DRM Provider API (for digital rights management)
- Email service (SendGrid/Mailgun for verification emails)

---

*End of Architecture Diagram Document*
