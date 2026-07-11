# **MVP Development Tasks**

## **1. Purpose**

This file converts the product plan into clear development tasks.

Developers and Codex must use this file to build the MVP step by step.

Do not build random features outside this task list unless the team
intentionally updates the MVP scope.

# **2. MVP Goal**

The first working MVP must prove this flow:

Agency registers  
→ Agency activates Razorpay subscription or allowed trial  
→ Agency dashboard opens  
→ Agency adds client  
→ Agency uploads Meta Ads CSV  
→ System validates and processes report  
→ System calculates metrics  
→ Gemini generates summary  
→ Dashboard draft is created  
→ Agency reviews and publishes dashboard  
→ Client logs in  
→ Client views dashboard  
→ Agency uploads leads  
→ Client views leads  
→ Client updates lead status  
→ Agency tracks updated lead progress

# **2A. Current Weekly Build Priority**

Follow this implementation order for the current week:

1.  Project setup

2.  Initial private staging deployment

3.  Authentication and user roles

4.  Activity logs

5.  Admin foundation

6.  Client management

Razorpay subscription billing remains part of the MVP, but it should be
implemented only after the core role, logging, admin, and client
management foundation is in place.

# **2B. Temporary Internal Staging Mode**

For the current internal development week, the team may temporarily
allow agency and admin development flows to continue after authenticated
bootstrap even before Razorpay is implemented.

Use this temporary mode only to unblock internal staging work for auth,
admin foundation, and client management.

Do not treat this as the final MVP or production access rule.

The final MVP flow still requires billing or allowed trial activation
before the agency dashboard and paid agency features are treated as
fully available.

# **3. Development Rules**

- Work in small pull requests.

- One task should usually become one GitHub issue.

- Do not push directly to main.

- Do not modify unrelated files.

- Do not build features outside MVP scope.

- Use TypeScript in frontend and backend.

- Use Better Auth for authentication.

- Use Joi for backend validation.

- Use Mongoose for MongoDB models.

- Use Cloudflare R2 for file storage.

- Use Razorpay for subscription billing.

- Use Gemini only for summaries and insights.

- Backend must calculate report metrics.

- Gemini must not calculate final numbers.

- Every protected query must check ownership.

- Every agency-owned document must include agencyId.

- Every client-specific document must include both agencyId and
  clientId.

# **4. Task Status Values**

Use these statuses when tracking tasks:

not_started

in_progress

blocked

in_review

done

# **5. Phase 1: Project Setup**

## **Task 1: Set up frontend repository**

Type:

frontend

Goal:

Create the Next.js frontend project.

Tech:

Next.js App Router

TypeScript

Tailwind CSS

shadcn/ui

npm

Acceptance criteria:

- Next.js app is created.

- TypeScript is enabled.

- Tailwind CSS is configured.

- shadcn/ui is configured.

- Basic folder structure follows TECH_STACK.md.

- App runs locally using npm run dev.

- .env.example exists.

- README exists.

## **Task 2: Set up backend repository**

Type:

backend

Goal:

Create the Express TypeScript backend project.

Tech:

Node.js

Express.js

TypeScript

npm

Acceptance criteria:

- Express app is created.

- TypeScript is configured.

- Backend folder structure follows TECH_STACK.md.

- src/index.ts exists.

- src/app.ts exists.

- Health check route exists.

- App runs locally.

- .env.example exists.

- README exists.

Suggested endpoint:

GET /api/health

## **Task 3: Configure backend base middleware**

Type:

backend

Goal:

Add common backend middleware.

Acceptance criteria:

- CORS is configured.

- JSON parsing is configured.

- Helmet is added.

- Morgan or request logger is added.

- Global error middleware exists.

- AppError.ts exists.

- catchAsync.ts exists.

- Environment variables are loaded safely.

Files:

backend/src/app.ts

backend/src/middlewares/error.middleware.ts

backend/src/utils/AppError.ts

backend/src/utils/catchAsync.ts

## **Task 4: Connect MongoDB on EC2**

Type:

backend

Goal:

Connect backend to MongoDB hosted on EC2 using Mongoose.

Acceptance criteria:

- MongoDB connection function exists.

- MONGODB_URI is read from environment variables.

- Connection success is logged.

- Connection errors are handled.

- Server should not start successfully if database connection fails.

## **Task 5: Add constants file**

Type:

backend

Goal:

Create named constants to avoid magic strings.

Acceptance criteria:

- User roles are defined.

- Agency statuses are defined.

- Client statuses are defined.

- Dashboard statuses are defined.

- Report statuses are defined.

- Lead statuses are defined.

- File upload limits are defined.

- Allowed file types are defined.

File:

backend/src/config/constants/index.ts

# **5A. Phase 1A: Initial Staging Deployment**

This phase starts deployment early without making the product public.

Goal:

Create a private staging environment immediately after the basic
frontend and backend skeletons exist.

Important rule:

Initial staging deployment is required for integration testing. It is
not the production launch.

## **Task 5A: Deploy frontend skeleton to Vercel staging**

Type:

frontend

Goal:

Deploy the basic Next.js app to a private Vercel staging environment.

Acceptance criteria:

- Vercel project exists for the frontend.

- Staging frontend URL is available.

- Vercel staging build passes.

