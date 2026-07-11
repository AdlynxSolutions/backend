# **Authentication and Permissions**

## **1. Purpose**

This document defines authentication, roles, permissions, route access
rules, and data ownership rules for the MVP.

Developers and Codex must follow this file when creating:

- Auth middleware

- Role middleware

- Protected routes

- API permission checks

- Frontend route protection

- Admin access rules

This is a multi-tenant SaaS platform.

The most important rule is:

Agency A must never access Agency B’s data.  
Client A must never access Client B’s data.  
Agency users and client users must never access admin routes.

# **2. Authentication Provider**

The project uses:

Better Auth

Better Auth handles:

- Signup

- Login

- Logout

- Session management

- Password reset

- OAuth login if enabled later

Password handling should be delegated to Better Auth.

Do not store plaintext passwords or duplicate password fields in
application user documents.

# **3. Local User Records**

Even though Better Auth handles authentication, the backend still needs
application-specific fields on the canonical Better Auth user record.

The users collection stores the Better Auth-managed canonical user
record plus app-specific data:

id

name

email

emailVerified

image

role

status

lastLoginAt

createdAt

updatedAt

User roles:

agency_owner

agency_member

client_user

admin

User statuses:

active

inactive

suspended

# **4. Main User Types**

## **4.1 Public Visitor**

A public visitor is not logged in.

Can access:

- Landing page

- Login page

- Register page

Cannot access:

- Agency dashboard

- Client portal

- Admin panel

- Protected APIs

## **4.2 Agency Owner**

Agency owner is the main owner of an agency workspace.

Can:

- Access agency dashboard

- Manage agency clients

- Upload reports

- Upload leads

- Review dashboard drafts

- Publish dashboards

- View lead follow-up progress

- View agency activity

- Manage basic agency settings

Cannot:

- Access another agency’s data

- Access admin panel

- Access another client’s portal as a client user unless separately
  invited

- Edit internal platform settings

## **4.3 Agency Member**

Agency member is a team member inside an agency.

Can:

- Access agency dashboard

- View clients

- Upload reports

- Review dashboards

- View leads

- Update allowed agency-side data

Cannot:

- Access another agency’s data

- Access admin panel

- Access client-only routes unless separately invited

- Perform advanced owner-only actions if added later

For MVP, agency_owner and agency_member can have similar permissions.

Advanced agency team permissions are not required in MVP.

## **4.4 Client User**

Client user belongs to one client account.

Can:

- View published dashboards assigned to their client

- View leads assigned to their client

- Update lead status

- Add lead remarks

- Add follow-up date

Cannot:

- View draft dashboards

- Edit report metrics

- Upload campaign reports

- Publish dashboards

- Edit agency notes

- Access agency dashboard

- Access admin panel

- Access another client’s data

## **4.5 Admin User**

Admin user is an internal SaaS platform user.

Can:

- Access admin dashboard

- View all agencies

- View agency details

- View failed uploads

- View activity logs

- Suspend agencies

- Reactivate agencies

Cannot:

- Access environment variables from UI

- View secrets

- Casually modify campaign metrics

- Use agency/client routes as a normal tenant user without clear reason

Admin actions should be logged.

# **5. Subscription Access Rules**

The MVP uses Razorpay subscription billing for agency accounts.

Paid seats:

- agency_owner
- agency_member

Free users:

- client_user

Agency subscription statuses should be treated as:

Allowed to access paid agency features:

- active
- trialing

Restricted from paid agency features:

- pending
- inactive
- cancelled
- halted
- expired
- payment_failed

Rules:

- Agency users can access agency product routes only if their agency subscription is active or trialing.
- Agency users with inactive billing may access only billing/account recovery routes.
- Client users are not billed separately, but client portal access depends on the agency account being active or trialing.
- Backend must verify subscription status on protected agency/client APIs.
- Razorpay webhook requests must verify signature before updating subscription status.

Temporary internal staging note:

During the current internal development week, the team may temporarily
allow authenticated bootstrap-complete agency/admin development access
before Razorpay is implemented.

Use that temporary access only for internal staging and feature
development.

Final MVP and production behavior should still enforce billing or
allowed trial status before paid agency features and client portal
access are treated as fully available.

# **6. Role Constants**

Define roles in:

backend/src/config/constants/index.ts

Recommended constants:

export const USER_ROLES = {

AGENCY_OWNER: "agency_owner",

AGENCY_MEMBER: "agency_member",

CLIENT_USER: "client_user",

ADMIN: "admin",

} as const;

# **7. Backend Auth Flow**

## **6.1 Protected API Request Flow**

For protected APIs:

Frontend sends request

↓

Request includes Better Auth session/cookie

↓

Express auth.middleware.ts verifies Better Auth session

↓

Backend finds local user by Better Auth user ID

↓

Backend checks user status

↓

Backend attaches user context to request

↓

Route/controller/service checks role and ownership

↓

API returns only authorized data

## **6.2 Request User Context**

After successful authentication, backend should attach user context to
the request.

Example request context:

req.user = {

userId: "betterAuthUserId",

email: "user@example.com",

role: "agency_owner",

agencyId: "agencyObjectId",

clientId: null,

status: "active",

};

For client user:

req.user = {

userId: "betterAuthUserId",

email: "client@example.com",

role: "client_user",

agencyId: "agencyObjectId",

clientId: "clientObjectId",

status: "active",

};

For admin user:

req.user = {

userId: "betterAuthUserId",

email: "admin@example.com",

role: "admin",

agencyId: null,

clientId: null,

status: "active",

adminRole: "super_admin",

};

# **8. Backend Middleware**

## **7.1 auth.middleware.ts**

Path:

backend/src/middlewares/auth.middleware.ts

Responsibilities:

- Verify Better Auth session

- Find local user using Better Auth user ID

- Reject unauthenticated requests

- Reject inactive/suspended users

- Attach user context to request

Should reject request if:

- No auth token/session exists

- Better Auth session verification fails

- Local user does not exist

- User status is not active

- Required agency/client/admin relationship is missing

## **7.2 Role Check Middleware**

Role check middleware can be inside:

backend/src/middlewares/auth.middleware.ts

or as a separate file if needed later.

Example helper:

requireRole(\["agency_owner", "agency_member"\])

Use it to protect route groups.

## **7.3 Ownership Check Helpers**

Ownership checks can live inside service files or permission helpers.

Recommended helper examples:

ensureAgencyAccess(user, agencyId)

ensureClientAccess(user, clientId)

ensureDashboardAccess(user, dashboard)

ensureAdminAccess(user)

Important:

Do not rely only on route-level role checks.

Services must still query using agencyId and clientId.

# **9. Data Ownership Rules**

## **8.1 Agency-Owned Data**

Agency users can access only documents where:

document.agencyId === req.user.agencyId

This applies to:

- clients

- client_users

- report_uploads

- report_metrics

- dashboards

- leads

- lead_status_history

- activity_logs

Example safe agency query:

Client.findOne({

\_id: clientId,

agencyId: req.user.agencyId,

});

Unsafe query:

Client.findById(clientId);

## **8.2 Client-Owned Data**

Client users can access only documents where:

document.clientId === req.user.clientId

Client users should usually also be scoped by agencyId if available:

Dashboard.findOne({

\_id: dashboardId,

agencyId: req.user.agencyId,

clientId: req.user.clientId,

status: "published",

visibleToClient: true,

});

## **8.3 Admin Data Access**

Admin users can access cross-agency data only through admin routes.

Admin route access rule:

req.user.role === "admin"

Admin actions should create activity logs.

# **10. Route Access Rules**

## **9.1 Public Routes**

Public routes do not require authentication.

Frontend routes:

/

/login

/register

/pricing

Backend routes:

GET /api/health

Public routes should not expose private data.

## **9.2 Agency Routes**

Agency frontend routes for `agency_owner` and `agency_member`:

/agency/dashboard

/agency/billing

/agency/clients

/agency/clients/new

/agency/clients/:clientId

/agency/clients/:clientId/upload-report

/agency/reports/:reportId/review

/agency/clients/:clientId/leads

Agency users must have active agency membership.

Agency users can access only their own agency data.

Agency users can access paid agency features only when the agency subscription is active or trialing. If billing is inactive, allow only billing/account recovery routes.

During the current internal staging week, temporary pre-billing access
may be allowed after authenticated bootstrap to unblock agency/admin
development work. Do not keep that exception for final MVP or
production behavior.

For billing routes, agency owners can manage subscriptions. Agency members may view billing status only on /agency/billing and cannot create or change subscriptions.

Agency owner-only routes:

