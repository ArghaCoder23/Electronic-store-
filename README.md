# VOLTIX

VOLTIX is a responsive electronics storefront and local development API built with React, TypeScript, Vite, Tailwind CSS, Express, and MongoDB/Mongoose dependencies.

## Requirements

- Node.js 20 or later
- npm
- MongoDB is optional for the seeded local demo

## Setup

Install the frontend and backend dependencies from the project root:

```sh
npm run install:all
```

Copy `server/.env.example` to `server/.env` and set a unique `JWT_SECRET` (at least 32 random bytes). In production, the server refuses to start without this value. In local development, if it is omitted, the server generates a temporary secret that changes at restart and invalidates existing sessions.

Run the API and frontend in separate terminals:

```sh
npm run dev:server
npm run dev:client
```

The client runs at `http://localhost:5173`, and the API runs at `http://localhost:5001`. The Vite development server proxies `/api` requests to the API. The API health check is available at `/api/health`.

## Development accounts

The in-memory demo store initializes these accounts:

| Role | Email | Password |
|---|---|---|
| Admin | `admin@voltix.io` | `Admin@12345` |
| Customer | `customer@voltix.io` | `Customer@12345` |

These are public demo credentials. Do not deploy them or use them with real customer data. User accounts and order data in the current demo store are reset when the backend process restarts.

## Data and integrations

The server can connect to MongoDB using `MONGODB_URI`; when MongoDB is unavailable, the local demo starts with sample in-memory data. The current request handlers use that in-memory store, so a successful MongoDB connection does **not** make catalog, account, cart, or order data persistent yet. Use a disposable development database only.

Online card, UPI, and Razorpay payments are not configured. Checkout accepts Cash on Delivery only; the server rejects other payment methods and does not report an online payment as completed. The development password-reset token is shown only in development, expires after 15 minutes, and is one-time use. Production email delivery is not implemented.

The sample catalog contains 107 deterministic demonstration products across 20 categories. Displayed prices, ratings, stock levels, and product condition are sample catalog data and do not represent verified inventory. The seller workflow supports private authenticated submissions, admin approval/rejection, and publishing approved listings to the sample catalog. Seller photos currently use validated HTTP(S) image URLs; secure file upload/storage and seller payouts are not configured. Seller listings and all catalog/order state remain in memory and reset when the server restarts.

## API overview

All routes are prefixed with `/api`. Authentication uses an HTTP-only `voltix_token` cookie.

| Method | Route | Purpose |
|---|---|---|
| `GET` | `/health` | API health |
| `GET` | `/products` | Search, filter, sort, and paginate sample products |
| `GET` | `/products/:slug` | Product detail and related products |
| `GET` | `/categories` | List categories |
| `POST` | `/auth/register` | Register a customer |
| `POST` | `/auth/login` | Sign in |
| `POST` | `/auth/logout` | Clear the session cookie |
| `GET` | `/auth/me` | Get the current account |
| `POST` | `/auth/forgot-password` | Request password reset instructions |
| `POST` | `/auth/reset-password` | Reset a password using a valid reset token |
| `GET` / `POST` | `/cart` and `/cart/items` | Read or update the guest/account cart |
| `GET` | `/wishlist` | Read the current wishlist |
| `POST` | `/orders` | Create an order after server-side price and inventory checks |
| `GET` | `/orders/my-orders` | List the signed-in customer's orders |
| `POST` | `/contact` | Submit a contact message |
| `POST` | `/contact/newsletter` | Subscribe an email address |
| `GET` | `/admin/overview` | Admin analytics (admin session required) |
| `POST` | `/sell/listings` | Submit a private marketplace listing (signed-in seller) |
| `GET` | `/sell/dashboard` | Read the signed-in seller's listings and summary |
| `PATCH` | `/sell/listings/:id` | Withdraw a published listing or edit an eligible listing |
| `POST` | `/sell/listings/:id/submit` | Resubmit a rejected/draft listing |
| `GET` | `/admin/listings` | List marketplace submissions for admin review |
| `POST` | `/admin/listings/:id/approve` | Approve and publish a listing |
| `POST` | `/admin/listings/:id/reject` | Reject a submission with a reason |

Admin catalog and fulfillment endpoints are under `/products` and `/orders/admin`; they require an authenticated admin account. Request and response shapes are defined by the route controllers in `server/src`.

## Validation

```sh
npm run build
```

This builds and type-checks both the Express server and Vite client.
