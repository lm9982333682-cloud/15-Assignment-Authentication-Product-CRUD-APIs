# Apex Commerce — MERN E-commerce Management Project

A full-stack commerce management project built with React and Vite on the client, and Express, Node.js, and MongoDB on the server. The supplied frontend is mainly an **admin dashboard**; the backend also models customer and seller roles.

## Features present in the supplied code

### Admin

- View dashboard statistics: user/product/order counts, delivered-order revenue, and recent orders.
- View, add, edit, and delete product records; manage any product.
- View order lists and order details.
- Update order status (`pending`, `confirmed`, `shipped`, `delivered`, `cancelled`) and order notes.
- Search/paginate users and view user details.
- Change another user's role between `user`, `seller`, and `admin`.

### Seller

- Product records include a seller/owner reference.
- The backend product controller allows a product owner to edit or delete their own product.
- A separate seller dashboard is not included in the supplied client. The current product-create route requires login but does not explicitly enforce the seller role; this should be tightened if seller-only product creation is intended.

### Customer (`user` role)

- Register and sign in.
- Read public product records through the API.
- Create an order and view their own orders through authenticated API endpoints.
- The supplied client does not currently include a full public shopping storefront, cart, checkout/payment UI, or shipping-address flow.

## Technology

**Client:** React 19, Vite, React Router, Redux Toolkit, Axios, Tailwind CSS 4, Lucide React, React Toastify.

**Server:** Node.js, Express 5, MongoDB/Mongoose, JWT access/refresh tokens, bcrypt, express-validator, Multer, cookie-parser, CORS, ImageKit.

## Repository layout

```text
ApexCommerce/
├── README.md
├── client/
│   ├── README.md
│   ├── package.json
│   └── src/
│       ├── components/
│       ├── layout/
│       ├── pages/
│       ├── services/
│       ├── store/
│       └── router.jsx
└── server/
    ├── README.md
    ├── package.json
    ├── server.js
    └── src/
        ├── config/
        ├── controllers/
        ├── middlewares/
        ├── models/
        ├── routes/
        ├── services/
        ├── utils/
        └── validators/
```

## Requirements

- Node.js and npm
- MongoDB (local or hosted)
- ImageKit private key for product image uploads

## Setup

### 1. Configure the server

Create `server/.env` with your own values:

```dotenv
PORT=4500
MONGO_URI=your_mongodb_connection_string
CLIENT_URL=http://localhost:5173
ACCESS_TOKEN_SECRET=replace_with_a_long_random_secret
REFRESH_TOKEN_SECRET=replace_with_another_long_random_secret
NODE_ENV=development
IMAGEKIT_PRICATE_KEY=your_imagekit_private_key
```

The key is spelled `IMAGEKIT_PRICATE_KEY` in the current source code. Keep the real `.env` file private and out of Git.

Install and start the server:

```bash
cd server
npm install
npm run dev
```

The backend defaults to port `5000` if `PORT` is not set. The client currently targets `http://localhost:4500/api`, so either set `PORT=4500` as above or update the client API URL.

### 2. Configure and run the client

Open a second terminal at the repository root:

```bash
cd client
npm install
npm run dev
```

Vite normally serves the client at `http://localhost:5173`.

## Useful commands

### Client (`client/`)

```bash
npm run dev      # start development server
npm run build    # create production build
npm run preview  # preview production build
npm run lint     # run ESLint
```

### Server (`server/`)

```bash
npm run dev      # start server with Nodemon
npm start        # currently also runs Nodemon
```

For production hosting, configure the server start command to use Node directly and confirm the host installs all runtime dependencies.

## Backend API overview

All API endpoints use the `/api` prefix.

| Base path | Main endpoints | Purpose |
| --- | --- | --- |
| `/api/auth` | `POST /register`, `POST /login`, `GET /me`, `POST /refresh`, `POST /logout` | Authentication and session refresh |
| `/api/products` | `GET /`, `GET /:id`, `POST /`, `PUT /:id`, `DELETE /:id` | Product listing and management |
| `/api/orders` | `POST /`, `GET /`, `GET /admin/all`, `GET /:id`, `PATCH /:id/status`, `PATCH /:id/notes`, `GET /admin/stats` | Order creation, customer order history, and admin order management |
| `/api/dashboard` | `GET /stats` | Admin dashboard statistics |
| `/api/users` | `GET /`, `GET /stats`, `GET /:id`, `PATCH /:id/role` | Admin user management |

See [`server/README.md`](server/README.md) for endpoint methods, access rules, environment variables, and implementation notes. See [`client/README.md`](client/README.md) for client routes and scripts.

## Data models

- **User:** name, email, password hash, role (`user`, `seller`, `admin`), refresh token, timestamps.
- **Product:** title, description, price, images, sizes, category, stock, status, seller reference, timestamps.
- **Order:** user reference, product line items, total amount, status, notes, timestamps.

The current Order schema does not include payment status/method or shipping address, and a payment provider is not configured in the supplied project.

## Before deploying

- Set production environment variables on the hosting provider; do not upload `.env` files or secrets to GitHub.
- Change `client/src/services/api.js` from the local API URL to the deployed server URL.
- Set `CLIENT_URL` to the deployed frontend origin and configure HTTPS/CORS/cookies correctly.
- Configure SPA fallback to `index.html` for frontend routes.
- Review server-side role enforcement, especially product creation.
- In `server/src/routes/orderRoutes.js`, move `GET /admin/stats` above `GET /:id`; otherwise `/admin/stats` may be captured by the ID route.
- Build the client and test login, refresh, products, orders, user management, and image uploads against the deployed API.

## Security

Never commit MongoDB connection strings, JWT secrets, ImageKit keys, or any real credentials. Use `.env.example` with placeholder values if you need to document required variables.