/agency/billing/success

/agency/billing/failed

`POST /api/billing/subscriptions/create`

Billing success and failed recovery pages are owner-only.

## **9.3 Client Routes**

Client frontend routes:

/client/dashboard

/client/leads

/client/reports/:dashboardId

Allowed role:

client_user

Client users must have active client membership.

Client users can access only their own client data.

Client portal access depends on the agency subscription being active or trialing.

## **9.4 Admin Routes**

Admin frontend routes:

/admin/dashboard

/admin/agencies

/admin/agencies/:agencyId

/admin/failed-uploads

/admin/activity-logs

Allowed role:

admin

Admin users must exist in admin_users and have active status.

Agency users and client users must never access admin routes.

# **11. API Permission Matrix**

## **11.1 Auth APIs**

| **API**                  | **Public** | **Agency** | **Client** | **Admin** |
|--------------------------|------------|------------|------------|-----------|
| POST /api/auth/bootstrap | No         | Yes        | Yes        | Yes       |
| GET /api/auth/me         | No         | Yes        | Yes        | Yes       |

Note: `POST /api/auth/bootstrap` is called after Better Auth signup/login with a valid Better Auth session. It is not an unauthenticated public backend endpoint.

## **11.2 Agency APIs**

| **API**                         | **Agency Owner** | **Agency Member** | **Client User** | **Admin** |
|---------------------------------|------------------|-------------------|-----------------|-----------|
| GET /api/agency/dashboard-stats | Yes              | Yes               | No              | No        |
| GET /api/agency/recent-activity | Yes              | Yes               | No              | No        |

## **11.3 Client Management APIs**

| **API**                      | **Agency Owner** | **Agency Member** | **Client User** | **Admin**        |
|------------------------------|------------------|-------------------|-----------------|------------------|
| POST /api/clients            | Yes              | Yes               | No              | No               |
| GET /api/clients             | Yes              | Yes               | No              | No               |
| GET /api/clients/:clientId   | Yes              | Yes               | No              | Admin route only |
| PATCH /api/clients/:clientId | Yes              | Yes               | No              | Admin route only |
| POST /api/clients/:clientId/client-users | Yes | Yes | No | No |

Important:

Agency users can access only clients under their own agency.

## **11.4 Report APIs**

| **API**                               | **Agency Owner** | **Agency Member** | **Client User** | **Admin**           |
|---------------------------------------|------------------|-------------------|-----------------|---------------------|
| POST /api/reports/upload              | Yes              | Yes               | No              | No                  |
| GET /api/reports/:reportId/review     | Yes              | Yes               | No              | No                  |

Important:

Client users cannot upload or review reports.

## **11.5 Dashboard APIs**

| **API**                                     | **Agency Owner** | **Agency Member** | **Client User** | **Admin** |
|---------------------------------------------|------------------|-------------------|-----------------|-----------|
| PATCH /api/dashboards/:dashboardId          | Yes              | Yes               | No              | No        |
| POST /api/dashboards/:dashboardId/publish   | Yes              | Yes               | No              | No        |
| GET /api/client/dashboard                   | No               | No                | Yes             | No        |
| GET /api/client/reports/:dashboardId        | No               | No                | Yes             | No        |

Important:

For MVP, the report review endpoint `GET /api/reports/:reportId/review` returns the dashboard draft and calculated metrics. Separate dashboard-by-report, reprocess, retry, and unpublish APIs are not included in MVP unless new tasks are intentionally added.

Client users can view only published dashboards where:

status === "published"

visibleToClient === true

clientId === req.user.clientId

## **11.6 Lead APIs**

| **API**                                  | **Agency Owner** | **Agency Member** | **Client User** | **Admin** |
|------------------------------------------|------------------|-------------------|-----------------|-----------|
| POST /api/leads/upload                   | Yes              | Yes               | No              | No        |
| GET /api/clients/:clientId/leads         | Yes              | Yes               | No              | No        |
| GET /api/clients/:clientId/leads/summary | Yes              | Yes               | No              | No        |
| GET /api/client/leads                    | No               | No                | Yes             | No        |
| PATCH /api/client/leads/:leadId/status   | No               | No                | Yes             | No        |

Client users can update only:

status

remarks

followUpDate

Client users cannot update:

agencyId

clientId

reportUploadId

sourceCampaign

campaign metrics