- Required frontend environment variables are configured for staging.

- Frontend does not contain backend-only secrets.

- README or deployment notes include the staging frontend URL.

## **Task 5B: Deploy backend health API to EC2 staging**

Type:

backend

Goal:

Deploy the basic Express API to an AWS EC2 instance with a working
health check.

Suggested endpoint:

GET /api/health

Acceptance criteria:

- EC2 instance exists for the backend.

- Staging backend URL is available.

- GET /api/health works on the deployed backend.

- Backend start script works in the deployed environment.

- Required backend environment variables are configured for staging.

- Backend secrets are not exposed to the frontend.

- README or deployment notes include the staging backend URL.

## **Task 5C: Configure staging database and storage**

Type:

backend

Goal:

Connect staging deployment to isolated staging infrastructure.

Acceptance criteria:

- MongoDB staging instance on EC2 exists.

- Backend staging uses the staging MongoDB connection details.

- Staging database is separate from production.

- Cloudflare R2 staging bucket or prefix is documented.

- No production data is used for early staging tests.

## **Task 5D: Configure staging CORS and environment URLs**

Type:

fullstack

Goal:

Connect the deployed frontend and backend safely.

Acceptance criteria:

- Frontend staging uses the deployed backend URL.

- Backend CORS allows the staging frontend URL.

- Local development URL and staging URL are both handled intentionally.

- FRONTEND_URL and BACKEND_URL are documented for staging.

- CORS is not configured as a broad wildcard for protected APIs.

## **Task 5E: Verify initial staging deployment**

Type:

fullstack

Goal:

Confirm the deployed skeleton works before feature development expands.

Acceptance criteria:

- Frontend staging page loads.

- Backend staging health endpoint returns success.

- Frontend can call the deployed backend health endpoint if a health
  check UI or API utility exists.

- Staging deployment notes list known missing features.

- Production launch is still blocked until the full MVP demo flow passes.

# **6. Phase 2: Authentication and User Bootstrap**

## **Task 6: Configure Better Auth in frontend**

Type:

frontend

Goal:

Set up Better Auth authentication in the Next.js app.

Acceptance criteria:

- Better Auth client is configured.

- Login page works with Better Auth.

- Register page works with Better Auth.

- The public register flow performs Better Auth signup first.

- After signup/login, the client calls `POST /api/auth/bootstrap`
  before starting any owner-only billing flow.

- Logged-in state can be accessed in frontend.

- User can log out.

- Environment variables are documented.

## **Task 7: Configure Better Auth in backend**

Type:

backend

Goal:

Verify Better Auth-authenticated users in Express APIs.

Acceptance criteria:

- auth.middleware.ts verifies Better Auth session.

- Protected routes reject unauthenticated users.

- Local user is fetched using Better Auth user ID.

- User context is attached to request.

- Suspended/inactive users are rejected.

File:

backend/src/middlewares/auth.middleware.ts

## **Task 8: Create user model**

Type:

backend

Goal:

Create user model aligned with Better Auth.

Collection:

users

Acceptance criteria:

- User schema exists.

- User model exists.

- TypeScript interface exists.

- Better Auth user ID is the primary identity.

- The canonical Better Auth user record stores app fields including
  `role`, `status`, `lastLoginAt`, `emailVerified`, and `image`.

- Role enum is used.

- Status enum is used.

- Timestamps are enabled.

- Indexes are added.

Module:

backend/src/modules/user/

## **Task 9: Create agency model and agency member model**

Type:

backend

Goal:

Create agency workspace data models.

Collections:

agencies

agency_members

Acceptance criteria:

- Agency schema/model exists.

- Agency member schema/model exists.

- Agency owner relationship is supported.

- agencyId + userId unique index exists in agency members.

- Timestamps are enabled.

- Status enums are used.

Module:

backend/src/modules/agency/

## **Task 10: Create auth bootstrap API**

Type:

backend

Goal:

Create application bootstrap and agency workspace after Better Auth signup.

Suggested endpoint:

POST /api/auth/bootstrap

Acceptance criteria:

- Joi validation is used.

- Better Auth session identity is verified.

- App user profile is created or updated if needed.

- Agency workspace is created for agency registration.

- User becomes agency_owner.

- Agency member record is created.

- Duplicate bootstrap requests are handled safely.

- Activity log is created.

## **Task 11: Create current user API**

Type:

backend

Goal:

Return authenticated user profile and role.

Suggested endpoint:

GET /api/auth/me

Acceptance criteria:

- Protected by auth middleware.

- Returns user role.

- Returns agencyId if agency user.

- Returns clientId if client user.

- Returns adminRole if admin.

- Does not expose secrets.

## **Task 12: Implement frontend role redirect**

Type:

frontend

Goal:

Redirect users to the correct dashboard after login.

Redirect rules:

agency_owner / agency_member → /agency/dashboard

client_user → /client/dashboard

admin → /admin/dashboard

Acceptance criteria:

- Frontend calls GET /api/auth/me.

- Correct redirect happens after login.

- Unknown role shows clear error.

- Loading state exists during redirect.

# **Deferred Phase: Subscription Billing with Razorpay**

Do this phase only after project setup, initial private staging,
authentication and user roles, activity logs, admin panel, and client
management are in place.

