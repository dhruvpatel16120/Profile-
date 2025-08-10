# 🚀 Gas Agency System — Full Development Roadmap (4 Weeks / 8 Phases)

## 📋 Overview
This is a **full-stack Next.js application** that provides:
- **User Dashboard** — for customers to book gas cylinders, make payments, view booking history, and get notices.
- **Admin Dashboard** — for agency staff to manage bookings, payments, deliveries, users, and announcements.
- **Database** — PostgreSQL hosted on Railway, managed with Prisma ORM.
- **Auth** — Firebase Authentication (Email+Password, Google SSO) for users, NextAuth.js (Credentials) for admins.
- **Payments** — Razorpay Payment Gateway.
- **Emails** — Gmail SMTP for transactional messages (order confirmations, booking updates).
- **UI/UX** — Tailwind CSS, responsive, modern, **Orange (#ff6600) + Black (#000000)** light theme.

---

## 📌 Additional Functional Requirements (From Project Specification)

### **Initial Cylinder Allocation**
- On user registration:
  - Create a `cylinderBalance` field with a default value of **12**.
  - Create an `allocationExpiry` date set to **current date + 1 year**.
- Booking restrictions:
  - Users cannot book more cylinders if `cylinderBalance` is **0** and `allocationExpiry` is still in the future.
  - If allocation period has expired, balance resets to 12 automatically.
- Admin control:
  - Admin dashboard should include a **"Reset Balance"** button for individual users.
  - Option to adjust cylinder balance manually.

---

### **Additional Payment Methods**
- **Cash on Delivery (COD)**:
  - User can select COD during checkout.
  - Booking record is created with `paymentStatus = PENDING` and `paymentMethod = COD`.
  - Admin can later mark COD payment as received.
- **Paytm QR Code Payment**:
  - Display static Paytm QR code on checkout page.
  - Allow users to enter **transaction ID** and upload a **payment screenshot**.
  - Admin reviews and marks payment as successful.
- Maintain consistent payment status tracking for all methods.

---

### **Account Balance Emails**
- After every booking/payment:
  - Send an email showing:
    - Booking details
    - Payment confirmation
    - Updated cylinder balance
    - Allocation expiry date
- Use Gmail SMTP for sending transactional emails.
- Templates should be consistent and professional.

---

### **Expanded Logging**
- Log **all CRUD actions** across the system, including:
  - User login attempts (success/failure)
  - User registration
  - Booking creation, update, deletion
  - Payment status updates
  - Profile updates
  - Admin actions (approvals, rejections, notice creation)
- Include:
  - Timestamp
  - Performed by (User ID / Admin ID)
  - Action description
  - Related entity ID

---

## 🛠 Quality & Coding Guidelines
- **Code Style**
  - Use PascalCase for React components.
  - No inline CSS; use Tailwind classes.
  - Keep logic and UI separate.
  - Avoid deeply nested components; split into smaller, reusable parts.
- **Architecture**
  - Use `services/` folder for DB operations.
  - Keep API handlers in `app/api/` and group by feature.
  - Shared constants and validation schemas in `utils/`.
- **Modularity**
  - Each feature (Bookings, Payments, Notices, Users) should have:
    - Its own API endpoints
    - Its own React components
    - Its own service functions
- **Security**
  - Validate all inputs using Zod.
  - Sanitize user input to prevent XSS.
  - Role-based checks in all API routes.
- **Performance**
  - Use pagination for all tables.
  - Implement database indexes for frequently queried fields.

---

## 🧪 Testing Scenarios & Expected Results

| Test Case | Steps | Expected Result |
|-----------|-------|-----------------|
| **1. User Registration** | Register new user with valid data | Account created, `cylinderBalance = 12`, `allocationExpiry = +1 year` |
| **2. Booking with Razorpay** | Book a cylinder, pay via Razorpay | Payment verified, booking status updated, cylinder balance reduced |
| **3. Booking with COD** | Book using COD method | Booking created with `paymentStatus = PENDING`, admin can approve |
| **4. Booking with Paytm QR** | Book using Paytm QR, enter transaction ID | Booking created, admin verifies payment |
| **5. Cylinder Balance Restriction** | Try to book with balance = 0 | Booking denied with error message |
| **6. Annual Balance Reset** | Simulate expiry date passed | Balance resets to 12 |
| **7. Admin Notice Update** | Create new notice | Notice visible to users instantly |
| **8. Payment Failure Handling** | Fail payment intentionally | Booking status = PENDING, user notified |
| **9. Email Notification** | Make successful booking | Email received with updated balance and booking details |
| **10. Unauthorized Route Access** | Access `/admin/*` without login | Redirected to admin login |

---

## 🖥 Deployment Justification
**Why Vercel?**
- Automatic GitHub integration with CI/CD.
- Global CDN for fast delivery.
- Free tier suitable for small projects.
- Easy environment variable configuration.

**Why Railway PostgreSQL?**
- Free tier available.
- Automatic backups and scaling.
- Works seamlessly with Prisma ORM.

---

## 📅 Timeline (4 Weeks / 8 Phases)

---

### **Week 1**
#### Phase 1 — Project Setup & Foundations
1. **Next.js Project**
   - Use App Router structure.
   - Run `npx create-next-app@latest` with TypeScript enabled.
   - Remove boilerplate files.
2. **Install Core Dependencies**
   - `firebase` — user auth
   - `next-auth` — admin auth
   - `prisma` + `@prisma/client` — ORM
   - `tailwindcss` — styling
   - `razorpay` — payment integration
   - `bcrypt` — password hashing
   - `nodemailer` — email service
   - `zod` — validation
3. **Tailwind Configuration**
   - Add brand colors to `tailwind.config.js`:
     ```js
     colors: {
       brand: '#ff6600',
       dark: '#000000'
     }
     ```
   - Global font selection: `Inter` or `Roboto`.
4. **Database Setup**
   - Create PostgreSQL DB on Railway.
   - Store `DATABASE_URL` in `.env.local`.
   - Define schema in Prisma (see **Database Schema** below).
   - Run `npx prisma migrate dev`.
5. **Environment Variables**
   - Firebase keys (NEXT_PUBLIC_*)
   - NextAuth secret & URLs
   - Razorpay key/secret
   - Gmail SMTP credentials
6. **Folder Structure**
   - Separate folders for `app/user`, `app/admin`, `app/auth`, `app/api`.
   - Shared `components` and `lib` folders.

---

#### Phase 2 — Design System & UI Planning
1. **Theme & Colors**
   - Light theme only.
   - Buttons & highlights: Orange.
   - Sidebars/navbars: Black with orange hover effects.
2. **UI Components to Build First**
   - Primary/secondary buttons.
   - Form inputs with labels.
   - Responsive tables with pagination.
   - Toast notifications for success/error.
   - Modals for confirmations.
3. **Layout Pages**
   - **Public Layout**: Navbar + Footer.
   - **User Dashboard Layout**: Sidebar + Topbar.
   - **Admin Dashboard Layout**: Sidebar + Topbar.
4. **Responsive Behavior**
   - Mobile menu toggle for sidebars.
   - Collapsible menus for smaller screens.

---

### **Week 2**
#### Phase 3 — Authentication System
1. **User Authentication (Firebase)**
   - Enable Email/Password + Google in Firebase console.
   - Build Signup, Login, Logout pages.
   - Persist session with Firebase Auth state.
   - Redirect unauthenticated users from `/user/*` routes.
2. **Admin Authentication (NextAuth.js)**
   - Credentials provider (email/password from `Admin` table).
   - Store hashed passwords (bcrypt).
   - Build `/admin/login` page.
   - Use `getServerSession` to protect `/admin/*` routes.
   - CLI script to create admins.
3. **Access Control**
   - Middleware to check route access based on role.
   - Redirect unauthorized attempts.

---

#### Phase 4 — Navigation & Layouts
1. **User Dashboard Navigation**
   - Links: Book Cylinder, Booking History, Notices.
   - Active link highlight (orange underline or icon change).
   - Collapsible on mobile.
2. **Admin Dashboard Navigation**
   - Links: Overview, Manage Bookings, Users, Notices, Logs.
   - Active link highlight.
   - Collapsible on mobile.
3. **Topbar Elements**
   - Logged-in user name & profile icon.
   - Logout button.
   - Notification icon for new notices (user side).

---

### **Week 3**
#### Phase 5 — User Features
**1. Book Cylinder Page**
   - **Form Fields**:
     - Number of cylinders (min: 1, max: 5).
     - Delivery address (textarea).
     - Preferred delivery date (date picker).
   - **Flow**:
     - Validate with Zod.
     - Create booking → redirect to payment.
   - **UX Details**:
     - Success toast on booking creation.
     - Disable form after payment starts.

**2. Payment Integration**
   - Create Razorpay order in backend.
   - Display Razorpay checkout widget.
   - On success: update booking status & payment status in DB.
   - On failure: mark as failed in DB.

**3. Booking History Page**
   - **Columns**: ID, Cylinders, Address, Date, Booking Status, Payment Status.
   - Color-coded status chips.
   - Filters for date range and status.

**4. Notices Page**
   - List of notices from DB.
   - Sorted by date (newest first).
   - Each notice: title, date, message.

---

#### Phase 6 — Admin Features
**1. Dashboard Overview**
   - Cards: Total Bookings, Pending Bookings, Completed Deliveries, Total Users.
   - Chart: Monthly bookings trend.

**2. Manage Bookings**
   - Table with booking details.
   - Filters: status, payment status, date.
   - Actions: Approve, Reject, Mark Delivered.
   - Sends email updates to users automatically.

**3. Manage Users**
   - List all users.
   - Search by email/name.
   - View detailed profile (bookings history, payment history).

**4. Manage Notices**
   - Form: title + message.
   - Stores notice in DB.
   - Visible instantly on user notices page.

**5. Logs**
   - Shows key admin/user actions.
   - Includes: booking creation, payment verification, status changes.

---

### **Week 4**
#### Phase 7 — Security, Optimization & Logging
1. **Security**
   - All API endpoints require auth.
   - Role-based access checks.
   - Input validation with Zod.
   - Escape all HTML inputs to avoid XSS.
   - Rate-limit booking creation.
2. **Optimization**
   - DB indexes on `createdAt`, `status`.
   - Server-side pagination.
   - Code splitting & lazy loading for heavy tables.
3. **Logging**
   - Store only major actions in `Log` table.
   - Create admin-only Logs view.

---

#### Phase 8 — Deployment & Testing
1. **Deployment**
   - Push code to GitHub.
   - Deploy DB on Railway.
   - Set env variables on Vercel.
   - Deploy app to Vercel.
   - Run Prisma migrations in production.
2. **Testing**
   - Test both user and admin auth flows.
   - Test full booking → payment → approval → delivery cycle.
   - Test emails are sent on updates.
   - Test responsive UI on multiple devices.
   - Verify that unauthorized users cannot access restricted routes.
3. **Documentation**
   - Add README with setup/run/deploy instructions.
   - Add `.env.example` with placeholders.

---

## 📂 Project Structure
```
src/
├── app/
│   ├── auth/             # Login/Signup (Firebase)
│   ├── user/             # User dashboard pages
│   ├── admin/            # Admin dashboard pages
│   ├── api/              # API routes (bookings, payments, notices)
│   └── page.jsx          # Public landing page
├── components/           # Shared UI components
├── lib/                  # Database, email, auth helpers
├── styles/               # Global Tailwind styles
├── utils/                # Validation schemas, constants
prisma/
└── schema.prisma
scripts/
└── createAdmin.js
```

---

## 🗄 Database Schema (Prisma)
*(Keep same schema from earlier roadmap — no code execution here)*

---

## 🔐 Security & Quality Guidelines
- Use `.env.local` for secrets.
- Role-based protection for all pages and API endpoints.
- Use form validation everywhere.
- Avoid inline HTML injection.
- Log essential activities only.

---

## 🧪 Testing Checklist
- User auth (Email + Google).
- Admin auth (Credentials).
- Booking flow with payment.
- Email notifications.
- Admin status updates.
- Responsive UI works on all breakpoints.
- API routes block unauthorized access.
- Live deployment works.

---

## 🚀 Deployment Steps
1. Push repo to GitHub.
2. Setup Railway PostgreSQL DB.
3. Add all env vars in Vercel.
4. Deploy to Vercel.
5. Run `prisma migrate deploy`.
6. Test live version thoroughly.

---
