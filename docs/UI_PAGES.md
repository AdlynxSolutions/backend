# **UI Pages**

## **Purpose**

This file defines the frontend pages required for the MVP.

For each page, it explains:

- Route

- User role

- Purpose

- Main sections

- Main actions

- API calls

- Access rule

Codex and frontend developers must follow this file when building pages.

# **1. Public Pages**

## **1.1 Landing Page**

Route:

/

User role:

public

Purpose:

Explain the product and encourage agencies to register.

Main sections:

Hero section

Problem section

How it works

Features

Call to action

FAQ

Pricing preview

Main actions:

Go to register

Go to pricing

Go to login

API calls:

GET /api/billing/plans

Access rule:

Publicly accessible

## **1.2 Pricing Page**

Route:

/pricing

User role:

public

Purpose:

Show simple Adlynx subscription pricing for agencies.

Main sections:

Starter plan: ₹499/month, includes 1 agency user

Team plan: ₹999/month, includes 2 agency users

Extra agency user note: ₹499/month per extra agency user

Client users free note

FAQ

Main actions:

Go to register

API calls:

GET /api/billing/plans

Access rule:

Publicly accessible.

## **1.3 Login Page**

Route:

/login

User role:

public

agency_owner

agency_member

client_user

admin

Purpose:

Allow users to log in.

Main sections:

Login form

Forgot password link

Register link

Main actions:

Login

Go to register

API calls:

Better Auth login

GET /api/auth/me

Access rule:

Public users can access.

Logged-in users should be redirected based on role.

Agency user → /agency/dashboard

Client user → /client/dashboard

Admin user → /admin/dashboard

## **1.4 Register Page**

Route:

/register

User role:

public

Purpose:

Allow a new agency owner to create an account.

Main sections:

Agency registration form

Plan selection summary

Login link

Main actions:

Create agency account

Go to login

API calls:

Better Auth signup

POST /api/auth/bootstrap

GET /api/billing/plans

After authenticated bootstrap:

POST /api/billing/subscriptions/create

Access rule:

Public users can access.

The page is public, but the billing create API call itself is not
public.

The public register page handles Better Auth signup only. After signup
or login, the client calls `POST /api/auth/bootstrap` with an
authenticated Better Auth session, then starts the owner-only billing
API flow using `POST /api/billing/subscriptions/create`.

Temporary internal staging note:

During the current internal development week, the team may temporarily
route the authenticated owner into agency setup/dashboard work after
`POST /api/auth/bootstrap` even before Razorpay is implemented.

The final MVP and production UX should still continue from signup/login
into the owner-only billing flow before the paid agency experience is
treated as fully available.

Logged-in users should be redirected based on role.

# **2. Agency Pages**

## **2.1 Agency Dashboard**

Route:

/agency/dashboard

User role:

agency_owner

agency_member

Purpose:

Show agency overview and important next actions.

Main sections:

Total clients

Total reports uploaded

Total dashboards published

Total leads uploaded

Reports waiting for review

Failed uploads

Recent activity

Subscription status card

Setup checklist

Main actions:

Add client

Upload report

View clients

Review dashboard drafts

API calls:

GET /api/agency/dashboard-stats

GET /api/agency/recent-activity

GET /api/billing/subscription/current

Access rule:

Only active agency users can access.

Agency users can see only their own agency data.

Temporary internal staging note:

For the current internal development week, bootstrap-complete
agency_owner and agency_member users may be allowed into this page
before billing is implemented so auth, admin foundation, and client
management work can move forward.

The final MVP and production rule remains active or trialing agency
access only.

## **2.2 Agency Billing Page**

Route:

/agency/billing

User role:

agency_owner

agency_member

Purpose:

Show the agency subscription plan. Agency owners can start or manage the subscription; agency members can view billing status only.

Main sections:

Current plan

Subscription status

Included agency users

Extra agency users

Plan cards

Main actions:

View billing status

Choose Starter (owner only)

Choose Team (owner only)

Open subscription flow (owner only)

API calls:

GET /api/billing/plans

GET /api/billing/subscription/current

POST /api/billing/subscriptions/create

Access rule:

Only active agency owners can manage billing. Active agency members may view billing status but cannot start or change subscriptions.

## **2.3 Billing Success Page**

Route:

/agency/billing/success

User role:

agency_owner

Purpose:

Show subscription setup success and next steps.

Main actions:

Go to agency dashboard

Add client

API calls:

GET /api/billing/subscription/current

Access rule:

Only active agency owners can access. Agency members can view billing status on /agency/billing but cannot access subscription success actions.

## **2.4 Billing Issue Page**

Route:

/agency/billing/failed

User role:

agency_owner

Purpose:

Show a clear billing issue message and help the agency retry.

Main actions:

Retry billing

Back to billing

API calls:

GET /api/billing/subscription/current

POST /api/billing/subscriptions/create

Access rule:

Only active agency owners can access. Agency members can view billing status on /agency/billing but cannot retry subscription setup.

## **2.5 Clients List Page**

