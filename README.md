# 🚀 Gas Agency System — Development Roadmap (4 Weeks / 8 Phases)

## 📋 Overview
The Gas Agency System is a **full-stack Next.js application** with:
- **User Dashboard** — booking gas cylinders, making payments, viewing booking history, receiving email updates.
- **Admin Dashboard** — managing bookings, users, payments, deliveries, notices, and logs.
- **Database** — PostgreSQL (Railway) + Prisma ORM.
- **Auth** — Firebase Authentication (Email+Password, Google SSO) for users, NextAuth.js (Credentials) for admins.
- **Payments** — Razorpay integration.
- **Emails** — Gmail SMTP transactional emails.
- **UI/UX** — Tailwind CSS (Orange + Black light theme), fully responsive, clean and minimal.

---

## 📅 Timeline (4 Weeks / 8 Phases)

### **Week 1**
#### Phase 1 — Project Setup & Foundations
1. **Next.js Setup**
   - Create project with `npx create-next-app@latest`.
   - Enable App Router structure.
   - Install required dependencies: `firebase`, `next-auth`, `prisma`, `@prisma/client`, `tailwindcss`, `razorpay`, `bcrypt`, `nodemailer`, `zod`.
2. **Tailwind CSS Configuration**
   - Set primary brand colors: **Orange (#ff6600)** and **Black (#000000)**.
   - Configure font and spacing in `tailwind.config.js`.
3. **Database Setup**
   - Create PostgreSQL DB on Railway.
   - Store `DATABASE_URL` in `.env.local`.
   - Define tables in Prisma schema (see **Database Schema** below).
4. **Folder Structure**
   - Organize folders for `user`, `admin`, `auth`, `api`, `components`, `lib`, `utils`.
5. **Environment Variables**
   - Prepare `.env.local` and add all keys for Firebase, NextAuth, Razorpay, Gmail SMTP.

#### Phase 2 — Design System & UI Planning
1. **UI Theme**
   - Light theme only — no dark mode.
   - Orange highlights for buttons, active links, and call-to-action areas.
   - Black as background for navbars and sidebars.
2. **Core UI Components**
   - Buttons (primary, secondary, disabled states).
   - Input fields with floating labels.
   - Table with sorting, search, and pagination.
   - Modal for confirmation dialogs.
   - Toast notification component.
3. **Layouts**
   - Public Layout (Navbar, Footer).
   - User Dashboard Layout (Sidebar + Content Area).
   - Admin Dashboard Layout (Sidebar + Content Area).

---

### **Week 2**
#### Phase 3 — Authentication System Implementation
1. **User Authentication (Firebase)**
   - Enable Email+Password and Google sign-in methods in Firebase console.
   - Implement signup, login, and logout.
   - Use Firebase hooks for client auth state.
   - Persist auth state across refresh.
2. **Admin Authentication (NextAuth.js)**
   - Credentials provider using Prisma `Admin` table.
   - Password hashing with bcrypt.
   - CLI script to create admin accounts from the terminal.
   - Secure admin routes with `getServerSession`.
3. **Access Control**
   - Protect `/user/*` routes for authenticated users only.
   - Protect `/admin/*` routes for authenticated admins only.
   - Redirect unauthorized users to login page.

#### Phase 4 — Navigation & Responsive Layouts
1. **User Navigation**
   - Sidebar: Book Cylinder, Booking History, Notices.
   - Active link highlighting.
   - Collapsible on mobile.
2. **Admin Navigation**
   - Sidebar: Dashboard, Manage Bookings, Users, Notices, Logs.
   - Collapsible on mobile.
3. **Responsive Design**
   - Use Tailwind responsive utilities (`sm:`, `md:`, `lg:`).
   - Test on mobile, tablet, and desktop.

---

### **Week 3**
#### Phase 5 — User Features Implementation
**1. Booking Cylinder Page**
   - Form fields: number of cylinders, delivery address, preferred delivery date.
   - Client-side validation with Zod.
   - API call to create booking in DB.
   - Show booking confirmation modal.

**2. Payment Integration**
   - API to create Razorpay order.
   - Client Razorpay checkout (pre-fill user details).
   - API to verify payment and update booking/payment status.

**3. Booking History Page**
   - Table with columns: Booking ID, Cylinders, Address, Date, Status, Payment Status.
   - Status badges with colors (Pending = Yellow, Approved = Green, Rejected = Red).
   - Pagination for large datasets.

**4. Notices Page**
   - List notices from DB.
   - Show title, date, and message.

#### Phase 6 — Admin Features Implementation
**1. Dashboard Overview**
   - Cards showing total users, total bookings, pending bookings.
   - Chart for monthly bookings.

**2. Manage Bookings**
   - Table with all bookings.
   - Filters: status, date.
   - Action buttons: Approve, Reject, Mark Delivered.
   - Email notifications to users on status change.

**3. Manage Users**
   - List all registered users.
   - Search by email or name.
   - View user details (bookings, payments).

**4. Manage Notices**
   - Form to create a new notice (title, message).
   - Store notice in DB.
   - Display notices to users.

**5. Logs Page**
   - Table of major system activities (booking creation, payment verification, admin actions).

---

### **Week 4**
#### Phase 7 — Security, Optimization & Logging
1. **Security**
   - All API routes require authentication.
   - Role checks for admin vs user endpoints.
   - Input validation for all forms.
   - Prevent XSS by sanitizing inputs.
2. **Optimization**
   - Add indexes to DB fields often used in queries (e.g., `createdAt`, `status`).
   - Implement server-side pagination for tables.
   - Lazy-load large components.
3. **Logging**
   - Store only important events in the `Log` table.
   - Create admin-only logs page.

#### Phase 8 — Deployment & Testing
1. **Deployment**
   - Connect GitHub repo to Vercel.
   - Set all environment variables in Vercel settings.
   - Run Prisma migrations on Railway DB.
2. **Testing**
   - User registration/login flow.
   - Admin login flow.
   - Booking → Payment → Status update.
   - Email notifications.
   - Responsive UI on multiple devices.
   - Unauthorized route protection.
3. **Documentation**
   - Write README with setup, run, and deploy instructions.
   - Add `.env.example` file.

---

## 📂 Project Structure
```
src/
├── app/
│   ├── auth/             # User auth pages (Firebase)
│   ├── user/             # User dashboard
│   ├── admin/            # Admin dashboard
│   ├── api/              # API routes
│   └── page.jsx          # Landing page
├── components/           # Shared UI components
├── lib/                  # DB, email, auth helpers
├── styles/               # Tailwind global styles
├── utils/                # Validation schemas, constants
prisma/
└── schema.prisma
scripts/
└── createAdmin.js
```

---

## 🗄 Database Schema (Prisma)
```prisma
generator client {
  provider = "prisma-client-js"
}
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id            String      @id @default(uuid())
  name          String
  email         String      @unique
  firebaseUid   String?     @unique
  phone         String?
  address       String?
  bookings      Booking[]
  createdAt     DateTime    @default(now())
  updatedAt     DateTime    @updatedAt
}

model Admin {
  id            String      @id @default(uuid())
  name          String
  email         String      @unique
  passwordHash  String
  createdAt     DateTime    @default(now())
  updatedAt     DateTime    @updatedAt
}

model Booking {
  id            String        @id @default(uuid())
  user          User          @relation(fields: [userId], references: [id])
  userId        String
  cylinders     Int
  address       String
  deliveryDate  DateTime
  status        BookingStatus @default(PENDING)
  paymentId     String?
  paymentStatus PaymentStatus @default(PENDING)
  createdAt     DateTime      @default(now())
  updatedAt     DateTime      @updatedAt
}

model Notice {
  id            String      @id @default(uuid())
  title         String
  message       String
  createdBy     Admin       @relation(fields: [adminId], references: [id])
  adminId       String
  createdAt     DateTime    @default(now())
}

model Log {
  id            String      @id @default(uuid())
  action        String
  performedBy   String
  meta          Json?
  createdAt     DateTime    @default(now())
}

enum BookingStatus {
  PENDING
  APPROVED
  REJECTED
  DELIVERED
}

enum PaymentStatus {
  PENDING
  SUCCESS
  FAILED
}
```

---

## 🔐 Security & Quality Guidelines
- **Authentication**: Always verify Firebase ID token or NextAuth session on server.
- **Validation**: Use Zod for all request bodies.
- **Role Checks**: Restrict `/admin/*` to admins only.
- **Sanitization**: Escape HTML to prevent XSS.
- **Database Safety**: Use Prisma ORM; avoid raw SQL queries.
- **Logging**: Store essential events only.

---

## 🧪 Testing Checklist
- [ ] User signup/login (Email + Google)
- [ ] Admin login (Credentials)
- [ ] Booking creation & payment flow
- [ ] Email notifications sent
- [ ] Admin booking approval/rejection
- [ ] Responsive layout works on mobile
- [ ] Unauthorized users blocked from restricted pages
- [ ] Deployment works on Vercel

---

## 🚀 Deployment Steps
1. Push project to GitHub.
2. Create PostgreSQL DB on Railway.
3. Add environment variables to Vercel.
4. Deploy project to Vercel.
5. Run `prisma migrate deploy` on Railway DB.
6. Test all flows on live deployment.

---