## **Task 12A: Create billing constants**

Type:

backend

Goal:

Define MVP pricing and subscription statuses.

MVP plans:

- Starter: ₹499/month, includes 1 agency user
- Team: ₹999/month, includes 2 agency users
- Extra agency user: ₹499/month per extra agency user
- Client users are free

Acceptance criteria:

- Plan constants exist.
- Subscription status constants exist.
- Amounts are stored in paise for backend calculations.
- Currency is INR.
- No pricing value is hardcoded randomly across controllers.

## **Task 12B: Create subscription and billing event models**

Type:

backend

Goal:

Store Razorpay subscription state and billing events.

Collections:

subscriptions

billing_events

Acceptance criteria:

- Subscription schema/model exists.
- Billing event schema/model exists.
- agencyId is required on subscription records.
- Razorpay subscription/customer IDs are stored.
- Subscription status enum is used.
- Webhook/billing events are stored for audit/debugging.
- Indexes are added.

Module:

backend/src/modules/billing/

## **Task 12C: Create billing plans API**

Type:

backend

Goal:

Return available MVP subscription plans.

Suggested endpoint:

GET /api/billing/plans

Acceptance criteria:

- Returns Starter and Team plans.
- Returns INR pricing.
- Returns included agency user count.
- Shows client users are free.
- Does not expose Razorpay secrets.

## **Task 12D: Create Razorpay subscription API**

Type:

backend

Goal:

Allow agency owners to start a Razorpay subscription.

Suggested endpoint:

POST /api/billing/subscriptions/create

Acceptance criteria:

- Only agency_owner can create subscription.
- Validates selected plan.
- Creates/uses Razorpay customer if needed.
- Creates Razorpay subscription or checkout flow data.
- Stores pending subscription record.
- Returns data needed by frontend to open Razorpay flow.
- Does not expose Razorpay key secret.
- Runs only after the authenticated bootstrap step completes.

## **Task 12E: Create Razorpay webhook API**

Type:

backend

Goal:

Receive and verify Razorpay subscription/payment events.

Suggested endpoint:

POST /api/billing/webhook/razorpay

Acceptance criteria:

- Uses raw request body where required for signature verification.
- Verifies Razorpay webhook signature.
- Rejects invalid signatures.
- Updates subscription status.
- Updates agency subscriptionStatus if needed.
- Stores billing event record.
- Handles duplicate events safely.

## **Task 12F: Create current subscription API**

Type:

backend

Goal:

Return logged-in agency subscription status.

Suggested endpoint:

GET /api/billing/subscription/current

Acceptance criteria:

- Protected by auth middleware.
- Returns current plan and subscription status.
- Returns included agency seats and extra seats.
- Does not expose Razorpay secrets.
- Agency users can view only their own agency subscription.

## **Task 12G: Add subscription access guard**

Type:

backend

Goal:

Restrict paid product APIs when subscription is inactive.

Acceptance criteria:

- Agency paid feature APIs require active or trialing subscription.
- Pending, inactive, cancelled, halted, expired, and payment_failed statuses are blocked.
- Billing/account recovery APIs remain accessible.
- Client portal access depends on agency subscription being active or trialing.
- Clear error response is returned when billing is inactive.

## **Task 12H: Create pricing and billing frontend pages**

Type:

frontend

Goal:

Allow agencies to view pricing and start/manage Razorpay subscription.

Pages:

/pricing

/agency/billing

/agency/billing/success

/agency/billing/failed

Acceptance criteria:

- Pricing shows Starter and Team plans.
- Client users are shown as free.
- Agency billing page shows current subscription status.
- Agency owner can start Razorpay subscription flow.
- Success and failed states are clear.
- Backend subscription status is checked after payment flow.
- Subscription creation remains owner-only and is never documented as a
  public API call.

# **7. Phase 3: Client Management**

## **Task 13: Create client model**

Type:

backend

Goal:

Create client collection.

Collection:

clients

Acceptance criteria:

- Client schema/model exists.

- agencyId is required.

- Client status enum is used.

- Indexes are added.

- Timestamps are enabled.

Module:

backend/src/modules/client/

## **Task 14: Create client validation schemas**

Type:

backend

Goal:

Validate client API inputs using Joi.

Acceptance criteria:

- Create client validation exists.

- Update client validation exists.

- Invalid email is rejected.

- Empty client name is rejected.

- Validation middleware is used in routes.

File:

backend/src/modules/client/client.validation.ts

## **Task 15: Create client API**

Type:

backend

Goal:

Allow agency users to create clients.

Suggested endpoint:

POST /api/clients

Acceptance criteria:

- Only agency users can create clients.

- Client is created under authenticated user’s agencyId.

- Frontend cannot manually decide agencyId.

- Joi validation is used.

- Activity log is created.

- Clear error responses exist.

## **Task 16: List clients API**

Type:

backend

Goal:

Allow agency users to list their own clients.

Suggested endpoint:

GET /api/clients

Acceptance criteria:

- Returns only clients where agencyId matches logged-in agency user.

- Supports basic search.

- Supports status filter.

- Does not return another agency’s clients.

## **Task 17: Client detail API**

Type:

backend

Goal:

Allow agency users to view one client.

Suggested endpoint:

GET /api/clients/:clientId

Acceptance criteria:

