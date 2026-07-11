# **Tech Stack**

## **1. Purpose**

This document defines the confirmed technology stack, repository
structure, folder structure, and technical rules for the MVP.

Developers and Codex must follow this file when setting up or modifying
the project.

# **2. Final Confirmed Stack**

## **2.1 Frontend**

Use:

Next.js App Router

TypeScript

Tailwind CSS

shadcn/ui

Recharts

Axios

npm

Frontend responsibility:

- Public landing page

- Login/register pages

- Agency dashboard

- Client portal

- Admin panel

- Report upload UI

- Dashboard review UI

- Client dashboard UI

- Lead tracker UI

- Charts and visual reports

## **2.2 Backend**

Use:

Node.js

Express.js

TypeScript

MongoDB hosted on AWS EC2

Mongoose

Joi

Better Auth authentication

Gemini API

Cloudflare R2

Razorpay

npm

Backend responsibility:

- API server

- Authentication verification

- Role-based authorization

- Agency APIs

- Client APIs

- Report upload APIs

- CSV parsing

- Metrics calculation

- Gemini summary generation

- Dashboard APIs

- Lead tracking APIs

- Admin APIs

- Activity logs

- Subscription billing with Razorpay

- Razorpay webhook handling

- Error handling

## **2.3 Database**

Use:

MongoDB hosted on AWS EC2

Mongoose

MongoDB stores:

- Users

- Better Auth sessions

- Better Auth accounts

- Better Auth verifications

- Agencies

- Agency members

- Clients

- Client users

- Report uploads

- Report metrics

- Dashboards

- Leads

- Lead status history

- Activity logs

- Admin users

- Subscriptions

- Billing events

## **2.4 Request Validation**

Use:

Joi

Joi should validate backend request bodies for:

- Agency registration metadata if handled by backend

- Client creation

- Client update

- Report upload metadata

- Lead upload metadata

- Lead status update

- Dashboard publish action

- Admin actions

Important rule:

Frontend validation is not enough.  
Every important backend API must validate request data with Joi.

## **2.5 Authentication**

Use:

Better Auth

Better Auth should handle:

- User signup

- User login

- Session management

- Password reset

- OAuth login if needed later

The backend should verify Better Auth sessions before allowing access to
protected APIs.

Backend should use:

auth.middleware.ts

for:

- Verifying logged-in users

- Reading authenticated user identity

- Attaching user context to request

- Protecting private APIs

Important rule:

Never depend only on frontend route protection.  
Backend must always verify authentication and authorization.

## **2.6 Authorization**

Authorization should be handled inside the backend using middleware and
service-level checks.

The backend must check:

- Is the user logged in?

- What is the user role?

- Which agency does the user belong to?

- Which client does the user belong to?

- Is the user allowed to access this resource?

- Does the requested document belong to the userâ€™s agency or client?

Main rule:

Agency A must never access Agency Bâ€™s data.  
Client A must never access Client Bâ€™s data.

## **2.7 AI**

Use:

Gemini API

Gemini should be used for:

- Client-friendly campaign summaries

- Report explanation

- Performance insights

- Suggested next steps

- Column mapping suggestions if needed

Gemini must not be used for:

- Final spend calculation

- Final lead count calculation

- Final cost per lead calculation

- CTR calculation

- CPC calculation

- Permission checks

- Billing decisions

- Auto-publishing dashboards

Important rule:

Backend calculates numbers.  
Gemini explains numbers.

## **2.8 File Upload**

Use:

Multer

Cloudflare R2

Multer responsibility:

- Receive uploaded files in Express backend

- Validate file type

- Validate file size

- Pass file for parsing or storage

Cloudflare R2 responsibility:

- Store uploaded report files

- Store uploaded lead files if needed

- Keep original uploads available for audit/debugging

For MVP, support:

.csv only

Do not support Excel files in the first MVP unless the team
intentionally expands scope.

## **2.9 CSV Parsing**

