# 🍽️ Cloud Kitchen Management System

A full-stack cloud kitchen ordering and operations platform with
separate **customer** and **admin** applications. Customers can browse
the menu, manage a cart, apply offers, place and track orders, manage
their profile, make payments, reorder previous purchases, cancel
eligible orders, and contact support. Administrators can manage orders,
menu items, chefs, kitchen assignments, support tickets, and business
analytics.

The project supports a lightweight JSON-backed development mode as well
as a **PostgreSQL-backed production mode**, and includes deployment
configuration for **Netlify** and **Render**.

![Cloud Kitchen Banner](Images/banner_new.png)

------------------------------------------------------------------------

## Table of Contents

-   [Project Overview](#project-overview)
-   [Main Features](#main-features)
-   [Customer Application](#customer-application)
-   [Admin Dashboard](#admin-dashboard)
-   [Tech Stack](#tech-stack)
-   [System Architecture](#system-architecture)
-   [Project Structure](#project-structure)
-   [Database Design](#database-design)
-   [API Overview](#api-overview)
-   [Getting Started](#getting-started)
-   [Environment Variables](#environment-variables)
-   [Running the Project](#running-the-project)
-   [Database Migration and Seeding](#database-migration-and-seeding)
-   [Build Commands](#build-commands)
-   [Deployment](#deployment)
-   [Screenshots](#screenshots)
-   [Security Notes](#security-notes)
-   [Future Improvements](#future-improvements)
-   [License](#license)

------------------------------------------------------------------------

## Project Overview

The Cloud Kitchen Management System connects the customer ordering
experience with day-to-day kitchen operations.

Instead of treating the customer website and kitchen dashboard as
separate projects, the system connects them through a common backend API
and database.

### Customer flow

``` text
Register / Login
       ↓
Browse Menu & Specials
       ↓
Add Items to Cart
       ↓
Apply Offer / Coupon
       ↓
Checkout & Payment
       ↓
Order Created
       ↓
Track Order Status
       ↓
Delivery / Reorder / Support
```

### Kitchen flow

``` text
Admin Login
     ↓
View Incoming Orders
     ↓
Assign Chef / Items
     ↓
Update Order Status
     ↓
Handle Support Requests
     ↓
Monitor Orders & Analytics
```

------------------------------------------------------------------------

## Main Features

### Customer Application

The customer-facing React application provides the complete ordering
experience.

-   User registration and login
-   Username/email based account support
-   Password reset and password change flows
-   Customer profile management
-   Saved address and default payment preference
-   Menu browsing
-   Food categories
-   Vegetarian item information
-   Item descriptions, prices, ratings and preparation time
-   Today's special items
-   Shopping cart
-   Add, update and remove cart items
-   Clear cart
-   Coupon and offer application
-   New-user-only coupon support
-   Checkout
-   Multiple payment modes
-   Order creation
-   Order history
-   Order status tracking
-   Order cancellation support
-   Cancellation fee support
-   Reordering previous orders
-   Support ticket creation
-   Customer/admin support conversation
-   Responsive UI

### Admin Dashboard

The admin application is a separate React/Vite interface for kitchen
operations.

-   Admin authentication
-   Operational overview/dashboard
-   View current and previous orders
-   Update order status
-   Menu management
-   Add/update menu information
-   Chef management
-   Chef availability/on-duty tracking
-   Assign a chef to an order
-   Assign individual order items to chefs
-   View and manage support tickets
-   Reply to customer support requests
-   Analytics endpoints for business/order data
-   Kitchen operations monitoring

### Orders and Kitchen Operations

The backend stores an order independently from the cart so historical
order information remains available.

Order-related data includes:

-   Customer name
-   Phone number
-   Delivery address
-   Ordered items
-   Quantity
-   Item price at the time of ordering
-   Subtotal
-   Discount
-   Delivery fee
-   Tax
-   Cancellation fee
-   Final total
-   Applied offer
-   Payment mode
-   Payment status
-   Payment reference
-   Order status
-   ETA
-   Cancellation reason
-   Creation, cancellation and delivery timestamps

The system also keeps an **order status history**, allowing status
changes to be recorded over the lifetime of an order.

### Chef Assignment

Kitchen staff can be represented as chefs with:

-   Name
-   Station
-   On-duty status
-   Active status
-   Last-seen time

The database supports:

1.  assigning one chef to an entire order, and
2.  assigning different order items to different chefs.

This allows the system to model kitchens where different stations
prepare different parts of the same order.

### Offers and Coupons

Offers support:

-   Percentage discounts
-   Flat discounts
-   Minimum order values
-   Active/inactive status
-   Unique offer codes
-   New-user-only offers

### Payments

The project contains a payment abstraction that can run in mock mode and
includes configuration/routes for Razorpay integration.

Payment records contain:

-   Provider
-   Amount
-   Currency
-   Transaction status
-   Gateway order ID
-   Gateway payment ID
-   Gateway signature
-   Metadata
-   Creation and capture timestamps

The default currency is **INR**.

------------------------------------------------------------------------

## Tech Stack

  Layer                     Technology
  ------------------------- ----------------------------------------------
  Customer Frontend         React 19, React DOM, React Router
  Admin Frontend            React 19, React DOM, React Router
  Frontend Build Tool       Vite 8
  Backend                   Node.js
  Production Database       PostgreSQL
  PostgreSQL Driver         `pg`
  Local Data Mode           JSON files
  Concurrent Development    `concurrently`
  Frontend Hosting Config   Netlify
  Backend Hosting Config    Render
  Payment Configuration     Mock provider / Razorpay-ready configuration

### Runtime Requirement

``` text
Node.js >= 18
```

The included Render configuration uses Node.js 22.

------------------------------------------------------------------------

## System Architecture

``` text
                         ┌─────────────────────────┐
                         │        Customer         │
                         └────────────┬────────────┘
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │ Customer React App      │
                         │ Vite / React Router     │
                         └────────────┬────────────┘
                                      │
                                      │ /api/*
                                      ▼
┌───────────────────┐     ┌─────────────────────────┐
│      Admin        │────▶│      Node.js API        │
└─────────┬─────────┘     │ server.postgres.js      │
          │               └────────────┬────────────┘
          ▼                            │
┌───────────────────┐                  │ SQL
│ Admin React App   │                  ▼
│ Vite              │        ┌──────────────────────┐
└───────────────────┘        │      PostgreSQL      │
                             │ Users, Orders, Menu, │
                             │ Payments, Chefs, etc.│
                             └──────────────────────┘
```

### Production deployment

``` text
Browser
   │
   ▼
Netlify
   │
   ├── /customer-react/*  → Customer React application
   ├── /admin-react/*     → Admin React application
   │
   └── /api/*             → Render backend
                                │
                                ▼
                           PostgreSQL
```

------------------------------------------------------------------------

## Project Structure

``` text
cloud-kitchen-management-system/
│
├── admin-app/
│   ├── src/
│   │   ├── AdminApp.jsx
│   │   ├── main.jsx
│   │   └── styles.css
│   ├── index.html
│   └── vite.config.js
│
├── customer-app/
│   ├── src/
│   │   ├── CustomerApp.jsx
│   │   ├── main.jsx
│   │   └── styles.css
│   ├── index.html
│   └── vite.config.js
│
├── config/
│   └── load-env.js
│
├── db/
│   ├── migrations/
│   │   ├── 001_init.sql
│   │   ├── 002_admin_ops.sql
│   │   ├── 003_item_assignment_and_support_replies.sql
│   │   ├── 004_payments.sql
│   │   ├── 005_usernames_and_password_policy.sql
│   │   ├── 006_new_user_coupons.sql
│   │   ├── 007_user_profile_fields.sql
│   │   └── 008_cancellation_fees.sql
│   │
│   └── seeds/
│       ├── 001_seed.sql
│       ├── 002_chefs.sql
│       └── 003_extra_offers.sql
│
├── Images/
│   ├── Admin_Portal/
│   ├── Customer_Site/
│   └── banner_new.png
│
├── public/
│   ├── admin-react/
│   ├── customer-react/
│   ├── assets/
│   └── legacy/static frontend files
│
├── scripts/
│   ├── migrate.js
│   ├── seed.js
│   └── backfill_usernames.js
│
├── server/
│   └── data/
│       ├── carts.json
│       ├── menu.json
│       ├── offers.json
│       ├── orders.json
│       └── users.json
│
├── .env.example
├── .gitignore
├── LICENSE
├── netlify.toml
├── package.json
├── package-lock.json
├── render.yaml
├── server.js
└── server.postgres.js
```

### Important Files

  ------------------------------------------------------------------------
  File                                 Purpose
  ------------------------------------ -----------------------------------
  `server.js`                          Lightweight/local backend using
                                       JSON data files

  `server.postgres.js`                 PostgreSQL-backed backend intended
                                       for production

  `customer-app/src/CustomerApp.jsx`   Main customer React application

  `admin-app/src/AdminApp.jsx`         Main admin React application

  `scripts/migrate.js`                 Runs PostgreSQL database migrations

  `scripts/seed.js`                    Loads initial seed data

  `scripts/backfill_usernames.js`      Utility for populating usernames
                                       for existing users

  `.env.example`                       Example environment configuration

  `render.yaml`                        Render backend deployment
                                       configuration

  `netlify.toml`                       Netlify frontend build, routing and
                                       API proxy configuration
  ------------------------------------------------------------------------

------------------------------------------------------------------------

## Database Design

The PostgreSQL version is built through incremental SQL migrations.

### Main Tables

#### `users`

Stores customer accounts.

Important fields include:

-   `id`
-   `name`
-   `username`
-   `email`
-   `password_hash`
-   `phone`
-   `address`
-   `default_payment_mode`
-   `created_at`

#### `menu_items`

Stores food/menu information.

Important fields include:

-   `id`
-   `name`
-   `description`
-   `price`
-   `rating`
-   `prep_minutes`
-   `category`
-   `image`
-   `is_veg`
-   `is_active`

#### `offers`

Stores promotional offers and coupon codes.

Supports percentage discounts, flat discounts, minimum order amounts and
new-user restrictions.

#### `carts`

Stores a cart by session ID and its currently applied offer.

#### `cart_items`

Connects menu items to a cart and stores their quantities.

#### `orders`

Stores checkout and order-level information including customer details,
payment state, order state, totals, ETA and cancellation information.

#### `order_items`

Stores a snapshot of each item purchased in an order.

#### `order_status_history`

Stores the timeline of order status changes.

#### `chefs`

Stores kitchen staff/station information.

#### `order_assignments`

Assigns a chef to an entire order.

#### `order_item_assignments`

Allows individual items in an order to be assigned to different chefs.

#### `support_tickets`

Stores customer support requests related to orders.

#### `support_ticket_replies`

Stores customer/admin messages associated with a support ticket.

#### `payment_transactions`

Stores payment attempts and payment gateway information.

#### `idempotency_keys`

Stores completed responses for idempotent operations and helps prevent
duplicate processing.

------------------------------------------------------------------------

## API Overview

The PostgreSQL backend exposes REST-style API routes.

### Authentication

``` text
POST /api/auth/register
POST /api/auth/login
POST /api/auth/change-password
POST /api/auth/reset-password
POST /api/admin/auth/login
```

### Customer Profile

``` text
GET/UPDATE /api/profile
```

### Menu and Categories

``` text
GET /api/menu
GET /api/categories
GET /api/special/today
```

### Offers

``` text
GET /api/offers
```

### Cart

``` text
GET  /api/cart
POST /api/cart/add
...  /api/cart/item
...  /api/cart/offer
...  /api/cart/clear
```

The exact HTTP method depends on the cart operation implemented by the
backend.

### Checkout and Orders

``` text
POST /api/checkout
GET  /api/orders
...  /api/orders/:id
```

Order-specific routes handle operations such as retrieving order
information, status-related actions, cancellation and other order
workflows implemented by the application.

### Payments

``` text
GET/POST /api/payments/config
POST     /api/payments/session
POST     /api/payments/attempt
POST     /api/payments/verify
POST     /api/webhooks/razorpay
```

### Customer Support

``` text
/api/support/tickets/:...
```

### Admin

``` text
/api/admin/overview
/api/admin/analytics
/api/admin/orders
/api/admin/orders/:...
/api/admin/menu
/api/admin/menu/:...
/api/admin/chefs
/api/admin/chefs/:...
/api/admin/tickets
/api/admin/tickets/:...
```

### Health Check

``` text
/api/health
```

The Render configuration also uses `/api/menu` as its service
health-check path.

------------------------------------------------------------------------

## Getting Started

### Prerequisites

Install the following before running the project:

-   Node.js 18 or newer
-   npm
-   Git
-   PostgreSQL, if using the database-backed version

Check your versions:

``` bash
node --version
npm --version
git --version
```

For PostgreSQL development, also verify:

``` bash
psql --version
```

### 1. Clone the Repository

``` bash
git clone <your-repository-url>
cd cloud-kitchen-management-system
```

### 2. Install Dependencies

``` bash
npm install
```

### 3. Create Environment Configuration

Copy `.env.example` to `.env`.

Windows Command Prompt:

``` cmd
copy .env.example .env
```

PowerShell:

``` powershell
Copy-Item .env.example .env
```

macOS/Linux:

``` bash
cp .env.example .env
```

Update the values in `.env` for your machine.

------------------------------------------------------------------------

## Environment Variables

The project currently defines the following environment variables:

``` env
PORT=3000
DATABASE_URL=postgres://postgres:postgres@localhost:5432/cloud_kitchen

ADMIN_KEY=change-me-in-production
ADMIN_USERNAME=manager
ADMIN_PASSWORD=manager123

PAYMENT_PROVIDER=mock
PAYMENT_CURRENCY=INR

UPI_RECEIVER_VPA=
UPI_RECEIVER_NAME=Cloud Kitchen

RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
RAZORPAY_WEBHOOK_SECRET=
```

### Variable Reference

  Variable                    Purpose
  --------------------------- ------------------------------------------
  `PORT`                      Backend HTTP port
  `DATABASE_URL`              PostgreSQL connection string
  `ADMIN_KEY`                 Secret/admin authorization configuration
  `ADMIN_USERNAME`            Admin login username
  `ADMIN_PASSWORD`            Admin login password
  `PAYMENT_PROVIDER`          Selected payment provider, e.g. `mock`
  `PAYMENT_CURRENCY`          Payment currency, default `INR`
  `UPI_RECEIVER_VPA`          UPI receiver address
  `UPI_RECEIVER_NAME`         Name displayed for the UPI receiver
  `RAZORPAY_KEY_ID`           Razorpay public/key ID
  `RAZORPAY_KEY_SECRET`       Razorpay secret
  `RAZORPAY_WEBHOOK_SECRET`   Secret used to verify Razorpay webhooks

> **Never commit your real `.env` file, database password, admin
> password, Razorpay secret or other production secrets to GitHub.**

The repository should contain `.env.example` only as a template.

------------------------------------------------------------------------

## Running the Project

The repository supports two backend approaches.

### Option 1: Lightweight JSON Backend

This mode uses the JSON files inside `server/data/`.

Run:

``` bash
npm start
```

or:

``` bash
npm run dev
```

Both commands currently execute:

``` bash
node server.js
```

This mode is useful for simple local testing without PostgreSQL.

### Option 2: PostgreSQL Backend

Set `DATABASE_URL` in `.env`, then run migrations and seed data:

``` bash
npm run db:migrate
npm run db:seed
npm run start:db
```

`start:db` executes:

``` bash
node server.postgres.js
```

### Run the React Applications Separately

Customer application:

``` bash
npm run customer:dev
```

Default Vite development port:

``` text
http://localhost:5174
```

Admin application:

``` bash
npm run admin:dev
```

Default Vite development port:

``` text
http://localhost:5173
```

The Vite development servers proxy `/api` and `/assets` requests to the
backend.

### Run PostgreSQL Backend + Both React Apps

The project includes a combined development command:

``` bash
npm run dev:all
```

It starts:

-   PostgreSQL backend
-   Admin Vite application
-   Customer Vite application

using `concurrently`.

For the included Vite proxy configuration, the development API target is
`http://127.0.0.1:3001`. Make sure the backend port/environment used for
this workflow matches the proxy target.

------------------------------------------------------------------------

## Database Migration and Seeding

### Run Migrations

``` bash
npm run db:migrate
```

The migrations build the database progressively.

``` text
001_init.sql
    Base users, menu, offers, carts, orders and support

002_admin_ops.sql
    Chefs and order assignments

003_item_assignment_and_support_replies.sql
    Item-level chef assignments and support replies

004_payments.sql
    Payment transactions

005_usernames_and_password_policy.sql
    Username support

006_new_user_coupons.sql
    New-user-only offers

007_user_profile_fields.sql
    Address and default payment mode

008_cancellation_fees.sql
    Order cancellation fee
```

### Seed Initial Data

``` bash
npm run db:seed
```

The seed directory contains initial application data including base
menu/offers, chefs and additional offers.

### Username Backfill

For an existing database that needs usernames populated, the project
also contains:

``` bash
node scripts/backfill_usernames.js
```

Use database maintenance scripts carefully and back up production data
before running one against an existing deployment.

------------------------------------------------------------------------

## Build Commands

### Customer Production Build

``` bash
npm run customer:build
```

The generated customer application is written to:

``` text
public/customer-react/
```

### Admin Production Build

``` bash
npm run admin:build
```

The generated admin application is written to:

``` text
public/admin-react/
```

### Preview Builds

``` bash
npm run customer:preview
npm run admin:preview
```

------------------------------------------------------------------------

## Deployment

The repository includes deployment configuration for:

-   **Netlify** for the frontend
-   **Render** for the Node.js backend
-   **PostgreSQL** for persistent production data

### Backend: Render

`render.yaml` defines a Node.js web service.

Important configuration:

``` yaml
buildCommand: npm install
startCommand: npm run start:db
healthCheckPath: /api/menu
```

The Render configuration expects production environment variables
including:

``` text
DATABASE_URL
ADMIN_KEY
ADMIN_USERNAME
ADMIN_PASSWORD
PAYMENT_PROVIDER
PAYMENT_CURRENCY
UPI_RECEIVER_VPA
UPI_RECEIVER_NAME
```

Create secure values in the Render dashboard instead of committing them
to the repository.

Before using the application, run the database migrations and required
seed data against the production PostgreSQL database.

### Frontend: Netlify

`netlify.toml` builds both React applications:

``` toml
command = "npm run customer:build && npm run admin:build"
publish = "public"
```

It also configures SPA routing for:

``` text
/customer-react/*
/admin-react/*
```

and proxies backend requests from:

``` text
/api/*
/assets/*
```

to the Render backend.

### Important Before Your Deployment

The repository's current `netlify.toml` contains a specific Render URL:

``` text
https://ck-project.onrender.com
```

If you deploy your own Render service under another URL, replace that
URL in the `/api/*` and `/assets/*` redirects with **your Render backend
URL** before deploying to Netlify.

Example:

``` toml
[[redirects]]
  from = "/api/*"
  to = "https://YOUR-BACKEND.onrender.com/api/:splat"
  status = 200
  force = true
```

Do the same for `/assets/*`.

### Deployment Flow

``` text
GitHub Repository
      │
      ├──────────────▶ Render
      │                 │
      │                 ├── Node.js backend
      │                 └── PostgreSQL connection
      │
      └──────────────▶ Netlify
                        │
                        ├── Customer React app
                        └── Admin React app
```

After deployment, test at least:

1.  Customer registration and login
2.  Menu loading
3.  Add/remove/update cart items
4.  Coupon application
5.  Checkout
6.  Payment flow
7.  Order history and tracking
8.  Order cancellation
9.  Profile updates
10. Support tickets and replies
11. Admin login
12. Order management
13. Chef assignment
14. Menu management
15. Analytics
16. Mobile/responsive layout

------------------------------------------------------------------------

## Screenshots

### Customer Landing Page

![Customer Landing Page](Images/Customer_Site/landing_page.png)

### Menu

![Menu](Images/Customer_Site/menu_page.png)

### Starters

![Starters](Images/Customer_Site/menu_starter.png)

### Main Course

![Main Course](Images/Customer_Site/menu_mainCourse.png)

### Beverages

![Beverages](Images/Customer_Site/menu_beverages.png)

### Specials

![Specials](Images/Customer_Site/specials.png)

### Cart

![Cart](Images/Customer_Site/cart_page.png)

### Orders

![Orders](Images/Customer_Site/orders_page.png)

### Payments

![Payments](Images/Customer_Site/payments_page.png)

### Customer Profile

![Customer Profile](Images/Customer_Site/profile%20page.png)

### Admin Dashboard

![Admin Dashboard](Images/Admin_Portal/admin_landing.png)

### Admin Analytics

![Admin Analytics](Images/Admin_Portal/admin_analytics.png)

### Admin Past Orders

![Admin Past Orders](Images/Admin_Portal/admin_pastOrders.png)

------------------------------------------------------------------------

## Security Notes

Before deploying publicly:

-   Change the example admin username/password.
-   Use a strong random `ADMIN_KEY`.
-   Never commit `.env`.
-   Never expose `DATABASE_URL`.
-   Never expose `RAZORPAY_KEY_SECRET`.
-   Never expose `RAZORPAY_WEBHOOK_SECRET`.
-   Store production secrets in Render/hosting environment variables.
-   Use HTTPS in production.
-   Keep PostgreSQL access restricted.
-   Validate payment webhooks before treating payments as successful.
-   Review authentication/session handling before using the project for
    real financial transactions.
-   Back up production data before running database maintenance scripts.
-   Do not use the example development credentials in production.

------------------------------------------------------------------------

## Future Improvements

Possible extensions to the current system include:

-   Real-time order updates using WebSockets
-   Delivery partner/rider dashboard
-   Live delivery tracking
-   Inventory and ingredient management
-   Low-stock alerts
-   Recipe-level ingredient consumption
-   Automated kitchen station routing
-   Customer ratings and written reviews
-   Email/SMS/WhatsApp order notifications
-   Refund management
-   More payment gateways
-   Role-based admin accounts
-   Fine-grained admin permissions
-   Sales reports and downloadable invoices
-   Multi-kitchen/branch support
-   Automated testing
-   Docker-based deployment
-   CI/CD workflows
-   Centralized logging and production monitoring

------------------------------------------------------------------------

## Development Notes

This repository contains some older/static frontend files in `public/`
in addition to the newer React applications. The active React source
applications are located in:

``` text
customer-app/
admin-app/
```

Their production builds are generated into:

``` text
public/customer-react/
public/admin-react/
```

When making UI changes, edit the React source files and rebuild rather
than editing generated bundle files directly.

------------------------------------------------------------------------

## License

This project includes an MIT License. See the [`LICENSE`](LICENSE) file
for the full license text.

------------------------------------------------------------------------

## Author

**Jalaj**

Student developer working with full-stack web development, software
projects and application development.

------------------------------------------------------------------------

## Repository Summary

**Cloud Kitchen Management System** is a full-stack food ordering and
kitchen operations project built with React, Node.js and PostgreSQL. It
combines customer ordering, cart and checkout, offers, payments, order
tracking, support, chef assignment, menu management and admin analytics
in one deployable application.