- Checks agencyId ownership.

- Returns client details.

- Returns basic report count.

- Returns basic lead count.

- Returns dashboard count if available.

- Returns 404 if client does not belong to agency.

## **Task 17A: Update client API**

Type:

backend

Goal:

Allow agency users to edit basic client information included in MVP scope.

Suggested endpoint:

PATCH /api/clients/:clientId

Acceptance criteria:

- Only agency users can update clients.

- Client must belong to authenticated user's agencyId.

- Frontend cannot manually change agencyId.

- Joi validation is used.

- Editable fields are limited to basic client information: name, businessType, contactName, contactEmail, phone, notes, and status.

- Activity log is created for important changes.

- Returns 404 if client does not belong to agency.

## **Task 17B: Create or link client user access API**

Type:

backend

Goal:

Create the minimum client portal access needed for the MVP client login flow.

Suggested endpoint:

POST /api/clients/:clientId/client-users

Acceptance criteria:

- Only agency users can create or link client user access.

- Client must belong to authenticated user's agencyId.

- Request accepts the client's email and optional name.

- Backend creates or links a local user/client_users record using Better Auth user identity when available.

- If the Better Auth user does not exist yet, a pending/invited client_users record can be created and linked during auth bootstrap.

- Client user is scoped to agencyId and clientId.

- Activity log is created.

- No email delivery system is required for MVP unless intentionally added later.

## **Task 18: Build clients frontend pages**

Type:

frontend

Pages:

/agency/clients

/agency/clients/new

/agency/clients/:clientId

Acceptance criteria:

- Agency can view clients list.

- Agency can add client.

- Agency can view client detail.

- Agency can edit basic client information.

- Agency can create or link client user access from client detail.

- Loading states exist.

- Empty states exist.

- Error states exist.

- UI follows UI_PAGES.md.

# **8. Phase 4: Report Upload and Processing**

## **Task 19: Create report upload model**

Type:

backend

Goal:

Create report upload collection.

Collection:

report_uploads

Acceptance criteria:

- Report upload schema/model exists.

- agencyId and clientId are required.

- Original file metadata is supported.

- Report source enum is used.

- Report type enum is used.

- Status enum is used.

- Processing error object is supported.

- Indexes are added.

Module:

backend/src/modules/report/

## **Task 20: Configure upload middleware**

Type:

backend

Goal:

Configure Multer for CSV upload.

Acceptance criteria:

- Only CSV files are allowed.

- File size limit exists.

- Upload errors are handled.

- Invalid file type is rejected.

- Upload middleware is reusable.

File:

backend/src/middlewares/upload.middleware.ts

## **Task 21: Configure Cloudflare R2 helper**

Type:

backend

Goal:

Create Cloudflare R2 upload helper.

Acceptance criteria:

- Cloudflare R2 is configured using environment variables.

- File upload function exists.

- Upload result returns file URL and storage object key.

- Errors are handled.

- Secrets are not exposed.

File:

backend/src/utils/storage.ts

## **Task 22: Create report upload API**

Type:

backend

Goal:

Allow agency users to upload Meta Ads CSV for a client.

Suggested endpoint:

POST /api/reports/upload

Acceptance criteria:

- Only agency users can upload reports.

- Client ownership is checked before processing.

- Only CSV files are accepted.

- Original file metadata is stored.

- Report upload record is created.

- Status starts as uploaded or processing.

- Activity log is created.

## **Task 23: Create CSV parser service**

Type:

backend

Goal:

Parse uploaded CSV files.

Acceptance criteria:

- CSV headers are extracted.

- CSV rows are extracted.

- Empty CSV is rejected.

- Parser errors are handled.

- Parsed output is passed to validation service.

Possible file:

backend/src/modules/report/reportParser.service.ts

If multiple report services exist, use:

backend/src/modules/report/service/reportParser.service.ts

## **Task 24: Create report validation service**

Type:

backend

Goal:

Validate required Meta Ads CSV columns.

Required MVP fields:

campaignName

spend

leads

impressions

clicks

Acceptance criteria:

- Missing columns are detected.

- Clear error message is returned.

- Failed report status is saved.

- Valid report continues processing.

## **Task 25: Create column mapping service**

Type:

backend

Goal:

Map common Meta Ads column names to standard fields.

Example mappings:

Campaign name → campaignName

Amount spent → spend

Results → leads

Impressions → impressions

Link clicks → clicks

Reach → reach

Acceptance criteria:

- Common Meta Ads column names are supported.

- Mapped columns are saved in report upload document.

- Unclear mappings produce clear error or warning.

- Gemini is not required for MVP column mapping.

## **Task 26: Create metrics calculator service**

Type:

backend

Goal:

Calculate report metrics using backend code.

Metrics:

totalSpend

totalLeads

costPerLead

impressions

clicks

ctr

cpc

campaignBreakdown

bestCampaign

worstCampaign

Acceptance criteria:

- Gemini is not used for calculations.

- Division by zero is handled.

- Numeric values are parsed safely.

- Campaign-wise breakdown is calculated.

- Totals are accurate.

- Unit tests are added for calculation logic.

## **Task 27: Create report metrics model**

Type:

backend

Goal:

Store calculated campaign metrics.

Collection:

report_metrics