dashboard data

## **11.7 Billing APIs**

| **API**                                      | **Public** | **Agency Owner** | **Agency Member** | **Client User** | **Admin** |
|----------------------------------------------|------------|------------------|-------------------|-----------------|-----------|
| GET /api/billing/plans                       | Yes        | Yes              | Yes               | No              | No        |
| POST /api/billing/subscriptions/create       | No         | Yes              | No                | No              | No        |
| GET /api/billing/subscription/current        | No         | Yes              | Yes               | No              | No        |
| POST /api/billing/webhook/razorpay           | Signature verified webhook only | No | No | No | No |

Important:

- Only agency owners can create or change subscriptions in MVP.
- Agency members can view billing status if needed.
- Client users never manage billing.
- Razorpay webhook route does not use normal user auth, but it must verify Razorpay webhook signature.

## **11.8 Admin APIs**

| **API**                                    | **Agency Owner** | **Agency Member** | **Client User** | **Admin** |
|--------------------------------------------|------------------|-------------------|-----------------|-----------|
| GET /api/admin/stats                       | No               | No                | No              | Yes       |
| GET /api/admin/agencies                    | No               | No                | No              | Yes       |
| GET /api/admin/agencies/:agencyId          | No               | No                | No              | Yes       |
| PATCH /api/admin/agencies/:agencyId/status | No               | No                | No              | Yes       |
| GET /api/admin/reports/failed              | No               | No                | No              | Yes       |
| GET /api/admin/activity-logs               | No               | No                | No              | Yes       |

# **12. Frontend Route Protection**

Frontend should protect routes based on user role.

But frontend protection is only for user experience.

Backend protection is mandatory.

## **11.1 Public User**

If not logged in:

Can access:

/

/login

/register

/pricing

Should be redirected away from:

/agency/\*

/client/\*

/admin/\*

## **11.2 Agency User**

If logged in as agency user:

Allowed:

/agency/\*

Not allowed:

/client/\*

/admin/\*

Unless the same Better Auth user is intentionally linked as a client user
also, which is not required in MVP.

## **11.3 Client User**

If logged in as client user:

Allowed:

/client/\*

Not allowed:

/agency/\*

/admin/\*

## **11.4 Admin User**

If logged in as admin:

Allowed:

/admin/\*

Not allowed by default:

/agency/\*

/client/\*

Admin support impersonation is not included in MVP.

# **13. Dashboard Permission Rules**

## **12.1 Draft Dashboard**

Draft dashboard:

status: draft

visibleToClient: false

Can be viewed by:

agency_owner

agency_member

Cannot be viewed by:

client_user

## **12.2 Published Dashboard**

Published dashboard:

status: published

visibleToClient: true

Can be viewed by:

agency_owner

agency_member

client_user assigned to that client

## **12.3 Unpublished Dashboard**

Unpublished dashboard:

status: unpublished

visibleToClient: false

Can be viewed by:

agency_owner

agency_member

Cannot be viewed by:

client_user

# **14. Lead Permission Rules**

## **13.1 Agency Lead Access**

Agency users can:

- View all leads under their agency

- View leads by client

- View lead status summary

- Upload leads

- Export leads later if added

Agency users must query leads using:

Lead.find({

agencyId: req.user.agencyId,

clientId,

});

## **13.2 Client Lead Access**

Client users can:

- View their own client leads

- Update lead status

- Add remarks

- Add follow-up date

Client users must query leads using:

Lead.find({

agencyId: req.user.agencyId,

clientId: req.user.clientId,

});

## **13.3 Lead Update Rule**

When a client updates lead status:

1.  Check lead belongs to the client.

2.  Allow only permitted fields.

3.  Save lead update.

4.  Create lead_status_history record.

5.  Create activity_logs record.

Allowed update fields:

status

remarks

followUpDate

Rejected update fields:

agencyId

clientId

reportUploadId

sourceCampaign

createdAt

updatedAt

lastUpdatedByRole if sent by frontend

The backend should set lastUpdatedBy and lastUpdatedByRole.

# **15. Report Upload Permission Rules**

Only agency users can upload reports.

Before upload processing:

1.  Verify user is agency owner/member.

2.  Verify client belongs to user’s agency.

3.  Validate file.

4.  Create report upload record with correct agencyId and clientId.

Safe client check:

Client.findOne({

\_id: clientId,

agencyId: req.user.agencyId,

});

If client is not found, return forbidden or not found.

Do not allow frontend to decide agencyId.

The backend should always set agencyId from authenticated user context.

# **16. Admin Permission Rules**

Admin users are internal platform users.

Admin verification should check:

- User is authenticated with Better Auth

- Local user role is admin

- Admin record exists in admin_users

- Admin status is active

Admin actions that should be logged:

- View agency detail

- Suspend agency

- Reactivate agency

- View failed upload details

- Change agency status

- Razorpay subscription created

- Razorpay subscription activated

- Razorpay payment failed

- Razorpay subscription cancelled/halted

- Any future billing/plan changes

Admin users should not see or edit environment secrets.

# **17. Suspended Account Rules**

## **16.1 Suspended Agency**

If agency status is:

suspended

Agency users should not access agency APIs.

They may receive a response like:

Your agency account is suspended. Please contact support.

Suspending an agency should not delete data.

## **16.2 Suspended User**

If user status is:

suspended

User should not access protected APIs.

## **16.3 Inactive Client User**

If client user status is:

inactive

Client user should not access client portal APIs.

# **18. Error Responses**

Use consistent error responses.

## **17.1 Unauthenticated**

When no valid authentication exists:

{

"success": false,

"message": "Authentication required"

}

Status:

401 Unauthorized

## **17.2 Forbidden**

When user is logged in but not allowed:

{

"success": false,

"message": "You do not have permission to access this resource"

}

Status:

403 Forbidden

## **17.3 Not Found**

When resource does not exist or does not belong to the user:

{

"success": false,

"message": "Resource not found"

}

Status:

404 Not Found

For security, prefer 404 Not Found when exposing existence of another
tenant’s resource could leak information.

# **19. Security Rules**

## **18.1 Never Trust Frontend IDs**

Frontend may send wrong or malicious IDs.

Backend must verify every ID.

Bad:

ReportUpload.findById(reportId);

Good:

ReportUpload.findOne({

\_id: reportId,

agencyId: req.user.agencyId,

});

## **18.2 Never Expose Raw Files Publicly**

Uploaded report files should not be publicly accessible without
authorization.

If Cloudflare R2 URLs are used, avoid exposing raw file links to
unauthorized users.

## **18.3 Never Expose Secrets**

Never expose:

- Better Auth secret

- Gemini API key

- R2 access key secret

- MongoDB connection string

- AWS credentials

Secrets must stay in environment variables.

## **18.4 Never Let Client Edit Metrics**

Client users cannot modify:

- report metrics

- dashboard calculations

- campaign breakdown

- AI summary if not allowed

- agency notes

## **18.5 Never Auto-Publish AI Output**

Gemini-generated summaries must be reviewed by agency users before
publishing.

Dashboards must start in draft mode.

# **20. Activity Logging Rules**

Create activity logs for important actions.

Required logs:

agency_registered

subscription_created

subscription_activated

payment_failed

subscription_cancelled

client_created

client_updated

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

Activity log should include:

agencyId

clientId if relevant

actorUserId

actorRole

action

entityType

entityId

message

metadata

createdAt

Do not store secrets in activity logs.

# **21. MVP Permission Checklist**

Before launch, test these:

## **Agency Tests**

- Agency A cannot view Agency B clients.

- Agency A cannot view Agency B reports.

- Agency A cannot view Agency B dashboards.

- Agency A cannot view Agency B leads.

- Suspended agency cannot access dashboard APIs.

## **Client Tests**

- Client A cannot view Client B dashboard.

- Client A cannot view Client B leads.

- Client cannot view draft dashboard.

- Client cannot edit report metrics.

- Client can update lead status, remarks, and follow-up date.

- Lead status update creates history record.

## **Admin Tests**

- Agency user cannot access admin routes.

- Client user cannot access admin routes.

- Admin can view agencies.

- Admin can suspend agency.

- Suspended agency access is blocked.

- Admin action creates activity log.

## **Upload Tests**

- Agency can upload report only for its own client.

- Agency cannot upload report for another agency’s client.

- Failed upload does not create published dashboard.

# **22. Final Rule**

Authentication proves who the user is.

Authorization proves what the user is allowed to access.

For every protected API, check both:

1.  Is the user authenticated?

2.  Is the user allowed to access this exact resource?

Never skip ownership checks.
