# fumanchu PRD

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
   - **Points Decay for Inactivity**: Points decay or flag users inactive after a period without engagement, encouraging active participation. Notifications will prompt users back to activity.

3. **Data Validation with Zod**
   - **User Input Validation**:
     - Validate email format on registration.
     - Ensure correct types and values for fields like firstName, lastName, DOB, and genrePreferences.
   - **API Requests & Responses**:
     - Confirm data types for responses from Supabase (e.g., user and points data).
     - Standardize response types for functions between backend functions and external APIs.
   - **Error Handling**:
     - Use Zod to handle and return type-safe error messages, providing users with meaningful feedback.

4. **User Dashboard**
   - **Referral Link & Points Display**: Users view their unique link, points breakdown, and referral activity.
   - **Leaderboard View**: Shows rank among other users.
   - **User Activity Logging**: Logging of user actions (shares, referrals, prize claims) for transparency and to aid in troubleshooting.
   - **User Feedback and Support**: Include a feedback form or support options to collect user insights, along with an FAQ for common points and prize questions.

5. **Leaderboard (Public)**
   - **Top Competitors Display**: Lists users with the highest points.
   - **Prize Structure Display**: Shows available and awarded prizes, with eligibility based on points.
   - **Fraud Detection Mechanism**: Basic anti-abuse monitoring (IP tracking, rate limits on referrals) to prevent misuse and enhance system integrity.

6. **Admin Dashboard**
   - **User Management**: View users, points, and referral stats.
   - **Prize Configuration**: Define prize thresholds and quantities; mark prizes as delivered.
   - **Automated Prize Delivery**: Set up automation for prize fulfillment based on thresholds:
     - **eBooks**: Integrate with BookFunnel to send links automatically.
     - **Print on Demand**: Use Lulu integration for automatic order and shipment.
   - **Notification Settings**: Adjust email frequency for user updates.
   - **Admin Notifications**: Periodic notifications or performance summaries for admin users, including new sign-ups, top users, or upcoming prize thresholds.
   - **Comprehensive Analytics Dashboard**: Detailed analytics covering user demographics, points trends, and referral patterns.

7. **Klaviyo Integration for Email Notifications**
   - **Automated Campaigns**: Sends performance updates, customizable in frequency.
   - **SPAM Compliance**: Allows users to adjust frequency or opt-out of notifications.
   - **User Consent for Communications**: Ensure clear user consent mechanisms to comply with anti-spam laws and regulations.

---

### 3. Documentation

- **Installation**: Steps to set up the development environment.
- **Supabase Setup**: Connecting to Supabase for user management and points storage.
- **Remix.run Initialization**: Bootstrapping a new Remix app or integrating with an existing setup.
- **Klaviyo Integration**: Setting up Klaviyo API for email campaigns.
- **Automated Prize Delivery Integrations**: Configuring BookFunnel, Lulu, or similar platforms for fulfillment automation.
- **Environment Variables**: Configuration for sensitive data handling (Supabase keys, Klaviyo API keys, etc.).

---

### 4. Technical Stack Setup

#### 1. **Remix with TypeScript**

To start with Remix in TypeScript, you’ll want to use the `create-remix` CLI.

1. **Create the Project**:

   ```bash
   npx create-remix@latest fumanchu
   ```

   This command will prompt you to:

   - Choose a language (select **TypeScript**).
   - Select a deployment target (default **Remix App Server** is fine for development).

2. **Install Dependencies**:

   Inside your project directory, run:

   ```bash
   npm install
   ```

3. **Structure**: The command creates a structured Remix project with directories like `app/` for your components, routes, and other core functionality.