Acceptance criteria:

- Report metrics schema/model exists.

- agencyId, clientId, and reportUploadId are required.

- Totals object is supported.

- Campaign breakdown array is supported.

- Indexes are added.

- reportUploadId is unique.

## **Task 28: Create Gemini summary service**

Type:

backend

Goal:

Generate client-friendly campaign summary from calculated metrics.

Module:

backend/src/modules/ai/

Acceptance criteria:

- Gemini API is called only from AI module/service.

- Gemini receives cleaned metrics, not raw secrets.

- Gemini generates summary, insights, and recommendations.

- AI failure does not break the whole report flow.

- Fallback summary exists.

- Gemini does not calculate final numbers.

## **Task 29: Create dashboard model**

Type:

backend

Goal:

Create dashboard collection.

Collection:

dashboards

Acceptance criteria:

- Dashboard schema/model exists.

- agencyId, clientId, reportUploadId, and reportMetricsId are required.

- Dashboard starts as draft.

- visibleToClient is false by default.

- AI summary object is supported.

- Agency notes are supported.

- Indexes are added.

## **Task 30: Generate dashboard draft after report processing**

Type:

backend

Goal:

Create a draft dashboard after successful report processing.

Acceptance criteria:

- Dashboard is created only after metrics are calculated.

- Dashboard status is draft.

- visibleToClient is false.

- AI summary is attached.

- Activity log is created.

- Client cannot see draft dashboard.

## **Task 31: Build upload report frontend page**

Type:

frontend

Page:

/agency/clients/:clientId/upload-report

Acceptance criteria:

- Agency can select report source.

- Agency can select report type.

- Agency can upload CSV.

- Upload progress/loading state exists.

- Upload errors are shown clearly.

- Successful upload redirects to report review page.

# **9. Phase 5: Dashboard Review and Publish**

## **Task 32: Create report review API**

Type:

backend

Goal:

Allow agency to review processed report and draft dashboard.

Suggested endpoint:

GET /api/reports/:reportId/review

Acceptance criteria:

- Only agency users can access.

- Report must belong to logged-in agency.

- Returns report upload info.

- Returns calculated metrics.

- Returns dashboard draft.

- Returns AI summary.

- Client users cannot access.

## **Task 33: Create dashboard update API**

Type:

backend

Goal:

Allow agency to update draft dashboard details.

Suggested endpoint:

PATCH /api/dashboards/:dashboardId

Acceptance criteria:

- Only agency users can update.

- Dashboard must belong to agency.

- Agency can update agency notes.

- Agency can update editable summary fields if allowed.

- Client users cannot update dashboard.

- Activity log is created if important changes happen.

## **Task 34: Create publish dashboard API**

Type:

backend

Goal:

Allow agency to publish dashboard to client.

Suggested endpoint:

POST /api/dashboards/:dashboardId/publish

Acceptance criteria:

- Only agency users can publish.

- Dashboard must belong to agency.

- Dashboard status becomes published.

- visibleToClient becomes true.

- publishedAt is saved.

- publishedBy is saved.

- Activity log is created.

- Client can now view dashboard.

## **Task 35: Build report review frontend page**

Type:

frontend

Page:

/agency/reports/:reportId/review

Acceptance criteria:

- Shows report status.

- Shows key metrics.

- Shows campaign breakdown.

- Shows charts.

- Shows Gemini summary.

- Shows agency notes.

- Shows draft/published badge.

- Agency can publish dashboard.

- Confirmation dialog appears before publish.

- Client users cannot access this page.

# **10. Phase 6: Client Portal**

## **Task 36: Create client dashboard API**

Type:

backend

Goal:

Allow client users to view their published dashboard.

Suggested endpoint:

GET /api/client/dashboard

Acceptance criteria:

- Only client users can access.

- Returns only dashboards assigned to logged-in client.

- Returns only published dashboards.

- Draft dashboards are not returned.

- Client cannot access another client’s dashboard.

## **Task 37: Create client report detail API**

Type:

backend

Goal:

Allow client to view one published report/dashboard.

Suggested endpoint:

GET /api/client/reports/:dashboardId

Acceptance criteria:

- Only client users can access.

- Dashboard must belong to client.

- Dashboard must be published.

- visibleToClient must be true.

- Unauthorized access returns 404 or 403.

## **Task 38: Build client dashboard frontend page**

Type:

frontend

Page:

/client/dashboard

Acceptance criteria:

- Client sees only published dashboard data.

- Shows key metrics.

- Shows simple AI summary.

- Shows agency notes.

- Shows lead summary if available.

- Empty state appears if no dashboard is published.

- Client cannot edit metrics.

## **Task 39: Build client report detail frontend page**

Type:

frontend

Page:

/client/reports/:dashboardId

Acceptance criteria:

- Client can view one published dashboard in detail.

- Shows metrics, charts, AI summary, and notes.

- Shows error state if dashboard is not found.

- Draft dashboard is not visible.

# **11. Phase 7: Lead Tracking**

## **Task 40: Create lead model**

Type:

backend

Goal:

Create leads collection.

Collection:

leads

Acceptance criteria:

- Lead schema/model exists.

- agencyId and clientId are required.

- Lead status enum is used.

- Contact fields are supported.

- Campaign source is supported.