Use a backend CSV parsing library.

Recommended package:

csv-parser

CSV parser responsibility:

- Read uploaded CSV files

- Extract headers

- Extract rows

- Detect empty files

- Help validate required columns

- Pass cleaned rows to the metric calculator service

## **2.10 Billing**

Use:

Razorpay Subscriptions

Billing currency:

INR (â‚¹)

MVP plans:

- Starter: â‚¹499/month, includes 1 agency user
- Team: â‚¹999/month, includes 2 agency users
- Extra agency user: â‚¹499/month per extra agency user

Billing rules:

- Agency users are paid seats.
- Client users are free.
- Backend must create and verify Razorpay subscription/payment flows.
- Backend must verify Razorpay webhook signatures.
- Subscription status must be stored in MongoDB and used for access control.

## **2.11 Charts**

Use:

Recharts

Chart usage:

- Campaign performance chart

- Spend chart

- Leads chart

- Cost per lead chart

- Lead status chart

- Campaign-wise breakdown

Client dashboard charts should be simple and easy to understand.

Agency dashboard charts can be more detailed.

## **2.12 Styling and UI Components**

Use:

Tailwind CSS

shadcn/ui

Tailwind is used for styling.

shadcn/ui is used for reusable production-quality UI components such as:

- Buttons

- Cards

- Dialogs

- Tables

- Forms

- Inputs

- Selects

- Tabs

- Toasts

- Dropdowns

- Alert dialogs

Frontend rule:

Use shadcn/ui for common UI components instead of building everything
from scratch.

## **2.13 HTTP Client**

Use:

Axios

Axios should be used for frontend API calls.

Recommended location:

frontend/src/lib/api.ts

Axios should handle:

- Base backend URL

- Auth token attachment if needed

- Common error responses

- API request helpers

## **2.14 Package Manager**

Use:

npm

Do not use pnpm or yarn unless the team intentionally changes this
decision.

# **3. Repository Strategy**

The project will use separate frontend and backend repositories.

Use:

frontend repo

backend repo

This means:

- Frontend code lives in the frontend repo

- Backend code lives in the backend repo

- Each repo should have its own README

- Each repo should have its own .env.example

- Each repo should have its own AGENTS.md for Codex

- Shared product docs should be copied or made available to both repos
  so Codex has context

Important Codex rule:

Codex can only follow the files it can see.  
Make sure relevant docs are available in the repo where Codex is
working.

# **4. Deployment Stack**

## **4.0 Early Staging Deployment Rule**

Deployment should start in the initial phase as a private staging
environment.

Do not wait until the end of the MVP to deploy for the first time.

Early staging deployment means:

- Frontend skeleton is deployed to Vercel after basic setup.

- Backend health-check API is deployed to an EC2 staging instance after
  basic setup.

- MongoDB staging instance on EC2 is connected early.

- Frontend and backend staging URLs are used for CORS, Better Auth, Razorpay
  webhook testing, and environment validation.

- Staging and production environment variables must be kept separate.

- Production launch still happens only after the full MVP demo flow is
  complete and verified.

Important:

Early deployment is for integration testing and feedback. It is not the
same as a public production launch.

## **4.1 Frontend Deployment**

Use:

Vercel

Vercel will host both staging and production frontend deployments.

Vercel will host:

- Next.js frontend

- Public website

- Agency UI

- Client UI

- Admin UI

## **4.2 Backend Deployment**

Use:

AWS

Recommended AWS option for MVP:

AWS EC2

Recommended MVP deployment style:

Run the Express backend on an EC2 instance behind Nginx and manage the
Node.js process with PM2.

Reason:

- Keeps the deployment model explicit and easy to reason about

- Works well for a single MVP backend service

- Gives direct control over server configuration, reverse proxy, and
  process management

Backend will host:

- Express API server

- File upload endpoints

- CSV processing

- Metrics calculation

- Gemini service

- Admin APIs