Route:

/agency/clients

User role:

agency_owner

agency_member

Purpose:

Show all clients under the logged-in agency.

Main sections:

Clients table

Search

Status filter

Add client button

Main actions:

Add client

View client

Edit client

API calls:

GET /api/clients

Access rule:

Only agency users can access.

Agency can see only clients where agencyId matches logged-in user
agencyId.

## **2.6 Add Client Page**

Route:

/agency/clients/new

User role:

agency_owner

agency_member

Purpose:

Allow agency users to add a new client.

Main sections:

Client form

Cancel button

Save button

Main actions:

Create client

Cancel

API calls:

POST /api/clients

Access rule:

Only agency users can access.

New client must be created under logged-in user's agencyId.

## **2.7 Client Detail Page**

Route:

/agency/clients/:clientId

User role:

agency_owner

agency_member

Purpose:

Show one client's details, reports, dashboards, leads, and activity.

Main sections:

Client profile

Report summary

Dashboard summary

Lead summary

Recent activity

Quick actions

Main actions:

Upload report

Upload leads

View leads

View dashboard

Edit client

Manage client access

Create/link client user access

API calls:

GET /api/clients/:clientId

PATCH /api/clients/:clientId

POST /api/clients/:clientId/client-users

GET /api/clients/:clientId/leads/summary

Access rule:

Only agency users can access.

Client must belong to logged-in user's agency.

## **2.8 Upload Report Page**

Route:

/agency/clients/:clientId/upload-report

User role:

agency_owner

agency_member

Purpose:

Allow agency users to upload a Meta Ads CSV report for a client.

Main sections:

Client name

Report source selector

Report type selector

CSV upload area

Upload instructions

Sample format guidance

Main actions:

Upload CSV

Cancel

API calls:

POST /api/reports/upload

Access rule:

Only agency users can access.

Report can be uploaded only for a client belonging to the logged-in
agency.

Only CSV files are supported in MVP.

## **2.9 Report Review Page**

Route:

/agency/reports/:reportId/review

User role:

agency_owner

agency_member

Purpose:

Allow agency users to review generated dashboard draft before publishing
it to the client.

Main sections:

Report status

Key metrics

Campaign breakdown

Charts

Gemini summary

Agency notes

Dashboard preview

Publish controls

Main actions:

Save draft

Edit agency notes

Publish dashboard

Go back to client

API calls:

GET /api/reports/:reportId/review

PATCH /api/dashboards/:dashboardId

POST /api/dashboards/:dashboardId/publish

Access rule:

Only agency users can access.

Report must belong to logged-in user's agency.

Client users cannot access draft dashboards.

## **2.10 Agency Leads Page**

Route:

/agency/clients/:clientId/leads

User role:

agency_owner

agency_member

Purpose:

Allow agency users to monitor client lead follow-up progress.

Main sections:

Lead status summary

Follow-up summary

Leads table

Status filter

Campaign filter

Search

Main actions:

Upload leads

View lead details

Filter leads

API calls:

GET /api/clients/:clientId/leads

GET /api/clients/:clientId/leads/summary

Access rule:

Only agency users can access.

Agency can see only leads under its own agency and selected client.

# **3. Client Pages**

## **3.1 Client Dashboard**

Route:

/client/dashboard

User role:

client_user

Purpose:

Show campaign performance in simple client-friendly language.

Main sections:

Total spend

Total leads

Cost per lead

Impressions

Clicks

Campaign performance

Gemini summary

Agency notes

Lead status summary

Main actions:

View leads

View report details

API calls:

GET /api/client/dashboard

Access rule:

Only active client users can access.

Client can view only published dashboards assigned to their clientId.

Client cannot view draft dashboards.

Client cannot edit report metrics.

## **3.2 Client Leads Page**

Route:

/client/leads

User role:

client_user

Purpose:

Allow client users to update lead status, remarks, and follow-up date.

Main sections:

Lead status summary

Leads table

Filters

Lead update form/modal

Main actions:

Update lead status

Add remarks

Add follow-up date

Filter leads

API calls:

GET /api/client/leads

PATCH /api/client/leads/:leadId/status

Access rule:

Only active client users can access.

Client can access only leads assigned to their clientId.

Client can update only status, remarks, and followUpDate.

Client cannot edit report metrics or campaign data.

## **3.3 Client Report Detail Page**

Route:

/client/reports/:dashboardId

User role:

client_user

Purpose:

Allow client users to view one published dashboard/report in detail.

Main sections:

Report title

Date range

Key metrics

Campaign breakdown

Charts

Gemini summary

Agency notes

Lead summary

Main actions:

View leads

Go back to dashboard

API calls:

GET /api/client/reports/:dashboardId

Access rule:

Only active client users can access.

Dashboard must be published.

visibleToClient must be true.

dashboard.clientId must match logged-in client user's clientId.

# **4. Admin Pages**

## **4.1 Admin Dashboard**

Route:

/admin/dashboard

User role:

admin

Purpose:

Show platform-level overview for internal SaaS admins.