- Follow-up date is supported.

- Indexes are added.

## **Task 41: Create lead status history model**

Type:

backend

Goal:

Track every lead status update.

Collection:

lead_status_history

Acceptance criteria:

- History schema/model exists.

- agencyId, clientId, and leadId are required.

- Old status and new status are stored.

- Updated by user is stored.

- Updated by role is stored.

- Timestamps are enabled.

## **Task 42: Create lead upload API**

Type:

backend

Goal:

Allow agency users to upload lead CSV for a client.

Suggested endpoint:

POST /api/leads/upload

Acceptance criteria:

- Only agency users can upload leads.

- Client ownership is checked.

- CSV file is validated.

- Leads are created under correct agency/client.

- Duplicates are handled reasonably.

- Lead CSV uploads do not create report_metrics or dashboard records.

- If an upload audit record is stored, use report_uploads with reportType lead_report and link imported leads through reportUploadId.

- Activity log is created.

## **Task 43: Create agency lead list API**

Type:

backend

Goal:

Allow agency users to view leads for a client.

Suggested endpoint:

GET /api/clients/:clientId/leads

Acceptance criteria:

- Only agency users can access.

- Client must belong to agency.

- Returns leads for selected client.

- Supports status filter.

- Supports search.

- Does not return another agency’s leads.

## **Task 44: Create agency lead summary API**

Type:

backend

Goal:

Return lead follow-up summary for agency.

Suggested endpoint:

GET /api/clients/:clientId/leads/summary

Acceptance criteria:

- Returns total leads.

- Returns count by status.

- Returns overdue follow-ups.

- Returns pending follow-ups.

- Checks agency ownership.

## **Task 45: Create client lead list API**

Type:

backend

Goal:

Allow client users to view their own leads.

Suggested endpoint:

GET /api/client/leads

Acceptance criteria:

- Only client users can access.

- Returns only leads where clientId matches logged-in client.

- Supports status filter.

- Supports search.

- Does not expose another client’s leads.

## **Task 46: Create client lead status update API**

Type:

backend

Goal:

Allow client users to update lead status, remarks, and follow-up date.

Suggested endpoint:

PATCH /api/client/leads/:leadId/status

Acceptance criteria:

- Only client users can access.

- Lead must belong to logged-in client.

- Client can update only:

  - status

  - remarks

  - followUpDate

- Backend sets lastUpdatedBy.

- Backend sets lastUpdatedByRole.

- Lead status history record is created.

- Activity log is created.

- Invalid status is rejected.

## **Task 47: Build agency leads frontend page**

Type:

frontend

Page:

/agency/clients/:clientId/leads

Acceptance criteria:

- Shows lead status summary.

- Shows leads table.

- Supports status filter.

- Supports search.

- Shows overdue follow-ups.

- Shows empty state if no leads exist.

## **Task 48: Build client leads frontend page**

Type:

frontend

Page:

/client/leads

Acceptance criteria:

- Client sees own leads only.

- Client can update status.

- Client can add remarks.

- Client can add follow-up date.

- Lead update success state exists.

- Lead update error state exists.

- Client cannot edit restricted fields.

# **12. Phase 8: Agency Dashboard**

## **Task 49: Create agency dashboard stats API**

Type:

backend

Goal:

Return stats for agency dashboard.

Suggested endpoint:

GET /api/agency/dashboard-stats

Acceptance criteria:

- Only agency users can access.

- Returns total clients.

- Returns total reports uploaded.

- Returns total dashboards published.

- Returns total leads uploaded.

- Returns reports waiting for review.

- Returns failed uploads.

- Returns only logged-in agency data.

## **Task 50: Create agency recent activity API**

Type:

backend

Goal:

Return recent agency activity.

Suggested endpoint:

GET /api/agency/recent-activity

Acceptance criteria:

- Only agency users can access.

- Returns only activity logs under logged-in agency.

- Supports limit.

- Does not return other agency activity.

## **Task 51: Build agency dashboard frontend page**

Type:

frontend

Page:

/agency/dashboard

Acceptance criteria:

- Shows stats cards.

- Shows setup checklist.

- Shows recent activity.

- Shows reports waiting for review.

- Shows failed uploads count.

- Has quick actions:

  - Add client

  - Upload report

  - View clients

# **13. Phase 9: Admin Panel**

Current week scope note:

Implement only the admin foundation in this phase until client, report,
lead, and failed-upload data actually exist.

This week the required scope is:

- admin_users
- admin access control
- agency list
- agency detail with basic owner and status context
- suspend/reactivate agency

Defer richer platform stats, failed upload reporting, and detailed admin
activity views until the related modules are implemented.

## **Task 52: Create admin user model**

Type:

backend

Goal:

Create admin user collection.

Collection:

admin_users

Acceptance criteria:

- Admin user schema/model exists.

- userId is unique.

- Admin role enum exists.

- Admin status enum exists.

- Indexes are added.

## **Task 53: Create admin stats API**

Type:

backend

Goal:

Return platform-level admin stats.

Suggested endpoint:

GET /api/admin/stats

Acceptance criteria:

- Only admin users can access.

- Returns total agencies.

- Returns active agencies.

- Returns suspended agencies.

- For the current week, client/report/dashboard/lead/failed-upload
  counts can be deferred until those modules exist.