For the initial phase, the backend should expose at least:

- GET /api/health

This endpoint should be deployed early so frontend-backend connectivity,
CORS, environment variables, and hosting setup can be verified before
feature work becomes large.

## **4.3 Database Hosting**

Use:

MongoDB hosted on AWS EC2

MongoDB on EC2 will host staging and production databases.

Important:

- Use a separate staging database for early deployment testing.

- Do not test staging features against the production database.

- Production data and staging data must remain separate.

## **4.4 File Storage**

Use:

Cloudflare R2

Cloudflare R2 will store uploaded report files and lead files for MVP.

For staging, use a separate R2 bucket or a clearly separated staging
prefix.

Important:

Uploaded raw reports should not be exposed publicly without
authorization.

# **5. Environment Variables**

Each repo should include:

.env.example

Do not commit real .env files.

Environment variables must be managed separately for:

- Local development

- Staging

- Production

Do not reuse production secrets in staging unless the provider requires
the same public key. Backend secrets must stay server-side in every
environment.

## **5.1 Frontend Environment Variables**

Example:

NEXT_PUBLIC_API_BASE_URL=

NEXT_PUBLIC_BETTER_AUTH_URL=

NEXT_PUBLIC_RAZORPAY_KEY_ID=

Do not place backend-only secrets in the frontend repo.

The frontend must not include:

- BETTER_AUTH_SECRET
- GEMINI_API_KEY
- R2_ACCESS_KEY_ID
- R2_SECRET_ACCESS_KEY
- MONGODB_URI
- Razorpay backend-only credentials

## **5.2 Backend Environment Variables**

Example:

NODE_ENV=

PORT=

MONGODB_URI=

BETTER_AUTH_SECRET=

BETTER_AUTH_URL=

BETTER_AUTH_TRUSTED_ORIGINS=

GEMINI_API_KEY=

R2_ACCOUNT_ID=

R2_ACCESS_KEY_ID=

R2_SECRET_ACCESS_KEY=

R2_BUCKET_NAME=

R2_PUBLIC_BASE_URL=

R2_ENDPOINT=

RAZORPAY_KEY_ID=

RAZORPAY_KEY_SECRET=

RAZORPAY_WEBHOOK_SECRET=

FRONTEND_URL=

BACKEND_URL=

Important rules:

- Never commit real environment variables

- Never expose Gemini API key to frontend

- Never expose R2 secret to frontend

- Never expose database URI to frontend

- Never expose Razorpay backend-only credentials to frontend

# **6. Backend Folder Structure**

The backend must follow this structure:

backend/src/

â”œâ”€â”€ index.ts

â”œâ”€â”€ app.ts

â”œâ”€â”€ config/

â”‚ â”œâ”€â”€ constants/

â”‚ â”‚ â””â”€â”€ index.ts

â”œâ”€â”€ middlewares/

â”‚ â”œâ”€â”€ auth.middleware.ts

â”‚ â”œâ”€â”€ error.middleware.ts

â”‚ â””â”€â”€ upload.middleware.ts

â”œâ”€â”€ utils/

â”‚ â”œâ”€â”€ AppError.ts

â”‚ â”œâ”€â”€ catchAsync.ts

â”‚ â””â”€â”€ storage.ts

â””â”€â”€ modules/

â””â”€â”€ \<feature\>/

â”œâ”€â”€ \<feature\>.schema.ts

â”œâ”€â”€ \<feature\>.model.ts

â”œâ”€â”€ \<feature\>.controller.ts

â”œâ”€â”€ \<feature\>.routes.ts

â”œâ”€â”€ \<feature\>.service.ts

â”œâ”€â”€ \<feature\>.validation.ts

â””â”€â”€ \<feature\>.test.ts

## **6.1 Backend File Responsibilities**

## **index.ts**

Server entry point.

Responsibilities:

- Load environment variables

- Connect to MongoDB

- Start Express server

- Handle server-level errors

