## **Project Overview**

This project is a SaaS platform for digital marketing agencies and
freelance marketers.

Agencies upload campaign reports and lead data from platforms like Meta
Ads. The platform converts messy ad reports into clean client-ready
dashboards. Clients can view campaign performance and update lead
status, remarks, and follow-up dates in one place.

## **Product Positioning**

Upload messy ad reports.  
Turn them into clean client dashboards.  
Track every lead follow-up.  
Prove campaign performance with clarity.

## **Core Product Flow**

Agency registers  
→ Agency creates workspace  
→ Agency activates Razorpay subscription or allowed trial  
→ Agency adds client  
→ Agency uploads Meta Ads campaign report CSV  
→ System validates the uploaded file  
→ System maps columns  
→ Backend calculates metrics  
→ Gemini generates a client-friendly summary  
→ Dashboard draft is created  
→ Agency reviews and edits dashboard  
→ Agency publishes dashboard  
→ Client gets access  
→ Client views campaign performance  
→ Client views leads  
→ Client updates lead status, remarks, and follow-up date  
→ Agency tracks lead follow-up progress

## **Main User Types**

### **Public Visitor**

A person visiting the landing page before signing up.

### **Agency Owner**

The main user who owns an agency workspace.

Agency owner can:

- Register agency

- Manage clients

- Upload reports

- Review dashboard drafts

- Publish dashboards

- View lead follow-up progress

- Manage agency settings

### **Agency Member**

A team member inside the agency.

Agency member can:

- View agency dashboard

- Manage clients if allowed

- Upload reports

- Review reports

- View leads

### **Client User**

A client of the agency.

Client user can:

- View published dashboards

- View campaign performance

- View assigned leads

- Update lead status

- Add remarks

- Add follow-up date

Client user cannot:

- Edit report metrics

- Upload campaign reports

- Publish dashboards

- Access other clients’ data

### **Admin User**

An internal platform admin.

Admin user can:

- View all agencies

- View platform stats

- Monitor failed uploads

- View activity logs

- Suspend or reactivate agencies

Admin user is not the same as an agency user or client user.

## **Confirmed Tech Stack**

### **Frontend**

- Next.js App Router

- TypeScript

- Tailwind CSS

- shadcn/ui

- Recharts

- Axios

### **Backend**

- Node.js

- Express.js

- TypeScript

- Joi for request validation

- Mongoose for MongoDB models

- Better Auth authentication

- Razorpay subscriptions

### **Database**

- MongoDB hosted on AWS EC2

### **AI**

- Gemini API

### **File Upload**

- Multer for receiving files

- Cloudflare R2 for storing uploaded files

### **Billing**

- Razorpay Subscriptions
- Currency: INR (₹)
- Starter: ₹499/month, includes 1 agency user
- Team: ₹999/month, includes 2 agency users
- Extra agency user: ₹499/month per extra agency user
- Client users are free

### **Deployment**

- Frontend: Vercel

- Backend: AWS EC2

- Database: MongoDB hosted on AWS EC2

### **Early Staging Deployment**

- Start private staging deployment during the initial setup phase.

- Deploy the frontend skeleton to Vercel staging.

- Deploy the backend health-check API to EC2 staging.

- Use a separate MongoDB staging instance on EC2.

- Keep local, staging, and production environment variables separate.

- Do not hardcode localhost or staging URLs in application code.

- Update `.env.example` whenever a new required environment variable is
  introduced.

- Production launch should happen only after the full MVP demo flow is
  verified end to end.

## **MVP Scope**

The MVP should include:

- Agency registration

- Agency login

- Agency dashboard

- Razorpay subscription billing

- Pricing page

- Agency billing page

- Add client

- Upload Meta Ads campaign CSV

- Upload lead CSV

- Validate uploaded files

- Calculate campaign metrics

- Generate Gemini summary

- Create dashboard draft

- Agency review and publish flow

- Client dashboard

- Client lead status update

- Basic admin dashboard

- Failed upload monitoring

## **Not Included in MVP**

Do not build these in the MVP:

- Meta Ads API integration

- Google Ads API integration

- WhatsApp integration

- Mobile app

- Advanced white-labeling

- Advanced team permissions

- Advanced dashboard builder

- Automated scheduled reporting

- Multi-platform report support

- Advanced billing features beyond the simple MVP subscription

The MVP should prove one workflow deeply:

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

## **Development Rules**

- Do not modify unrelated files.

- Keep changes small and focused.

- Follow the existing folder structure.

- Do not create large all-in-one pull requests.

- Build one feature at a time.

- Add validation for all backend inputs.

- Add error handling for all APIs.

- Use clear and consistent naming.

- Prefer readable code over clever code.

- Do not hardcode secrets.

- Do not commit .env files.

- Keep frontend, backend, and docs organized.

- Keep code deployment-ready from the start by using environment
  variables for frontend and backend URLs.

- Mention deployment or environment assumptions when changing frontend
  API configuration, backend CORS, auth setup, billing webhooks, or file
  upload configuration.

## **Frontend Rules**

- Use Next.js with TypeScript.

- Use reusable components where useful.

- Use clear loading states.

- Use empty states for pages with no data.

- Use clear error messages.

- Protect pages based on user role.

- Client dashboard should be simple and non-technical.

- Agency dashboard can show more detailed information.

- Admin panel should be functional before being fancy.

## **Backend Rules**

- Use Express.js for backend APIs.

- Use Joi for request validation.

- Use Mongoose for MongoDB models.

- Use middleware for authentication.

- Use middleware or helper functions for role checks.

- Keep business logic inside services where possible.

- Controllers should not become too large.

- Do not call Gemini directly from controllers.

- Use separate services for report parsing, metrics calculation, Gemini
  summary, permissions, and activity logs.