Main sections:

Total agencies

Active agencies

Suspended agencies

Total clients

Total reports uploaded

Total dashboards published

Failed uploads

Recent activity

Main actions:

View agencies

View failed uploads

View activity logs

API calls:

GET /api/admin/stats

GET /api/admin/activity-logs?limit=10

GET /api/admin/reports/failed?limit=10

Access rule:

Only active admin users can access.

Agency users and client users cannot access admin pages.

## **4.2 Admin Agencies Page**

Route:

/admin/agencies

User role:

admin

Purpose:

Allow admin users to view all agencies.

Main sections:

Agencies table

Search

Status filter

Main actions:

View agency

Suspend agency

Reactivate agency

API calls:

GET /api/admin/agencies

PATCH /api/admin/agencies/:agencyId/status

Access rule:

Only active admin users can access.

## **4.3 Admin Agency Detail Page**

Route:

/admin/agencies/:agencyId

User role:

admin

Purpose:

Show one agency's details for support and monitoring.

Main sections:

Agency profile

Owner details

Usage summary

Clients summary

Report upload summary

Failed uploads

Recent activity

Account status controls

Main actions:

Suspend agency

Reactivate agency

View failed uploads

View activity logs

API calls:

GET /api/admin/agencies/:agencyId

PATCH /api/admin/agencies/:agencyId/status

GET /api/admin/activity-logs?agencyId=:agencyId

Access rule:

Only active admin users can access.

Suspending an agency must not delete its data.

## **4.4 Admin Failed Uploads Page**

Route:

/admin/failed-uploads

User role:

admin

Purpose:

Allow admin users to monitor failed report uploads.

Main sections:

Failed uploads table

Error reason

Agency name

Client name

File name

Created date

Main actions:

View error

View agency

API calls:

GET /api/admin/reports/failed

Access rule:

Only active admin users can access.

## **4.5 Admin Activity Logs Page**

Route:

/admin/activity-logs

User role:

admin

Purpose:

Show important platform activity for support and debugging.

Main sections:

Activity logs table

Agency filter

Client filter

Action filter

Date filter

Main actions:

Filter logs

View related agency

View related client if needed

API calls:

GET /api/admin/activity-logs

Access rule:

Only active admin users can access.

# **5. MVP Page Build Order**

Build pages in this order:

1. /

2. /pricing

3. /login

4. /register

5. /agency/dashboard

6. /agency/billing

7. /agency/billing/success

8. /agency/billing/failed

9. /agency/clients

10. /agency/clients/new

11. /agency/clients/:clientId

12. /agency/clients/:clientId/upload-report

13. /agency/reports/:reportId/review

14. /client/dashboard

15. /client/leads

16. /client/reports/:dashboardId

17. /agency/clients/:clientId/leads

18. /admin/dashboard

19. /admin/agencies

20. /admin/agencies/:agencyId

21. /admin/failed-uploads

22. /admin/activity-logs

# **6. Common Empty States**

## **No clients**

You have not added any clients yet. Add your first client to start
creating dashboards.

Action:

Add Client

## **No reports**

No reports uploaded for this client yet. Upload a Meta Ads CSV to
generate a dashboard.

Action:

Upload Report

## **No leads**

No leads uploaded yet. Upload lead data to start tracking follow-ups.

Action:

Upload Leads

## **No published dashboard**

No published dashboard is available yet. Your agency will publish a
report when it is ready.

## **No failed uploads**

No failed uploads found.

# **7. Common Error States**

## **Subscription inactive**

Your agency subscription is not active. Please open Billing to continue.

Action:

Go to Billing

## **Unauthorized**

You do not have permission to view this page.

## **Upload failed**

We could not process this file. Please check that it is a valid Meta Ads
CSV report.

## **Missing columns**

This file is missing required columns. Please upload a CSV with campaign
name, spend, leads, impressions, and clicks.

## **Dashboard not found**

Dashboard not found or you do not have permission to view it.

## **Lead update failed**

Lead status could not be updated. Please try again.

# **8. Codex Rules**

When building any page, Codex must check:

1\. Which role can access this page?

2\. What is the purpose of this page?

3\. What sections should be shown?

4\. What actions are allowed?

5\. What API calls are needed?

6\. What loading state is needed?

7\. What empty state is needed?

8\. What error state is needed?

9\. Does this page expose another agency or client's data?

10\. Does this page match the MVP scope?

# **9. Final Rule**

Do not build extra frontend pages unless they support this MVP flow:

Agency registers  
→ Agency activates Razorpay subscription or allowed trial  
→ Agency dashboard opens  
→ Agency adds client  
→ Agency uploads Meta Ads CSV  
→ System validates and processes report  
→ System calculates metrics  
→ Gemini generates summary  
→ Dashboard draft is created  
→ Agency reviews dashboard draft  
→ Agency publishes dashboard  
→ Client logs in  
→ Client views dashboard  
→ Agency uploads leads  
→ Client views leads  
→ Client updates lead status  
→ Agency tracks updated lead progress