## **app.ts**

Express app setup.

Responsibilities:

- Initialize Express app

- Configure CORS

- Configure JSON parsing

- Configure routes

- Configure global error middleware

- Configure health check route

## **config/constants/index.ts**

Named constants.

Use this file to avoid magic strings and magic numbers.

Examples:

MAX_FILE_SIZE

ALLOWED_FILE_TYPES

USER_ROLES

DASHBOARD_STATUS

LEAD_STATUS

REPORT_STATUS

## **Passport/OAuth note**

The MVP uses Better Auth for authentication. Do not create or implement `config/passport.ts` for the MVP. Passport-based Google OAuth is a future feature only if the team intentionally changes the auth decision.

## **middlewares/auth.middleware.ts**

Authentication and authorization middleware.

Responsibilities:

- Verify Better Auth session

- Attach authenticated user context to request

- Protect private routes

- Support role checks

- Reject unauthorized requests

## **middlewares/error.middleware.ts**

Global error handler.

Responsibilities:

- Handle AppError

- Handle validation errors

- Handle unexpected errors

- Send consistent error responses

- Avoid exposing internal server details in production

## **middlewares/upload.middleware.ts**

Upload middleware.

Responsibilities:

- Configure Multer

- Restrict file types

- Restrict file size

- Handle upload errors

## **utils/AppError.ts**

Custom error class.

Use this for operational errors.

Example use cases:

- Unauthorized access

- Forbidden access

- Missing resource

- Invalid upload

- Validation failure

## **utils/catchAsync.ts**

Async error wrapper.

Use this to avoid repeated try/catch blocks in controllers.

## **utils/storage.ts**

Cloudflare R2 helper.

Responsibilities:

- Configure Cloudflare R2

- Upload files

- Delete files if needed

- Return secure file URLs

# **7. Backend Module Structure**

Each feature should live inside:

backend/src/modules/\<feature\>/

Example:

backend/src/modules/client/

A module can contain:

client.schema.ts

client.model.ts

client.controller.ts

client.routes.ts

client.service.ts

client.validation.ts

client.test.ts

## **7.1 Module File Responsibilities**

## **\<feature\>.schema.ts**

Contains the Mongoose schema definition.

## **\<feature\>.model.ts**

Contains:

- Mongoose model

- TypeScript interface/type

- Model export

## **\<feature\>.controller.ts**

Handles request and response logic.

Controllers should stay thin.

Controllers should call services for business logic.

## **\<feature\>.routes.ts**

Defines Express routes for the module.

Routes should use:

- Auth middleware

- Role middleware if needed

- Joi validation middleware

- Controller functions

## **\<feature\>.service.ts**

Contains business logic.

Use service files for:

- Creating records

- Updating records

- Checking ownership

- Processing reports

- Calculating metrics

- Calling Gemini service

If a module needs multiple services, create a service/ folder.

## **\<feature\>.validation.ts**

Contains Joi validation schemas for the module.

Use this for:

- Request body validation

- Query validation

- Params validation if needed

## **\<feature\>.test.ts**

Contains tests for the module.

If the module needs multiple test files, create a \_\_tests\_\_/ folder.

## **7.2 Subfolder Rule**

Only create a subfolder when it contains more than one file.

Example:

Use this when there is only one service file:

modules/client/client.service.ts

Use this when there are multiple service files:

modules/user/service/user.service.ts

modules/user/service/email.service.ts

Use this when there is only one test file:

modules/client/client.test.ts

Use this when there are multiple test files:

modules/user/\_\_tests\_\_/auth.test.ts

modules/user/\_\_tests\_\_/user-management.test.ts

Do not create empty folders.

# **8. Recommended Backend Modules**

Create modules based on features.

Recommended MVP modules:

auth

agency

client

report

dashboard

lead

activityLog

admin

billing

ai

## **8.1 Auth Module**

Path:

backend/src/modules/auth/

Responsibilities:

- Better Auth bootstrap flow if needed

- Login/register support if backend stores user profile

- User role handling

- Auth-related helpers

## **8.2 Agency Module**

Path:

backend/src/modules/agency/

Responsibilities:

- Agency workspace

- Agency members

- Agency status

- Agency-level stats

## **8.3 Client Module**

Path:

backend/src/modules/client/

Responsibilities:

- Add client

- List clients

- View client details

- Update client details

- Client ownership checks

## **8.4 Report Module**

Path:

backend/src/modules/report/

Responsibilities:

- Report upload

- CSV parsing

- Column mapping

- File validation

- Metrics calculation

- Report status handling

If multiple services are needed, use:

backend/src/modules/report/service/

Example:

reportParser.service.ts

columnMapper.service.ts

metricCalculator.service.ts

## **8.5 Dashboard Module**

Path:

backend/src/modules/dashboard/

Responsibilities:

- Dashboard draft creation

- Dashboard review

- Dashboard update

- Dashboard publish

- Dashboard visibility rules

## **8.6 Lead Module**

Path:

backend/src/modules/lead/

Responsibilities:

- Lead upload

- Lead list

- Lead status update

- Lead remarks

- Follow-up date

- Lead status history

## **8.7 Activity Log Module**

Path:

backend/src/modules/activityLog/

Responsibilities:

- Create activity logs

- List activity logs

- Admin activity monitoring

## **8.8 Admin Module**

Path:

backend/src/modules/admin/

Responsibilities:

- Admin stats

- View agencies

- View agency details

- View failed uploads

- Suspend agency

- Reactivate agency

## **8.9 Billing Module**

Path:

backend/src/modules/billing/

Responsibilities:

- Return available MVP plans
- Create Razorpay subscriptions
- Store agency subscription records
- Handle Razorpay webhooks
- Verify Razorpay webhook signatures
- Restrict agency access when subscription is inactive
- Log important billing events

## **8.10 AI Module**

Path:

backend/src/modules/ai/

Responsibilities:

- Gemini API wrapper

- Generate report summaries

- Generate insights

- Handle AI failure fallback

Gemini should be called through this module, not directly inside
controllers.

# **9. Frontend Folder Structure**

Use this industry-standard Next.js App Router structure:

frontend/

â”œâ”€â”€ src/

â”‚ â”œâ”€â”€ app/

â”‚ â”‚ â”œâ”€â”€ (public)/

â”‚ â”‚ â”‚ â”œâ”€â”€ page.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ pricing/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ login/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ register/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”œâ”€â”€ agency/

â”‚ â”‚ â”‚ â”œâ”€â”€ layout.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ dashboard/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ clients/

â”‚ â”‚ â”‚ â”‚ â”œâ”€â”€ page.tsx

â”‚ â”‚ â”‚ â”‚ â”œâ”€â”€ new/

â”‚ â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ \[clientId\]/

â”‚ â”‚ â”‚ â”‚ â”œâ”€â”€ page.tsx

â”‚ â”‚ â”‚ â”‚ â”œâ”€â”€ upload-report/

â”‚ â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ leads/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â””â”€â”€ reports/

â”‚ â”‚ â”‚ â””â”€â”€ \[reportId\]/

â”‚ â”‚ â”‚ â””â”€â”€ review/

â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”œâ”€â”€ client/

â”‚ â”‚ â”‚ â”œâ”€â”€ layout.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ dashboard/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ leads/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â””â”€â”€ reports/

â”‚ â”‚ â”‚ â””â”€â”€ \[dashboardId\]/

â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”œâ”€â”€ admin/

â”‚ â”‚ â”‚ â”œâ”€â”€ layout.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ dashboard/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ agencies/

â”‚ â”‚ â”‚ â”‚ â”œâ”€â”€ page.tsx

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ \[agencyId\]/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â”œâ”€â”€ failed-uploads/