4. **Documentation & Resources**:

   - [Remix Quick Start](https://remix.run/docs/en/v1/getting-started/quick-start)
   - [Remix CLI Reference](https://remix.run/docs/en/v1/api/create-remix)

---

#### 2. **Supabase for Backend**

Supabase will handle your database and real-time features for tracking users, points, and referrals.

1. **Set Up Supabase Project**:

   - Go to [Supabase](https://supabase.com/) and create a new project. This will generate API keys for your database connection.

2. **Install Supabase Client**:

   ```bash
   npm install @supabase/supabase-js
   ```

3. **Initialize Supabase in Your App**:

   - In your `app` folder, create a Supabase client:

     ```typescript
     // app/utils/supabaseClient.ts
     import { createClient } from '@supabase/supabase-js';

     const supabaseUrl = process.env.SUPABASE_URL!;
     const supabaseAnonKey = process.env.SUPABASE_ANON_KEY!;

     export const supabase = createClient(supabaseUrl, supabaseAnonKey);
     ```

   - Add `.env` file with your Supabase URL and keys:

     ```plaintext
     SUPABASE_URL=<your_supabase_url>
     SUPABASE_ANON_KEY=<your_supabase_anon_key>
     ```

4. **Documentation & Resources**:

   - [Supabase Quickstart Guide](https://supabase.com/docs/guides/with-remix)

---

#### 3. **Zustand for State Management**

Zustand will help manage application state, including user points and session data.

1. **Install Zustand**:

   ```bash
   npm install zustand
   ```

2. **Create a Zustand Store**:

   - Create a simple store for managing points, referral data, etc.

     ```typescript
     // app/store/useStore.ts
     import create from 'zustand';

     interface StoreState {
       user: any;
       points: number;
       setUser: (user: any) => void;
       incrementPoints: (value: number) => void;
     }

     export const useStore = create<StoreState>((set) => ({
       user: null,
       points: 0,
       setUser: (user) => set({ user }),
       incrementPoints: (value) =>
         set((state) => ({ points: state.points + value })),
     }));
     ```

3. **Documentation & Resources**:

   - [Zustand Documentation](https://zustand-demo.pmnd.rs/)

---

#### 4. **TailwindCSS for Styling**

TailwindCSS is a utility-first CSS framework, ideal for keeping your UI components concise and consistent.

1. **Install TailwindCSS**:

   ```bash
   npm install -D tailwindcss postcss autoprefixer
   npx tailwindcss init -p
   ```

2. **Configure TailwindCSS**:

   - Add the paths to your Remix app files in `tailwind.config.js`:

     ```javascript
     module.exports = {
       content: ['./app/**/*.{js,ts,jsx,tsx}'],
       theme: {
         extend: {},
       },
       plugins: [],
     };
     ```

3. **Import Tailwind in Your CSS**:

   - Update your main CSS file (e.g., `app/styles/global.css`):

     ```css
     @tailwind base;
     @tailwind components;
     @tailwind utilities;
     ```

4. **Documentation & Resources**:

   - [TailwindCSS Installation Guide](https://tailwindcss.com/docs/installation)

---

#### 5. **ShadCN for Components**

ShadCN is a collection of customizable components, making UI development faster and more consistent.

1. **Install ShadCN**:

   - Follow ShadCN’s component installation as needed:

     ```bash
     npm install @shadcn/ui
     ```

2. **Usage in Remix**:

   - Import ShadCN components as you build your Remix routes and pages. Customize components with Tailwind for a consistent theme.

3. **Documentation & Resources**:

   - [ShadCN UI Docs](https://ui.shadcn.com/)

---

#### 6. **Zod for Validation**

Zod is TypeScript-first schema validation with static type inference.

1. **Install Zod**:

   ```bash
   npm install zod
   ```

2. **Enable Strict Mode in TypeScript**:

   - In your `tsconfig.json`:

     ```json
     {
       "compilerOptions": {
         "strict": true,
         // ...other options
       }
     }
     ```

3. **Usage**:

   - **Define Schemas**:

     ```typescript
     // app/validators/userValidator.ts
     import { z } from 'zod';

     export const registrationSchema = z.object({
       email: z.string().email(),
       firstName: z.string().optional(),
       lastName: z.string().optional(),
       DOB: z.string().optional(),
       genrePreferences: z.array(z.string()).optional(),
     });
     ```

   - **Validate Data**:

     ```typescript
     // app/routes/api/register.tsx
     import { registrationSchema } from '~/validators/userValidator';

     export const action = async ({ request }) => {
       const data = Object.fromEntries(await request.formData());
       const parsedData = registrationSchema.safeParse(data);

       if (!parsedData.success) {
         return { errors: parsedData.error.flatten() };
       }

       // Proceed with registration
     };
     ```

4. **Documentation & Resources**:

   - [Zod Documentation](https://zod.dev/)

---

### 5. Deployment Strategy and CI/CD Pipeline

#### **Deployment with Vercel**

**Vercel** will be used for deploying the application, providing ease of use, scalability, and seamless integration with GitHub.

1. **Set Up Vercel Account**:

   - Sign up at [Vercel](https://vercel.com/) and link your GitHub account.

2. **Import Project**:

   - In the Vercel dashboard, click on **"New Project"** and select the `fumanchu` repository.

3. **Configure Project Settings**:

   - **Framework Preset**: Ensure Vercel detects Remix.
   - **Environment Variables**: Add all necessary environment variables (e.g., `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `KLAVIYO_API_KEY`).

4. **Custom Domain Setup**:

   - Add `contest.talesofmurder.com` as a custom domain in Vercel.
   - Update DNS records to point to Vercel.

5. **Automatic Deployments**:

   - Vercel will automatically deploy the `main` branch upon new commits.

#### **Continuous Integration/Continuous Deployment (CI/CD) Pipeline**

Implement a CI/CD pipeline using GitHub Actions to automate testing and deployment.

1. **GitHub Repository Setup**:

   - Create a repository named `fumanchu`.
   - Use branches for development (`dev`) and production (`main`).

2. **GitHub Actions Workflow**:

   - Add a workflow file `.github/workflows/ci.yml`:

     ```yaml
     name: CI Pipeline

     on:
       push:
         branches: [main]

     jobs:
       build-and-test:
         runs-on: ubuntu-latest
         steps:
           - uses: actions/checkout@v2
           - uses: actions/setup-node@v2
             with:
               node-version: '16'
           - run: npm install
           - run: npm run build
           - run: npm run test
     ```

3. **Vercel Deployment via GitHub Actions** (Optional):

   - Use the Vercel Action in your workflow:

     ```yaml
     - name: Deploy to Vercel
       uses: amondnet/vercel-action@v20
       with:
         vercel-token: ${{ secrets.VERCEL_TOKEN }}
         vercel-args: '--prod'
         working-directory: ./
         git-commit-sha: ${{ github.sha }}
     ```

   - Add `VERCEL_TOKEN` to your GitHub repository secrets.

4. **Testing and Quality Assurance**:

   - **Automated Testing**: Write unit and integration tests using frameworks like Jest.
   - **Code Quality Checks**: Implement ESLint and Prettier for consistent code style.

5. **Branching Strategy**:

   - Develop features in separate branches.
   - Merge into `dev` for testing before deploying to `main`.

6. **Monitoring and Rollbacks**:

   - Monitor deployments via Vercel dashboard.
   - Roll back to previous deployments if issues are detected.

#### **Environment Variables Management**

- Use Vercel's environment variable settings for production.
- Use a `.env` file for local development, ensuring it's included in `.gitignore`.

---

### 6. Additional Requirements

#### 1. Project Setup

- Initialize with Remix for frontend, Supabase for backend, Klaviyo for ESP.
- GitHub repo name: `fumanchu`.
- Copy existing landing and thank-you pages from the main web development project to this application.

#### 2. Server-side API Calls

- Supabase for backend API handling, with services for creating users, managing points, and leaderboard ranking.

#### 3. Environment Variables

- Use `.env` for storing sensitive keys (Supabase keys, Klaviyo API keys, BookFunnel, and Lulu API keys).
- Ensure sensitive data is kept out of the codebase and version control.

#### 4. Error Handling & Logging

- Centralized error handling in Remix actions and loaders.
- Implement error monitoring tools (e.g., Sentry) to proactively track and resolve issues.

#### 5. Type Safety

- Use TypeScript for frontend and backend; define types for user, referral, points, and leaderboard.
- Enforce strict typing and avoid the use of `any` where possible.

#### 6. API Client Initialization

- Initialize Supabase client in `/app/utils/` for frontend data fetching and updates.

#### 7. Data Fetching

- Use Remix loaders and actions for data fetching and mutations.
- Utilize Supabase's real-time capabilities for dynamic leaderboard updates.

#### 8. Remix Config

- Configure routes for user, leaderboard, and admin dashboards in Remix.

#### 9. CORS and API Routes

- Configure CORS policies in Supabase to allow requests only from your domain.
- Ensure API routes are protected and properly authenticated where necessary.

#### 10. Component Structure

- Use ShadCN for UI components; modularize for reuse in user/admin dashboards.
- Maintain a consistent design system across the application.

#### 11. Security

- Implement rate limiting for API endpoints.
- Use secure session management for user authentication.
- Protect admin routes with proper authentication and authorization.
- Regularly update dependencies to patch security vulnerabilities.

#### 12. Backup and Disaster Recovery

- Implement data backup strategies with Supabase's backup features.
- Plan for data recovery to prevent data loss and ensure business continuity.

---

### 7. Additional Functionalities

1. **Fraud Detection Details**:

   - Implement IP tracking to monitor suspicious activities.
   - Set rate limits on referrals and signups from the same IP.
   - Consider adding CAPTCHA on registration to prevent bot signups.

2. **Accessibility Considerations**:

   - Ensure the application meets WCAG 2.1 AA standards.
   - Use semantic HTML and ARIA roles where appropriate.
   - Test the application with screen readers.

3. **User Consent for Communications**:

   - Include checkboxes for users to consent to receiving emails.
   - Provide clear privacy policy and terms of service.

4. **Error Monitoring and Logging**:

   - Integrate error monitoring tools like Sentry.
   - Log errors with sufficient detail to aid in debugging.

5. **Scalability and Performance**:

   - Optimize database queries to reduce load times.
   - Use caching strategies where appropriate.
   - Monitor application performance using Vercel's analytics tools.

6. **User Authentication and Security**:

   - Use secure cookies for session management.
   - Implement CSRF protection in forms.
   - Ensure all user input is sanitized to prevent XSS attacks.

7. **Testing and Quality Assurance**:

   - Write comprehensive unit tests for critical components.
   - Implement integration tests for key user flows.
   - Use end-to-end testing tools like Cypress for full application testing.

8. **Backup and Disaster Recovery**:

   - Schedule regular backups of the Supabase database.
   - Document recovery procedures.

9. **User Feedback and Support**:

   - Provide a contact form for users to submit feedback or issues.
   - Set up an automated email response acknowledging receipt of feedback.

---

### 8. Current File Structure

(Outline for guidance; refine based on actual file structure upon setup)

```plaintext
fumanchu/
│
├── app/                      # Remix application root
│   ├── components/           # Reusable UI components (ShadCN)
│   ├── routes/               # Remix routes for pages and API endpoints
│   ├── styles/               # TailwindCSS styles
│   ├── utils/                # Utility functions and helpers
│   ├── validators/           # Zod schemas for data validation
│   ├── store/                # Zustand state management
│   └── entry.client.tsx      # Client entry point
│
├── public/                   # Static assets
│
├── .github/
│   └── workflows/            # GitHub Actions workflows
│
├── .env                      # Environment variables (not committed)
│
├── remix.config.js           # Remix configuration
├── tailwind.config.js        # TailwindCSS configuration
├── tsconfig.json             # TypeScript configuration
├── package.json              # NPM scripts and dependencies
└── README.md                 # Project documentation
```

---

### 9. Final Notes

- **Deployment Flexibility**: While Vercel is used initially, the architecture allows for migration to other platforms if needed.
- **Continuous Improvement**: The application will be iteratively improved based on personal use and feedback.
- **Documentation**: Keep documentation updated with any changes to the setup or architecture.

---

### 10. Step-by-step Instructions

Sure! Below is a step-by-step breakdown of the project tasks, organized by section/activity with main tasks and subtasks. You can use this to guide the Cursor AI IDE's Composer AI as you work through each part of the project.

---

# **Step-by-Step Instructions for Building "fumanchu"**

---

## **1. Project Initialization and Setup**

### **1.1. Initialize GitHub Repository**

- **Task**: Create a new GitHub repository named `fumanchu`.
  - **Subtasks**:
    - Go to GitHub and create a new repository called `fumanchu`.
    - Clone the repository to your local machine.

### **1.2. Initialize Remix App with TypeScript**

- **Task**: Set up a new Remix app using TypeScript.
  - **Subtasks**:
    - Open a terminal in your project directory.
    - Run `npx create-remix@latest fumanchu`.
      - Select **TypeScript** when prompted.
      - Choose the default **Remix App Server** for development.
    - Navigate into the project directory with `cd fumanchu`.
    - Install dependencies with `npm install`.

### **1.3. Set Up Version Control**

- **Task**: Commit the initial project files to GitHub.
  - **Subtasks**:
    - Run `git add .` to stage all files.
    - Run `git commit -m "Initial commit with Remix app setup"`.
    - Push to GitHub with `git push origin main`.

---

## **2. Install and Configure Tailwind CSS**

### **2.1. Install Tailwind CSS**

- **Task**: Add Tailwind CSS to the project.
  - **Subtasks**:
    - Run `npm install -D tailwindcss postcss autoprefixer`.
    - Initialize Tailwind CSS with `npx tailwindcss init -p`.

### **2.2. Configure Tailwind CSS**

- **Task**: Set up Tailwind to work with Remix.
  - **Subtasks**:
    - Update `tailwind.config.js`:
      ```javascript
      module.exports = {
        content: ['./app/**/*.{js,jsx,ts,tsx}'],
        theme: {
          extend: {},
        },
        plugins: [],
      };
      ```
    - Create a global CSS file, e.g., `app/styles/global.css`, and add:
      ```css
      @tailwind base;
      @tailwind components;
      @tailwind utilities;
      ```
    - Import the global CSS file in `app/root.tsx`:
      ```typescript
      import styles from './styles/global.css';

      export const links: LinksFunction = () => {
        return [{ rel: 'stylesheet', href: styles }];
      };
      ```

---

## **3. Set Up Supabase Backend**

### **3.1. Create Supabase Project**

- **Task**: Set up a new Supabase project for backend services.
  - **Subtasks**:
    - Go to [Supabase](https://supabase.com/) and sign up or log in.
    - Create a new project and note the `API URL` and `API Key`.

### **3.2. Install Supabase Client**

- **Task**: Add Supabase client library to the project.
  - **Subtasks**:
    - Run `npm install @supabase/supabase-js`.

### **3.3. Initialize Supabase Client**

- **Task**: Set up the Supabase client in your app.
  - **Subtasks**:
    - Create a new file `app/utils/supabaseClient.ts`:
      ```typescript
      import { createClient } from '@supabase/supabase-js';

      const supabaseUrl = process.env.SUPABASE_URL!;
      const supabaseAnonKey = process.env.SUPABASE_ANON_KEY!;

      export const supabase = createClient(supabaseUrl, supabaseAnonKey);
      ```

### **3.4. Configure Environment Variables**

- **Task**: Securely store and access environment variables.
  - **Subtasks**:
    - Create a `.env` file in the project root:
      ```plaintext
      SUPABASE_URL=your_supabase_url
      SUPABASE_ANON_KEY=your_supabase_anon_key
      ```
    - Add `.env` to `.gitignore` to prevent it from being committed.

---

## **4. Set Up State Management with Zustand**

### **4.1. Install Zustand**

- **Task**: Add Zustand for state management.
  - **Subtasks**:
    - Run `npm install zustand`.

### **4.2. Create Zustand Store**

- **Task**: Initialize a Zustand store for application state.
  - **Subtasks**:
    - Create `app/store/useStore.ts`:
      ```typescript
      import create from 'zustand';

      interface User {
        id: string;
        email: string;
        // Add other user properties as needed
      }

      interface StoreState {
        user: User | null;
        points: number;
        setUser: (user: User) => void;
        incrementPoints: (value: number) => void;
      }

      export const useStore = create<StoreState>((set) => ({
        user: null,
        points: 0,
        setUser: (user) => set({ user }),
        incrementPoints: (value) =>
          set((state) => ({ points: state.points + value })),
      }));
      ```

---

## **5. Set Up Validation with Zod**

### **5.1. Install Zod**

- **Task**: Add Zod for schema validation.
  - **Subtasks**:
    - Run `npm install zod`.

### **5.2. Enable Strict Mode in TypeScript**

- **Task**: Ensure TypeScript strict mode is enabled.
  - **Subtasks**:
    - In `tsconfig.json`, verify or add:
      ```json
      {
        "compilerOptions": {
          "strict": true,
          // ...other options
        }
      }
      ```

### **5.3. Define Validation Schemas**

- **Task**: Create Zod schemas for data validation.
  - **Subtasks**:
    - Create `app/validators/userValidator.ts`:
      ```typescript
      import { z } from 'zod';

      export const registrationSchema = z.object({
        email: z.string().email(),
        firstName: z.string().optional(),
        lastName: z.string().optional(),
        DOB: z.string().optional(),
        genrePreferences: z.array(z.string()).optional(),
      });
      ```

### **5.4. Apply Validation in Routes**

- **Task**: Use Zod schemas in Remix actions and loaders.
  - **Subtasks**:
    - In your registration route `app/routes/register.tsx`:
      ```typescript
      import { ActionFunction, json } from '@remix-run/node';
      import { registrationSchema } from '~/validators/userValidator';
      import { supabase } from '~/utils/supabaseClient';

      export const action: ActionFunction = async ({ request }) => {
        const formData = await request.formData();
        const data = Object.fromEntries(formData);

        const result = registrationSchema.safeParse(data);

        if (!result.success) {
          return json({ errors: result.error.flatten() }, { status: 400 });
        }

        // Proceed with registration logic
      };
      ```

---

## **6. Set Up UI Components with ShadCN**

### **6.1. Install ShadCN UI**

- **Task**: Add ShadCN UI component library.
  - **Subtasks**:
    - Run `npm install @shadcn/ui`.

### **6.2. Import and Customize Components**

- **Task**: Use ShadCN components in your app.
  - **Subtasks**:
    - In your components, import ShadCN components:
      ```typescript
      import { Button } from '@shadcn/ui';
      ```
    - Customize components using Tailwind CSS classes.

---

## **7. Implement Core Functionalities**

### **7.1. User Registration and Profile**

- **Task**: Build user registration and profile update features.
  - **Subtasks**:
    - **Registration Form**:
      - Create a registration page `app/routes/register.tsx`.
      - Use a form to collect the user's email.
      - Validate input with Zod.
      - On successful registration, generate a unique referral link.
    - **Profile Page**:
      - Create `app/routes/dashboard/profile.tsx`.
      - Allow users to update their name, DOB, and genre preferences.
      - Validate updates with Zod.

### **7.2. Referral Tracking and Points System**

- **Task**: Implement the referral system and points logic.
  - **Subtasks**:
    - **Database Schema**:
      - Define tables in Supabase for users, referrals, and points.
    - **Point Allocation Logic**:
      - Implement logic for assigning points based on actions:
        - Signup (5 points).
        - Sharing links.
        - Click-throughs.
        - Referral signups.
        - Extended referral actions.
    - **Points Decay Mechanism**:
      - Set up a scheduled function to reduce points after inactivity.
    - **API Endpoints**:
      - Create Remix actions/loaders for updating points.

### **7.3. User Dashboard**

- **Task**: Develop the user dashboard.
  - **Subtasks**:
    - Display the user's unique referral link.
    - Show points breakdown and referral activity.
    - Include a leaderboard view showing the user's rank.
    - Implement user activity logging.

### **7.4. Leaderboard**

- **Task**: Create a public leaderboard page.
  - **Subtasks**:
    - Fetch and display top users based on points.
    - Show prize structures and eligibility criteria.
    - Ensure real-time updates using Supabase's real-time features.

### **7.5. Admin Dashboard**

- **Task**: Build the admin interface.
  - **Subtasks**:
    - Implement user management features.
    - Allow admins to define prize thresholds and quantities.
    - Enable marking prizes as delivered.
    - Set up automated prize delivery settings.
    - Provide analytics and performance summaries.

---

## **8. Integrate Klaviyo for Email Notifications**

### **8.1. Set Up Klaviyo Account**

- **Task**: Prepare Klaviyo for email campaigns.
  - **Subtasks**:
    - Sign up or log in to [Klaviyo](https://www.klaviyo.com/).
    - Obtain your Klaviyo API keys.

### **8.2. Install Klaviyo SDK (if available)**

- **Task**: Add Klaviyo SDK or set up API access.
  - **Subtasks**:
    - Install SDK if available: `npm install klaviyo-sdk` (replace with actual package name).
    - If no SDK, plan to use HTTP requests to Klaviyo's API.

### **8.3. Implement Email Campaigns**

- **Task**: Set up automated emails for user engagement.
  - **Subtasks**:
    - Integrate with Klaviyo API to send emails upon certain triggers (e.g., registration, reaching a points threshold).
    - Ensure users have opted in for communications.
    - Provide options for users to adjust email frequency or opt-out.

---

## **9. Implement Security Measures**

### **9.1. User Authentication and Session Management**

- **Task**: Secure user sessions.
  - **Subtasks**:
    - Use Remix's built-in session management.
    - Store session data securely using HTTP-only cookies.
    - Implement logout functionality.

### **9.2. Input Sanitization and CSRF Protection**

- **Task**: Protect against common web vulnerabilities.
  - **Subtasks**:
    - Sanitize all user inputs to prevent XSS attacks.
    - Use CSRF tokens in forms.

### **9.3. Rate Limiting and Fraud Detection**

- **Task**: Prevent abuse of the referral system.
  - **Subtasks**:
    - Implement rate limiting on API endpoints.
    - Track IP addresses to detect suspicious activities.
    - Consider adding CAPTCHA to registration and key forms.

---

## **10. Set Up Error Handling and Monitoring**

### **10.1. Centralized Error Handling**

- **Task**: Manage errors gracefully.
  - **Subtasks**:
    - Use Remix's error boundary components.
    - Return meaningful error messages to users.
    - Log errors for troubleshooting.

### **10.2. Integrate Error Monitoring Tools**

- **Task**: Proactively track and resolve issues.
  - **Subtasks**:
    - Sign up for an error monitoring service like [Sentry](https://sentry.io/).
    - Install Sentry: `npm install @sentry/react @sentry/tracing`.
    - Configure Sentry in your app's entry points.

---

## **11. Testing and Quality Assurance**

### **11.1. Write Unit and Integration Tests**

- **Task**: Ensure application reliability.
  - **Subtasks**:
    - Install Jest: `npm install --save-dev jest @types/jest ts-jest`.
    - Configure Jest for TypeScript.
    - Write tests for critical components and functions.

### **11.2. Implement Code Quality Checks**

- **Task**: Maintain code standards.
  - **Subtasks**:
    - Install ESLint: `npm install eslint --save-dev`.
    - Install Prettier: `npm install --save-dev --save-exact prettier`.
    - Configure ESLint and Prettier to work together.
    - Add linting and formatting scripts to `package.json`.

---

## **12. Deployment Strategy**

### **12.1. Set Up Vercel Account and Project**

- **Task**: Deploy the application using Vercel.
  - **Subtasks**:
    - Sign up at [Vercel](https://vercel.com/) and connect your GitHub account.
    - Import the `fumanchu` repository into Vercel.

### **12.2. Configure Environment Variables in Vercel**

- **Task**: Securely manage environment variables.
  - **Subtasks**:
    - In Vercel dashboard, navigate to your project's settings.
    - Add environment variables:
      - `SUPABASE_URL`
      - `SUPABASE_ANON_KEY`
      - `KLAVIYO_API_KEY`
      - Any other required keys.

### **12.3. Set Up Custom Domain**

- **Task**: Configure your custom domain.
  - **Subtasks**:
    - In Vercel, add `contest.talesofmurder.com` as a domain.
    - Update your DNS records to point to Vercel's servers.

### **12.4. Configure Deployment Settings**

- **Task**: Ensure Remix is configured for Vercel.
  - **Subtasks**:
    - In `remix.config.js`, set `serverBuildTarget` to `'vercel'`:
      ```javascript
      module.exports = {
        serverBuildTarget: 'vercel',
        // ...other configurations
      };
      ```

---

## **13. Set Up CI/CD Pipeline**

### **13.1. Set Up GitHub Actions**

- **Task**: Automate testing and deployment.
  - **Subtasks**:
    - Create a workflow file `.github/workflows/ci.yml`:
      ```yaml
      name: CI Pipeline

      on:
        push:
          branches: [main]

      jobs:
        build-and-test:
          runs-on: ubuntu-latest
          steps:
            - uses: actions/checkout@v2
            - uses: actions/setup-node@v2
              with:
                node-version: '16'
            - run: npm install
            - run: npm run build
            - run: npm run test
      ```

### **13.2. Configure Vercel Token**

- **Task**: Allow GitHub Actions to deploy to Vercel.
  - **Subtasks**:
    - Generate a Vercel token from your Vercel account settings.
    - Add `VERCEL_TOKEN` to your GitHub repository secrets.

### **13.3. Update CI Workflow for Deployment**

- **Task**: Extend the CI workflow to deploy after tests pass.
  - **Subtasks**:
    - Update `.github/workflows/ci.yml` to include deployment:
      ```yaml
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-args: '--prod'
          working-directory: ./
          git-commit-sha: ${{ github.sha }}
      ```

### **13.4. Test CI/CD Pipeline**

- **Task**: Verify the pipeline works as expected.
  - **Subtasks**:
    - Commit and push changes to `main`.
    - Check the Actions tab in GitHub to monitor the workflow.
    - Ensure the application is deployed to Vercel upon successful completion.

---

## **14. Final Steps**

### **14.1. Copy Existing Landing and Thank-You Pages**

- **Task**: Integrate existing pages into the application.
  - **Subtasks**:
    - Copy the landing and thank-you page code from your main web development project.
    - Place them into appropriate routes in the Remix app, e.g., `app/routes/index.tsx` and `app/routes/thank-you.tsx`.
    - Update any links or resources to match the new project structure.

### **14.2. Documentation**

- **Task**: Update project documentation.
  - **Subtasks**:
    - Write or update the `README.md` with installation instructions, project overview, and usage guidelines.
    - Document any setup steps required for other developers or future reference.

### **14.3. Review and Testing**

- **Task**: Thoroughly test the application.
  - **Subtasks**:
    - Manually test all user flows (registration, referral, dashboard).
    - Ensure all features work as expected in different browsers and devices.
    - Fix any bugs or issues discovered during testing.

### **14.4. Launch Application**

- **Task**: Make the application live.
  - **Subtasks**:
    - Ensure the DNS changes have propagated, and the custom domain is working.
    - Monitor the application for any performance issues or errors.
    - Begin using the application internally.

---

## **15. Ongoing Maintenance and Improvement**

### **15.1. Monitor Application Performance**

- **Task**: Keep an eye on the app's health.
  - **Subtasks**:
    - Use Vercel's analytics to monitor performance.
    - Check error monitoring tools regularly.

### **15.2. Update Dependencies**

- **Task**: Keep the project up-to-date.
  - **Subtasks**:
    - Regularly check for updates to dependencies.
    - Test updates in a development environment before applying to production.

### **15.3. Plan for Future Enhancements**

- **Task**: Identify areas for improvement.
  - **Subtasks**:
    - Collect feedback from users.
    - Add new features or improvements based on needs.

---

# **Summary**

This step-by-step guide breaks down the development of the "fumanchu" application into manageable tasks and subtasks, covering everything from project setup to deployment and maintenance. Use this as a roadmap to guide your development process with the Cursor AI IDE and its Composer AI, tackling each task sequentially.

