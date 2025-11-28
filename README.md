# Online Shopping Website — Short Documentation

A client-side demo of an online shoe store built with **HTML, CSS, JavaScript**, **Bootstrap 5** and Material-style UI patterns.  
All data (users, products, cart, login state) is stored in the browser's **LocalStorage** — no backend required. This README documents the pages, CRUD behavior, Bootstrap & Material usage, and the login/registration workflow.

---

## Table of contents
- [Pages & Explanation](#pages--explanation)
- [CRUD operations](#crud-operations)
- [Bootstrap elements used](#bootstrap-elements-used)
- [Material components used](#material-components-used)
- [Login / Registration workflow details](#login--registration-workflow-details)
- [LocalStorage schemas](#localstorage-schemas)
- [Admin test account (default)](#admin-test-account-default)

---

## Pages & Explanation

> All filenames use underscores (important on GitHub Pages / case-sensitive hosts)

### `register.html` — Registration Page
- Purpose: allow new users to sign up.
- Fields: **name**, **email**, **password**, **confirm password**.
- Validates client-side (email format, password length ≥ 6, password match).
- On success: appends new user object to `localStorage.users`, shows a snackbar, then redirects to `login.html`.

### `login.html` — Login Page
- Purpose: user authentication to access protected pages.
- Fields: **email**, **password**.
- Validates non-empty fields, checks credentials against `localStorage.users`.
- On success: sets `localStorage.loggedInUser = <email>` and redirects to `index.html`.
- On failure: shows snackbar “Invalid credentials”.

### `index.html` — Home / Landing Page
- Protected: redirects to `login.html` if no logged-in user.
- Shows hero banner, CTA (“Start Shopping”), and a navbar.
- Navbar conditionally shows **Add Product** link if logged-in user is an admin.

### `shop.html` — Product Listing (READ)
- Loads `localStorage.products` and renders cards (image, name, description, price).
- Provides **Add to Cart** on every product.
- If logged-in user is admin, shows **Edit** (goes to `edit_product.html?id=...`) and **Delete** buttons.
- Includes a basic search/filter input.

### `add_product.html` — Add Product (CREATE) — Admin only
- Material-style form to add new product (name, description, price, image URL optional).
- If image URL is empty: auto-generates a Picsum placeholder URL.
- On submit: creates new `product` object and stores it in `localStorage.products`.

### `edit_product.html` — Edit Product (UPDATE) — Admin only
- Reads `id` from query string: `edit_product.html?id=2`.
- Loads product data into the form, allows updates and saves back to `localStorage.products`.

### `cart.html` — Shopping Cart (Cart CRUD)
- Displays items in `localStorage.cart`.
- Allows quantity updates, item removal, and clearing the entire cart.
- Shows subtotal per item and grand total.

---

## CRUD operations

### Users
- **Create**: `register.html` → pushes new user to `localStorage.users`.
- **Read**: `login.html` reads `localStorage.users` to authenticate.
- **Update** / **Delete**: not implemented in the demo (can be added later).

### Products
- **Create**: `add_product.html` → push new product to `localStorage.products`.
- **Read**: `shop.html` reads and renders `localStorage.products`.
- **Update**: `edit_product.html` updates the matched product by `id`.
- **Delete**: `shop.html` admin Delete button removes a product from `localStorage.products`.

### Cart
- **Create**: `shop.html` Add to Cart: adds `{ id, name, price, qty, imagename }` to `localStorage.cart`.
- **Read**: `cart.html` reads and displays `localStorage.cart`.
- **Update**: change `qty` in `cart.html` and save back to `localStorage.cart`.
- **Delete**: remove item or clear all in `cart.html`.

---

## Bootstrap elements used

The app uses **Bootstrap 5** for responsive layout and common components:

- **Layout & Grid**
  - `.container`, `.row`, `.col-md-*` for responsive pages and grid of product cards.
- **Navigation**
  - `.navbar`, `.navbar-brand`, `.navbar-expand-lg`, `.navbar-nav`, `.nav-link`.
- **Cards**
  - `.card`, `.card-body`, `.card-img-top` for product display.
- **Forms**
  - `.form-control`, `.form-label`, `.form-text`, `.form-floating` (if used).
- **Buttons**
  - `.btn`, `.btn-primary`, `.btn-outline-secondary`, `.btn-danger`, `.btn-success`.
- **Utilities**
  - Spacing helpers (`.mt-3`, `.mb-3`, `.p-4`), text helpers, and `.shadow-sm`.

---

## Material components & patterns used

No external Material library is required — the UI follows Material design patterns implemented with simple CSS:

- **Material Cards** — rounded corners, elevated shadows for product and hero cards.
- **Material Buttons** — pill / rounded buttons with soft elevation (`.material-btn`).
- **Material Form Inputs** — inputs with rounded corners and inset shadows to mimic Material inputs (`.form-input`).
- **Snackbar / Toast** — lightweight toast notifications implemented in pure JS/CSS (`.snackbar`).
- **Hero / Emphasis Card** — a prominent hero block on `index.html` with subtle gradient / elevation.

---

## Login / Registration workflow details

### Registration (step-by-step)
1. User opens `register.html`.
2. Client-side validation:
   - Name must not be empty.
   - Email validated with a simple regex.
   - Password ≥ 6 characters.
   - Confirm password must match.
3. Duplicate email check:
   - Script reads `localStorage.users` (array).
   - If an object with the same `email` exists, registration is rejected.
4. On success:
   - Create and append a new user object:
     ```js
     {
       id: <number>,
       name: "Jane",
       email: "jane@example.com",
       password: "plain_text_password",
       isAdmin: false
     }
     ```
   - Save back to `localStorage`:
     ```js
     localStorage.setItem('users', JSON.stringify(usersArray));
     ```
   - Show snackbar, then redirect to `login.html`.

### Login (step-by-step)
1. User opens `login.html`.
2. Input validation: checks that both fields are filled.
3. Authentication:
   - Read `users` from LocalStorage and find a user where `user.email === enteredEmail && user.password === enteredPassword`.
4. On success:
   - Set login state:
     ```js
     localStorage.setItem('loggedInUser', enteredEmail);
     ```
   - Redirect to `index.html`.
   - Navbar on protected pages checks `loggedInUser` and then looks up the user in `users` to display name and admin links.
5. On failure:
   - Show snackbar “Invalid credentials”.

> **Security note:** Passwords are stored in plain text in LocalStorage for this demo — this is acceptable for learning and prototyping only. For production, use a backend, hashed passwords, HTTPS, and secure sessions.

---

## LocalStorage schemas (examples)

### `users`
```json
[
  { "id": 1, "name": "Admin", "email": "admin@gmail.com", "password": "admin123", "isAdmin": true },
  { "id": 2, "name": "Diya",  "email": "diya@example.com",  "password": "mypwd12", "isAdmin": false }
]