## **Task 54: Create admin agencies API**

Type:

backend

Goal:

Allow admin to view all agencies.

Suggested endpoint:

GET /api/admin/agencies

Acceptance criteria:

- Only admin users can access.

- Returns list of agencies.

- Supports search.

- Supports status filter.

- Includes owner and status context.

- Usage counts can be added after client/report/lead modules exist.

## **Task 55: Create admin agency detail API**

Type:

backend

Goal:

Allow admin to view one agency.

Suggested endpoint:

GET /api/admin/agencies/:agencyId

Acceptance criteria:

- Only admin users can access.

- Returns agency profile.

- Returns owner info.

- Returns current agency status.

- Usage summary, recent activity, and failed uploads summary can be
  deferred until the related modules exist.

## **Task 56: Create suspend/reactivate agency API**

Type:

backend

Goal:

Allow admin to suspend or reactivate agency.

Suggested endpoint:

PATCH /api/admin/agencies/:agencyId/status

Acceptance criteria:

- Only admin users can access.

- Valid statuses are enforced.

- Suspended agency cannot access agency APIs.

- Reactivated agency can access again.

- Admin action is logged.

- Agency data is not deleted.

## **Task 57: Create failed uploads admin API**

Type:

backend

Goal:

Allow admin to view failed uploads.

Suggested endpoint:

GET /api/admin/reports/failed

Acceptance criteria:

- Defer this task until report upload and failed upload data exist.

## **Task 58: Create admin activity logs API**

Type:

backend

Goal:

Allow admin to view platform activity.

Suggested endpoint:

GET /api/admin/activity-logs

Acceptance criteria:

- Defer this task until the reusable activity log service is in place
  and other modules are writing meaningful activity records.

## **Task 59: Build admin dashboard frontend page**

Type:

frontend

Page:

/admin/dashboard

Acceptance criteria:

- For the current week, focus on a simple admin landing view that links
  to agencies and supports status management work.

- Platform stats, failed upload count, and recent activity can be added
  after the related backend data exists.

## **Task 60: Build admin agencies frontend pages**

Type:

frontend

Pages:

/admin/agencies

/admin/agencies/:agencyId

Acceptance criteria:

- Admin can view agencies list.

- Admin can open agency detail.

- Admin can suspend agency.

- Admin can reactivate agency.

- Confirmation dialog exists for status changes.

## **Task 61: Build admin failed uploads page**

Type:

frontend

Page:

/admin/failed-uploads

Acceptance criteria:

- Defer this page until report upload and failed upload data exist.

## **Task 62: Build admin activity logs page**

Type:

frontend

Page:

/admin/activity-logs

Acceptance criteria:

- Defer this page until the admin activity logs API exists and multiple
  modules are writing activity records.

# **14. Phase 10: Activity Logs**

## **Task 63: Create activity log model**

Type:

backend

Goal:

Create activity logs collection.

Collection:

activity_logs

Acceptance criteria:

- Activity log schema/model exists.

- Supports agencyId and clientId.

- Supports actor user and actor role.

- Supports action, entity type, entity ID, message, and metadata.

- Indexes are added.

## **Task 64: Create activity log service**

Type:

backend

Goal:

Create reusable logging service.

Acceptance criteria:

- Service can create activity logs.

- Service is used by key modules.

- Does not store secrets.

- Handles logging errors safely.

Log these actions:

agency_registered

subscription_created

subscription_activated

payment_failed

subscription_cancelled

client_created

report_uploaded

report_processing_failed

dashboard_draft_created

dashboard_published

lead_uploaded

lead_status_updated

client_viewed_dashboard

agency_suspended

agency_reactivated

admin_action

# **15. Phase 11: Frontend Layout and Shared Components**

## **Task 65: Build shared layout components**

Type:

frontend

Goal:

Create layout components used across app.

Components:

AgencyLayout

ClientLayout

AdminLayout

Sidebar

Topbar

UserMenu

Acceptance criteria:

- Agency layout works.

- Client layout works.

- Admin layout works.

- Navigation is role-specific.

- Layouts are responsive enough for MVP.

## **Task 66: Build shared UI components**

Type:

frontend

Goal:

Create reusable UI components.

Components:

PageHeader

StatsCard

DataTable

EmptyState

ErrorState

LoadingState

ConfirmDialog

StatusBadge

Acceptance criteria:

- Components are reusable.

- Components use shadcn/ui where useful.

- Components are typed with TypeScript.

- Components match project UI style.

## **Task 67: Configure Axios API client**

Type:

frontend

Goal:

Create reusable API client.

File:

frontend/src/lib/api.ts

Acceptance criteria:

- Axios instance exists.

- Base URL uses environment variable.

- Auth token is attached if needed.

- Common error handling exists.

- APIs can reuse the client.

# **16. Phase 12: Testing and Security**

## **Task 68: Add backend test setup**

Type:

backend

Goal:

Configure Jest and Supertest.

Acceptance criteria:

- Test command works.

- Basic health check test exists.

- Test environment is configured.

- Documentation added in README.

## **Task 69: Add permission tests**

Type:

backend

Goal:

Test the most important SaaS data isolation rules.

Must test:

- Agency A cannot view Agency B clients.

- Agency A cannot view Agency B reports.