## **MongoDB Rules**

Every important tenant-owned document must include:

- agencyId

Every client-specific document must include:

- agencyId

- clientId

Important collections should include timestamps.

Use indexes for common queries.

Recommended collections:

- users

- agencies

- agency_members

- clients

- client_users

- report_uploads

- report_metrics

- dashboards

- leads

- lead_status_history

- activity_logs

- admin_users
- subscriptions
- billing_events

## **Billing Rules**

- Agency users are paid seats.
- Client users are free.
- Backend must verify Razorpay webhook signatures.
- Agency product access requires subscription status active or trialing.
- If subscription is pending, inactive, cancelled, halted, expired, or payment_failed, redirect agency users to billing/account recovery pages.

## **Permission Rules**

Very important:

Agency A must never access Agency B’s data.

Client A must never access Client B’s data.

Admin routes must never be accessible by agency users or client users.

### **Agency Rules**

Agency users can access only data where:

- document.agencyId matches logged-in user’s agencyId

### **Client Rules**

Client users can access only data where:

- document.clientId matches logged-in client user’s clientId

Client users can update only:

- lead status

- lead remarks

- follow-up date

Client users cannot update:

- campaign metrics

- report data

- dashboard calculations

- agency notes

- raw uploaded reports

### **Admin Rules**

Admin users can access admin routes only.

Admin actions should be logged.

Sensitive admin actions should require confirmation.

## **Report Upload Rules**

For MVP, support only these CSV uploads:

- Meta Ads campaign CSV

- Lead CSV

Meta Ads campaign report upload flow:

Agency selects client  
→ Agency uploads campaign CSV  
→ Backend validates file type  
→ Backend checks required columns  
→ Backend maps columns  
→ Backend calculates metrics  
→ Gemini generates summary  
→ Dashboard draft is created  
→ Agency reviews  
→ Agency publishes

Lead CSV upload flow:

Agency selects client  
→ Agency uploads lead CSV  
→ Backend validates file type  
→ Backend checks required lead columns  
→ Backend imports leads under the agency and client  
→ Activity log is created

Lead CSV uploads do not create campaign metrics or dashboard drafts.

If validation fails:

- Do not create published dashboard

- Store upload status as failed

- Show clear error message

- Allow agency to re-upload

## **Metrics Calculation Rules**

Backend code must calculate all final numbers.

Backend should calculate:

- Total spend

- Total leads

- Cost per lead

- Impressions

- Clicks

- CTR

- CPC

- Campaign-wise breakdown

Gemini must not calculate final financial or campaign numbers.

Gemini can only explain already calculated metrics.

## **Gemini AI Rules**

Gemini can be used for:

- Client-friendly campaign summary

- Performance explanation

- Simple insights

- Suggested next steps

- Column mapping suggestions

Gemini must not be used for:

- Final spend calculation

- Final lead count

- Final CPL calculation

- Final CTR calculation

- Permission decisions

- Billing decisions

- Auto-publishing dashboards

Main rule:

Backend calculates numbers.  
Gemini explains numbers.

## **Dashboard Rules**

Dashboards must start in draft mode.

Client users must not see draft dashboards.

Agency users must review dashboards before publishing.

Dashboard statuses:

- draft

- published

- unpublished

When dashboard is published:

- status becomes published

- visibleToClient becomes true

- publishedAt is saved

- activity log is created

## **Lead Tracking Rules**

Lead statuses:

- New

- Contacted

- Interested

- Not Interested

- Follow-up Required

- Converted

- Invalid

- Not Reachable

Client users can update:

- status

- remarks

- followUpDate

Every status change should create a lead status history record.

Agency should be able to see:

- Total leads

- Contacted leads

- Interested leads

- Converted leads

- Not contacted leads

- Follow-up required leads

- Overdue follow-ups

## **Admin Panel Rules**

Admin panel is for internal SaaS team only.

MVP admin panel should include:

- Admin dashboard

- View all agencies

- View agency details

- View failed uploads

- View activity logs

- Suspend agency

- Reactivate agency

Admin panel should not be overbuilt in MVP.

For the current internal development week, start with admin
foundation work only:

- admin_users

- admin access control

- agency list and basic detail

- suspend/reactivate agency

Add failed uploads, richer activity views, and broader platform stats
after the related client/report/lead modules exist.

## **Testing Rules**

Important things to test:

- Agency A cannot see Agency B clients

- Agency A cannot see Agency B reports

- Client A cannot see Client B dashboard

- Client cannot view draft dashboard

- Client cannot edit report metrics

- Wrong file type is rejected

- Empty CSV is rejected

- Missing required columns are rejected

- Dashboard starts as draft

- Published dashboard is visible to client

- Lead status update creates history record

## **Codex Instructions**

Before writing code, read:

- AGENTS.md

- PRD.md

- MVP_SCOPE.md

- USER_FLOWS.md

- TECH_STACK.md

- MONGODB_SCHEMA.md

- AUTH_PERMISSIONS.md

- UI_PAGES.md

- TASKS.md

When implementing a task:

1.  Understand the related docs first.

2.  Work on only one task at a time.

3.  Do not add features outside the task.

4.  Do not modify unrelated files.

5.  Add validation where required.

6.  Add permission checks where required.

7.  Add clear error handling.

8.  Keep code readable.

9.  Mention any assumptions.

10. Mention any files changed.

## **First MVP Goal**

Current internal development note:

For this week, internal staging may temporarily allow post-bootstrap
agency/admin development access before Razorpay is implemented.

That temporary mode is only to unblock internal build work. The final
MVP target flow below still assumes billing or allowed trial activation
before the agency dashboard is treated as fully available.

The first working demo should prove this flow:

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

This flow is more important than adding many features.