â”‚ â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”‚ â””â”€â”€ activity-logs/

â”‚ â”‚ â”‚ â””â”€â”€ page.tsx

â”‚ â”‚ â”œâ”€â”€ layout.tsx

â”‚ â”‚ â”œâ”€â”€ loading.tsx

â”‚ â”‚ â”œâ”€â”€ error.tsx

â”‚ â”‚ â””â”€â”€ not-found.tsx

â”‚ â”œâ”€â”€ components/

â”‚ â”‚ â”œâ”€â”€ ui/

â”‚ â”‚ â”œâ”€â”€ layout/

â”‚ â”‚ â”œâ”€â”€ forms/

â”‚ â”‚ â”œâ”€â”€ tables/

â”‚ â”‚ â”œâ”€â”€ charts/

â”‚ â”‚ â”œâ”€â”€ dashboard/

â”‚ â”‚ â”œâ”€â”€ leads/

â”‚ â”‚ â””â”€â”€ shared/

â”‚ â”œâ”€â”€ features/

â”‚ â”‚ â”œâ”€â”€ auth/

â”‚ â”‚ â”œâ”€â”€ agency/

â”‚ â”‚ â”œâ”€â”€ clients/

â”‚ â”‚ â”œâ”€â”€ reports/

â”‚ â”‚ â”œâ”€â”€ dashboards/

â”‚ â”‚ â”œâ”€â”€ leads/

â”‚ â”‚ â””â”€â”€ admin/

â”‚ â”œâ”€â”€ hooks/

â”‚ â”œâ”€â”€ lib/

â”‚ â”‚ â”œâ”€â”€ api.ts

â”‚ â”‚ â”œâ”€â”€ auth.ts

â”‚ â”‚ â”œâ”€â”€ constants.ts

â”‚ â”‚ â””â”€â”€ utils.ts

â”‚ â”œâ”€â”€ types/

â”‚ â””â”€â”€ styles/

â”œâ”€â”€ public/

â”œâ”€â”€ components.json

â”œâ”€â”€ next.config.ts

â”œâ”€â”€ tailwind.config.ts

â”œâ”€â”€ tsconfig.json

â”œâ”€â”€ package.json

â””â”€â”€ .env.example

## **9.1 Frontend Folder Responsibilities**

## **src/app**

Contains Next.js App Router pages and layouts.

Use route groups and nested layouts where useful.

## **src/components/ui**

Contains shadcn/ui components.

Do not manually rewrite components that shadcn/ui already provides.

## **src/components/layout**

Contains shared layout components.

Examples:

- Sidebar

- Topbar

- Agency layout shell

- Client layout shell

- Admin layout shell

## **src/components/forms**

Contains reusable form components.

Examples:

- Client form

- Login form

- Register form

- Report upload form

- Lead status form

## **src/components/tables**

Contains reusable table components.

Examples:

- Clients table

- Leads table

- Reports table

- Agencies table

- Activity logs table

## **src/components/charts**

Contains reusable chart components.

Examples:

- Spend chart

- Leads chart

- Campaign breakdown chart

- Lead status chart

## **src/features**

Contains feature-specific frontend logic and components.

Use this for larger feature areas.

Examples:

features/clients

features/reports

features/leads

features/admin

## **src/hooks**

Contains custom React hooks.

Examples:

useAuth

useClients

useReports

useLeads

## **src/lib**

Contains shared utilities and configuration.

Important files:

api.ts

auth.ts

constants.ts

utils.ts

## **src/types**

Contains shared TypeScript types.

Examples:

User

Agency

Client

Report

Dashboard

Lead

ActivityLog

# **10. Development Commands**

## **10.1 Frontend**

cd frontend

npm install

npm run dev

npm run build

npm run lint

## **10.2 Backend**

cd backend

npm install

npm run dev

npm run build

npm start

npm run lint

Recommended backend scripts:

{

"dev": "tsx watch src/index.ts",

"build": "tsc",

"start": "node dist/index.js",

"lint": "eslint ."

}