- Agency A cannot view Agency B dashboards.

- Agency A cannot view Agency B leads.

- Client A cannot view Client B dashboard.

- Client A cannot view Client B leads.

- Client cannot view draft dashboard.

- Client cannot edit report metrics.

- Agency user cannot access admin routes.

- Client user cannot access admin routes.

## **Task 70: Add upload validation tests**

Type:

backend

Goal:

Test report upload edge cases.

Must test:

- Wrong file type is rejected.

- Empty CSV is rejected.

- Missing required columns are rejected.

- File too large is rejected.

- Report for another agency’s client is rejected.

- Failed upload does not create published dashboard.

## **Task 71: Add metrics calculation tests**

Type:

backend

Goal:

Test report metric calculations.

Must test:

- Total spend calculation.

- Total leads calculation.

- CPL calculation.

- CTR calculation.

- CPC calculation.

- Division by zero.

- Campaign breakdown calculation.

## **Task 72: Add lead update tests**

Type:

backend

Goal:

Test lead status update flow.

Must test:

- Client can update own lead status.

- Client cannot update another client’s lead.

- Client cannot update restricted fields.

- Status history is created.

- Activity log is created.

# **17. Phase 13: Production Deployment Hardening**

This phase is not the first deployment.

The first deployment happens in Phase 1A as private staging. Phase 13 is
for hardening the frontend, backend, environment variables, and release
process before production launch.

## **Task 73: Create frontend deployment config**

Type:

frontend

Goal:

Prepare frontend for production-ready Vercel deployment.

Acceptance criteria:

- Production build passes.

- Environment variables are documented.

- Vercel deployment instructions exist.

- Frontend uses deployed backend URL.

- Staging and production frontend environment variables are documented
  separately.

## **Task 74: Create backend deployment config**

Type:

backend

Goal:

Prepare backend for production-ready AWS deployment.

Recommended MVP option:

AWS EC2 with Nginx and PM2

Acceptance criteria:

- Production build works.

- Start script works.

- Environment variables are documented.

- Razorpay env variables are configured.

- CORS is configured for frontend domain.

- MongoDB on EC2 connection works.

- Cloudflare R2 env variables are configured.

- Gemini env variable is configured.

- Razorpay key ID, key secret, and webhook secret are configured.

- Staging and production backend environment variables are documented
  separately.

- Production CORS allows only approved production frontend domains.

## **Task 75: Create .env.example files**

Type:

frontend

backend

Goal:

Document required environment variables.

Frontend .env.example:

NEXT_PUBLIC_API_BASE_URL=

NEXT_PUBLIC_BETTER_AUTH_URL=

NEXT_PUBLIC_RAZORPAY_KEY_ID=

Backend .env.example:

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

Acceptance criteria:

- .env.example exists in both repos.

- No real secrets are committed.

- README explains how to configure env variables.

- Staging and production variable names are documented without real
  values.

# **18. Phase 14: MVP Demo and Launch Readiness**

## **Task 76: Complete end-to-end MVP demo flow**

Type:

fullstack

Goal:

Verify the complete MVP journey.

Demo flow:

1.  Agency registers.

2.  Agency activates Razorpay subscription or allowed trial.

3.  Agency dashboard opens.

4.  Agency adds client.

5.  Agency uploads Meta Ads CSV.

6.  System validates file.

7.  System calculates metrics.

8.  Gemini generates summary.

9.  Dashboard draft is created.

10. Agency reviews dashboard.

11. Agency publishes dashboard.

12. Client logs in.

13. Client views dashboard.

14. Agency uploads leads.

15. Client views leads.

16. Client updates lead status.

17. Agency sees updated lead progress.

18. Admin views platform activity.

Acceptance criteria:

- Full flow works without manual database editing.

- No developer-only workaround is needed.

- Permissions work correctly.

- Upload errors are understandable.

- Dashboard is not visible before publish.

- Lead status update creates history.

## **Task 77: MVP bug fixing sprint**

Type:

fullstack

Goal:

Fix critical bugs found during demo testing.

Priority order:

1.  Permission bugs

2.  Authentication bugs

3.  Report upload bugs

4.  Metrics calculation bugs

5.  Dashboard publish bugs

6.  Lead update bugs

7.  UI clarity bugs

Acceptance criteria:

- No known critical permission bug remains.

- No known critical upload bug remains.

- Full MVP demo flow works reliably.

## **Task 78: Prepare first user testing**

Type:

product

Goal:

Prepare for testing with real agencies or freelance marketers.

Acceptance criteria:

- Demo data is ready.

- Sample Meta Ads CSV is ready.

- Sample lead CSV is ready.

- Feedback questions are prepared.

- Known limitations are documented.

- Support contact process is defined.

# **19. Final Build Rule**

Build the MVP in this order:

1.  Project setup

2.  Initial private staging deployment

3.  Authentication and user roles

4.  Activity logs

5.  Admin foundation

6.  Client management, including client user access

7.  Report upload

8.  Metrics calculation

9.  Dashboard draft

10. Publish dashboard

11. Client portal

12. Lead tracking

13. Agency dashboard and shared frontend components

14. Razorpay subscription billing

15. Testing and production deployment hardening

Do not build extra features until the main MVP flow works end to end.
