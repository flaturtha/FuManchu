
# fumanchu PRD

## Draft Instructions

---

### 1. Project Overview

**fumanchu** is a self-hosted viral email capture and referral application. Inspired by platforms like KickoffLabs and Gleam.io, **fumanchu** will integrate directly with the existing Remix-run website using Sanity.io as its CMS, Klaviyo as the ESP, and MedusaJS for e-commerce. Built to run on a user-managed server, **fumanchu** enables email capture, points tracking for referrals, and leaderboard competition. This app will be hosted on a dedicated subdomain, likely `contest.talesofmurder.com`, and will include public and private user dashboards, a leaderboard, and prize structures.

---

### 2. Core Functionalities

1. **User Registration & Profile**
   - **Simple Email-only Registration**: Users register with email only; unique referral link generation upon registration.
   - **User Profile Details**: Option to update profile information (name, book genre preferences, DOB) on the dashboard.

2. **Referral Tracking & Points System**
   - **Point Tallying**:
     - **Initial Points on Signup**: Users receive 5 points upon registration, qualifying for a base-level prize (e.g., an eBook copy).
     - **Sharing Links**: Points are awarded for sharing links on platforms like Facebook, Instagram, and via email.
     - **Click-Through Points**: Users earn points when others click on their shared link.
     - **Referral Signups**: When a new user subscribes via a shared link, the referrer earns points.
     - **Extended Referral Actions**: Points cascade for the referrer when their referrals share links or generate further referrals (e.g., 5 points for initial referral shares, 2 points for actions by recruited users).

3. **User Dashboard**
   - **Referral Link & Points Display**: Users view their unique link, points breakdown, and referral activity.
   - **Leaderboard View**: Shows rank among other users.

4. **Leaderboard (Public)**
   - **Top Competitors Display**: Lists users with the highest points.
   - **Prize Structure Display**: Shows available and awarded prizes, with eligibility based on points.

5. **Admin Dashboard**
   - **User Management**: View users, points, and referral stats.
   - **Prize Configuration**: Define prize thresholds and quantities; mark prizes as delivered.
   - **Automated Prize Delivery**: Set up automation for prize fulfillment based on thresholds:
     - **eBooks**: Integrate with BookFunnel to send links automatically.
     - **Print on Demand**: Use Lulu integration for automatic order and shipment.
   - **Notification Settings**: Adjust email frequency for user updates.

6. **Klaviyo Integration for Email Notifications**
   - **Automated Campaigns**: Sends performance updates, customizable in frequency.
   - **SPAM Compliance**: Allows users to adjust frequency or opt-out of notifications.

---

### 3. Documentation

- **Installation**: Steps to set up the development environment.
- **Supabase Setup**: Connecting to Supabase for user management and points storage.
- **Remix.run Initialization**: Bootstrapping a new Remix app or integrating with an existing setup.
- **Klaviyo Integration**: Setting up Klaviyo API for email campaigns.
- **Automated Prize Delivery Integrations**: Configuring BookFunnel, Lulu, or similar platforms for fulfillment automation.
- **Environment Variables**: Configuration for sensitive data handling (Supabase keys, Klaviyo API keys, etc.).

---

### 4. Current File Structure

(Outline for guidance; refine based on actual file structure upon setup)

```plaintext
fumanchu/
│
├── api/                  # Backend server files
│   ├── controllers/      # Handles core business logic
│   ├── models/           # Supabase schema models
│   ├── routes/           # API routes (user, referral, leaderboard)
│   ├── services/         # External integrations (Klaviyo, BookFunnel, Lulu)
│   └── utils/            # Shared utility functions
│
├── client/               # Frontend (Remix + Zustand + ShadCN)
│   ├── components/       # Reusable ShadCN components
│   ├── pages/            # Pages for dashboard, leaderboard, etc.
│   ├── hooks/            # Zustand hooks for state management
│   ├── styles/           # TailwindCSS or ShadCN styling
│   └── utils/            # Client-side helpers
│
├── config/               # Configuration for environment variables
├── db/                   # Supabase configuration and schema
├── scripts/              # Database migration and seeding
└── tests/                # Unit and integration tests
```

---

### 5. Additional Requirements

#### 1. Project Setup
- Initialize with Remix for frontend, Supabase for backend, Klaviyo for ESP.
- GitHub repo name: `fumanchu`.
- Copy existing landing and thank-you pages from the main web development project to this application.

#### 2. Server-side API Calls
- Supabase for backend API handling, with services for creating users, managing points, and leaderboard ranking.

#### 3. Environmental Variables
- Use `.env` for storing sensitive keys (Supabase keys, Klaviyo API keys, BookFunnel, and Lulu API keys).

#### 4. Error Handling & Logging
- Centralized error handling in Express, with Supabase error responses managed with logging for tracking.

#### 5. Type Safety
- TypeScript for frontend and backend; define types for user, referral, points, and leaderboard.

#### 6. API Client Initialization
- Initialize Supabase client in `/client/hooks/` for frontend data fetching and updates.

#### 7. Data Fetching
- Use Zustand for state management and Supabase’s real-time capabilities for dynamic leaderboard updates.

#### 8. Remix Config
- Configure routes for user, leaderboard, and admin dashboards in Remix.

#### 9. CORS and API Routes
- Enable CORS with restrictions to protect API; routes in Express with Supabase integration.

#### 10. Component Structure
- ShadCN for UI components; modularized for reuse in user/admin dashboards.

#### 11. Security
- Rate limiting for API endpoints.
- Password hashing and JWT for authenticated admin actions.

---

### Additional Functionalities

1. **Points Decay for Inactivity**: Points decay or flag users inactive after a period without engagement, encouraging active participation. Notifications will prompt users back to activity.

2. **Fraud Detection Mechanism**: Basic anti-abuse monitoring (IP tracking, rate limits on referrals) to prevent misuse and enhance system integrity.

3. **User Activity Logging**: Logging of user actions (shares, referrals, prize claims) for transparency and to aid in troubleshooting.

4. **Admin Notifications**: Periodic notifications or performance summaries for admin users, including new sign-ups, top users, or upcoming prize thresholds.

5. **User Feedback and Support**: Include a feedback form or support options to collect user insights, along with an FAQ for common points and prize questions.

6. **Comprehensive Analytics Dashboard**: Detailed analytics for admin users, covering user demographics, points trends, and referral patterns.

7. **Automated Content Scheduling**: Pre-schedule email updates for user milestones (e.g., reaching specific points thresholds) to maintain engagement.

---