# **11. Recommended Packages**

## **11.1 Backend Packages**

Install:

express

mongoose

joi

cors

dotenv

multer

@aws-sdk/client-s3

csv-parser

helmet

express-rate-limit

morgan

better-auth

@google/generative-ai

razorpay

Dev dependencies:

typescript

tsx

ts-node

nodemon

@types/node

@types/express

@types/cors

@types/multer

eslint

prettier

jest

supertest

## **11.2 Frontend Packages**

Install:

next

react

react-dom

typescript

tailwindcss

axios

recharts

lucide-react

react-hook-form

clsx

tailwind-merge

class-variance-authority

Use shadcn/ui for UI components.

# **12. Testing Stack**

Recommended backend testing tools:

jest

supertest

Important tests:

- Auth middleware tests

- Role permission tests

- Agency ownership tests

- Client ownership tests

- Billing access restriction tests

- Razorpay webhook signature tests

- Report upload validation tests

- CSV parsing tests

- Metrics calculation tests

- Dashboard publish tests

- Lead status update tests

- Admin access tests

Frontend testing can be added later.

Priority for MVP:

Backend permission and upload tests first.

# **13. Security and Production Tools**

Before launch, backend should include:

helmet

express-rate-limit

proper CORS settings

global error handling

request logging

file size limits

environment variable validation

Minimum production requirements:

- Staging deployment has already been verified.

- Environment variables configured

- MongoDB on EC2 connected

- Frontend deployed

- Backend deployed

- CORS configured correctly

- File upload size limit enforced

- Unauthorized access blocked

- Admin access protected

- Errors handled cleanly

- Real secrets not committed

Minimum staging requirements:

- Frontend staging URL is live on Vercel.

- Backend staging URL is live on AWS EC2.

- GET /api/health works on the deployed backend.

- Frontend can call the deployed backend.

- CORS allows the staging frontend domain only.

- Staging database and staging secrets are separate from production.

# **14. Important Technical Rules**

## **14.1 Backend Calculates, Gemini Explains**

Backend must calculate:

- Spend

- Leads

- CPL

- CTR

- CPC

- Campaign totals

Gemini only explains calculated data.

## **14.2 Agency and Client Isolation**

Every agency-owned document must include:

agencyId

Every client-specific document must include:

agencyId

clientId

Every protected query must check ownership.

## **14.3 Draft Before Publish**

Every dashboard starts as:

draft

Client can see only:

published dashboards

where:

visibleToClient === true

## **14.4 Validate All Inputs**

Use Joi on the backend.

Frontend validation is helpful, but backend validation is required.

## **14.5 Keep Admin Separate**

Admin routes and pages must be separate from agency/client routes.

Admin routes must use admin-only authentication and role checks.

# **15. Final Stack Summary**

The final MVP stack is:

Frontend:

Next.js App Router + TypeScript + Tailwind CSS + shadcn/ui + Recharts +
Axios

Backend:

Node.js + Express.js + TypeScript + Joi + Mongoose

Database:

MongoDB on EC2

Authentication:

Better Auth

AI:

Gemini API

File Upload:

Multer + Cloudflare R2

CSV Support:

CSV only for MVP

Deployment:

Frontend on Vercel

Backend on AWS

Database on MongoDB on EC2

Deployment timing:

Deploy private staging during the initial setup phase. Treat final
production deployment as launch readiness work after the MVP demo flow
passes.

Package Manager:

npm

Repository:

Separate frontend and backend repositories

This stack is suitable for the MVP because:

- The team can continue using MongoDB

- Express gives full backend control

- TypeScript improves safety

- Better Auth keeps authentication under app control

- Next.js is strong for SaaS dashboards

- shadcn/ui helps build a professional UI faster

- Gemini supports AI summaries

- Cloudflare R2 simplifies file storage

- AWS gives a production-ready backend deployment path

