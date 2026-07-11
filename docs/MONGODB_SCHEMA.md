# **MongoDB Schema**

## **1. Purpose**

This document defines the MongoDB collections, fields, relationships,
indexes, and data rules for the MVP.

Developers and Codex must follow this schema when creating Mongoose
models.

The product is a multi-tenant SaaS platform. Data isolation is critical.

Main rule:

Agency A must never access Agency B’s data.  
Client A must never access Client B’s data.

# **2. Database Design Principles**

## **2.1 Use Separate Collections**

Do not store the whole product inside one large document.

Use separate collections for:

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

Optional:

- ai_summaries

## **2.2 Use References Instead of Deep Nesting**

Use ObjectId references between tenant/business entities and Better
Auth string IDs for user-linked references.

Example:

A client document should store:

agencyId

A report document should store:

agencyId

clientId

uploadedBy

A lead document should store:

agencyId

clientId

reportUploadId

Do not deeply embed all clients, reports, dashboards, and leads inside
the agency document.

## **2.3 Required Tenant Fields**

Every agency-owned document must include:

agencyId

Every client-specific document must include:

agencyId

clientId

This applies to:

- clients

- client_users

- report_uploads

- report_metrics

- dashboards

- leads

- lead_status_history

- activity_logs where relevant

## **2.4 Better Auth Rule**

The project uses Better Auth for authentication.

Use the Better Auth user ID as the primary application user identity.

Password handling should be delegated to Better Auth.

Do not store plaintext passwords or duplicate password fields in
application user documents.

The users collection is used for:

- internal user profile

- role

- agency/client relationship

- app-specific status

- createdAt/updatedAt

# **3. Common Field Rules**

## **3.1 Object IDs**

Use MongoDB ObjectId for tenant/business references:

agencyId

clientId

reportUploadId

dashboardId

leadId

Use Better Auth string IDs for application user references:

id

ownerUserId

userId

createdBy

uploadedBy

publishedBy

lastUpdatedBy

updatedBy

actorUserId

## **3.2 Timestamps**

All main collections should use timestamps:

createdAt

updatedAt

In Mongoose, enable:

timestamps: true

## **3.3 Soft Status Instead of Hard Delete**

For important business data, prefer status fields instead of deleting
immediately.

Example:

active

inactive

suspended

archived

This helps with audit history and support.

# **4. Enums and Constants**

Define these in:

backend/src/config/constants/index.ts

## **4.1 User Roles**

agency_owner

agency_member

client_user

admin

## **4.2 Agency Status**

active

suspended

inactive

trial

## **4.3 Subscription Status**

trialing

active

pending

inactive

cancelled

halted

expired

payment_failed

## **4.4 Client Status**

draft

invited

active

inactive

archived

## **4.5 Report Source**

meta_ads

Future sources:

google_ads

google_analytics

linkedin_ads

manual

Only meta_ads is required for MVP.

## **4.6 Report Type**

campaign_report

lead_report

## **4.7 Report Upload Status**

uploaded

processing

failed

draft_ready

published

unpublished

## **4.8 Dashboard Status**

draft

published

unpublished

archived

## **4.9 Lead Status**

New

Contacted

Interested

Not Interested

Follow-up Required

Converted

Invalid

Not Reachable

## **4.10 Activity Log Actions**

agency_registered

agency_suspended

agency_reactivated

client_created

client_updated

report_uploaded

report_processing_started

report_processing_failed

report_processing_completed

dashboard_draft_created

dashboard_published

lead_uploaded

lead_status_updated

client_viewed_dashboard

subscription_created

subscription_activated

payment_failed

subscription_cancelled

admin_action

# **5. Collections Overview**

Required MVP collections:

users

agencies

agency_members

clients

client_users

report_uploads

report_metrics

dashboards

leads

lead_status_history

activity_logs

admin_users

subscriptions

billing_events

Better Auth-managed auth collections for sessions, accounts, and
verifications

Optional MVP collection:

ai_summaries

# **6. Collection: users**

## **Purpose**

Stores the Better Auth-managed user profile plus app-specific role and
status fields used by the application.

## **Fields**

id: string

name: string

email: string

emailVerified: boolean

image: string | null

role: agency_owner \| agency_member \| client_user \| admin

status: active \| inactive \| suspended

lastLoginAt: Date

createdAt: Date

updatedAt: Date

## **Notes**

A user can be connected to an agency through agency_members.

A client user can be connected to a client through client_users.

Admin user details can be stored separately in admin_users.

## **Required Fields**

id

email

role

status

## **Indexes**

id unique

email

role

status

# **7. Collection: agencies**

## **Purpose**

Stores agency workspace details.

Each agency is a tenant in the SaaS.

## **Fields**

\_id: ObjectId

name: string

slug: string

ownerUserId: string

status: active \| suspended \| inactive \| trial

plan: string

subscriptionId: ObjectId

subscriptionStatus: trialing | active | pending | inactive | cancelled | halted | expired | payment_failed

billingProvider: razorpay

includedAgencySeats: number

extraAgencySeats: number

trialEndsAt: Date

settings: object

createdAt: Date

updatedAt: Date

## **Example settings**

settings: {

currency: "INR",

timezone: "Asia/Kolkata",

defaultReportSource: "meta_ads"

}

## **Required Fields**

name

ownerUserId

status

## **Indexes**

slug unique

ownerUserId

status

createdAt

## **Important Rules**

- Suspending an agency should not delete its data.

- Suspended agencies should not be able to access agency dashboard APIs.

- Admin can reactivate a suspended agency.

- Agency paid feature access requires subscriptionStatus to be active or trialing.

- Client portal access depends on the related agency subscription being active or trialing.

# **8. Collection: agency_members**

## **Purpose**

Connects users to agency workspaces.

This allows one agency to have multiple users.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

userId: string

role: agency_owner \| agency_member

status: active \| inactive

invitedBy: string

joinedAt: Date

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

userId

role

status

## **Indexes**

agencyId

userId

agencyId + userId unique

agencyId + role

status

## **Important Rules**

- A user must be an active agency member to access agency APIs.

- Agency members can access only data where agencyId matches their
  agency.

# **9. Collection: clients**

## **Purpose**

Stores clients created by agencies.

Each client belongs to one agency.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

name: string

businessType: string

contactName: string

contactEmail: string

phone: string

notes: string

status: draft \| invited \| active \| inactive \| archived

createdBy: string

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

name

status

createdBy

## **Indexes**

agencyId

agencyId + status

agencyId + contactEmail

agencyId + name

createdAt

## **Important Rules**

- Agency users can see only clients under their own agency.

- Client documents must always include agencyId.

- A client should not be visible to client users until client access is
  created.

# **10. Collection: client_users**

## **Purpose**

Connects users to client accounts.

This allows client-side users to log in and view dashboards/leads.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

userId: string

role: client_user

status: active \| inactive \| invited

invitedBy: string

invitedAt: Date

joinedAt: Date

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

clientId

userId

role

status

## **Indexes**

agencyId

clientId

userId

clientId + userId unique

status

## **Important Rules**

- Client users can access only documents where clientId matches their
  assigned client.

- Client users cannot access agency dashboard APIs.

- Client users cannot access admin APIs.

# **11. Collection: report_uploads**

## **Purpose**

Stores uploaded report files and processing status.

This collection tracks original uploaded campaign CSV files, optional
lead CSV audit records, validation state, mapped columns, processing
result, and status.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

uploadedBy: string

source: meta_ads

reportType: campaign_report \| lead_report

originalFile: {

fileName: string

fileUrl: string

storageObjectKey: string

mimeType: string

size: number

}

status: uploaded \| processing \| failed \| draft_ready \| published \|
unpublished

rawColumns: string\[\]

mappedColumns: {

campaignName: string

spend: string

leads: string

impressions: string

clicks: string

reach: string

}

dateRange: {

startDate: Date

endDate: Date

}

rowCount: number

processingError: {

message: string

code: string

details: object

}

duplicateHash: string

processedAt: Date

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

clientId

uploadedBy

source

reportType

originalFile

status

## **Indexes**

agencyId

clientId

agencyId + clientId + createdAt

agencyId + status

clientId + status

duplicateHash

createdAt

## **Important Rules**

- Every report upload must belong to an agency and client.

- Client ownership must be checked before upload.

- Failed uploads should keep useful error information.

- Dashboard should not be published directly after upload.

- Dashboard must start as draft.

- `campaign_report` uploads can create report_metrics and dashboard
  records after successful processing.

- `lead_report` uploads are audit records for lead CSV imports. They
  must not create report_metrics or dashboard records.

# **12. Collection: report_metrics**

## **Purpose**

Stores calculated campaign metrics from processed reports.

Backend code calculates these numbers.  
Gemini must not calculate final metrics.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

reportUploadId: ObjectId

currency: string

dateRange: {

startDate: Date

endDate: Date

}

totals: {

totalSpend: number

totalLeads: number

costPerLead: number

impressions: number

clicks: number

ctr: number

cpc: number

reach: number

}

campaignBreakdown: \[

{

campaignName: string

spend: number

leads: number

costPerLead: number

impressions: number

clicks: number

ctr: number

cpc: number

reach: number

}

\]

bestCampaign: {

campaignName: string

reason: string

metric: string

}

worstCampaign: {

campaignName: string

reason: string

metric: string

}

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

clientId

reportUploadId

totals

campaignBreakdown

## **Indexes**

agencyId

clientId

reportUploadId unique

agencyId + clientId + createdAt

## **Important Rules**

- Metrics must be calculated by backend code.

- Handle division by zero.

- If leads are zero, costPerLead should not crash.

- If clicks are zero, CTR/CPC should not crash.

# **13. Collection: dashboards**

## **Purpose**

Stores client dashboard data created from report metrics and AI summary.

Dashboards must start in draft mode.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

reportUploadId: ObjectId

reportMetricsId: ObjectId

status: draft \| published \| unpublished \| archived

visibleToClient: boolean

title: string

sections: \[

{

type: string

title: string

visible: boolean

order: number

data: object

}

\]

aiSummary: {

summary: string

insights: string\[\]

recommendations: string\[\]

generatedAt: Date

provider: gemini

}

agencyNotes: string

publishedAt: Date

publishedBy: string

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

clientId

reportUploadId

reportMetricsId

status

visibleToClient

title

## **Indexes**

agencyId

clientId

reportUploadId

agencyId + clientId + status

clientId + visibleToClient

createdAt

## **Important Rules**

- New dashboards must be created with status: draft.

- New dashboards must be created with visibleToClient: false.

- Client users can see only dashboards where:

  - status === "published"

  - visibleToClient === true

  - clientId matches their client

- Agency must review before publishing.

# **14. Collection: leads**

## **Purpose**

Stores leads uploaded by agencies and updated by clients.

Each lead belongs to one agency and one client.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

reportUploadId: ObjectId

name: string

phone: string

email: string

sourceCampaign: string

source: meta_ads

status: New \| Contacted \| Interested \| Not Interested \| Follow-up
Required \| Converted \| Invalid \| Not Reachable

remarks: string

followUpDate: Date

lastUpdatedBy: string

lastUpdatedByRole: agency_owner \| agency_member \| client_user \| admin

lastStatusUpdatedAt: Date

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

clientId

status

At least one contact field should exist:

phone

or

email

or

name

## **Indexes**

agencyId

clientId

agencyId + clientId + status

agencyId + clientId + followUpDate

clientId + status

phone

email

createdAt

## **Important Rules**

- Client users can update only:

  - status

  - remarks

  - followUpDate

- Client users cannot change:

  - agencyId

  - clientId

  - reportUploadId

  - campaign metrics

  - source campaign

- Every status update should create a lead status history record.

# **15. Collection: lead_status_history**

## **Purpose**

Tracks every lead status update.

This is important for accountability and agency proof of follow-up.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

leadId: ObjectId

oldStatus: string

newStatus: string

remarks: string

followUpDate: Date

updatedBy: string

updatedByRole: agency_owner \| agency_member \| client_user \| admin

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

clientId

leadId

oldStatus

newStatus

updatedBy

updatedByRole

## **Indexes**

agencyId

clientId

leadId

agencyId + clientId + leadId

createdAt

## **Important Rules**

- Never update lead status without creating history.

- History records should not be casually deleted.

- Agency should be able to view history for support and reporting.

# **16. Collection: activity_logs**

## **Purpose**

Stores important platform actions for support, audit, and debugging.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

actorUserId: string

actorRole: agency_owner \| agency_member \| client_user \| admin

action: string

entityType: string

entityId: ObjectId

message: string

metadata: object

ipAddress: string

userAgent: string

createdAt: Date

updatedAt: Date

## **Required Fields**

actorRole

action

message

## **Indexes**

agencyId

clientId

actorUserId

action

entityType + entityId

createdAt

## **Important Logged Actions**

For MVP, log actions that are implemented by the task list. Future-only actions should not be treated as required until their feature is intentionally added.

agency_registered

agency_suspended

agency_reactivated

client_created

client_updated

report_uploaded

report_processing_failed

dashboard_draft_created

dashboard_published

lead_uploaded

lead_status_updated

client_viewed_dashboard

subscription_created

subscription_activated

payment_failed

subscription_cancelled

admin_action

## **Important Rules**

- Activity logs help debug support issues.

- Admin actions should always be logged.

- Do not store secrets or sensitive tokens in activity logs.

# **17. Collection: admin_users**

## **Purpose**

Stores internal SaaS admin users.

These are not agency users or client users.

## **Fields**

\_id: ObjectId

userId: string

role: super_admin \| support_admin \| read_only_admin

status: active \| inactive

createdAt: Date

updatedAt: Date

## **Required Fields**

userId

role

status

## **Indexes**

userId unique

role

status

## **Important Rules**

- Admin users should access only admin routes.

- Admin routes must not be accessible by agency or client users.

- Admin actions should be logged.

# **18. Collection: subscriptions**

## **Purpose**

Stores Razorpay subscription state for each agency.

Agency users are paid seats. Client users are free.

## **Fields**

_id: ObjectId

agencyId: ObjectId

provider: razorpay

planKey: starter | team | custom

planName: string

currency: INR

amountMonthlyInPaise: number

includedAgencySeats: number

extraAgencySeats: number

status: trialing | active | pending | inactive | cancelled | halted | expired | payment_failed

razorpayCustomerId: string

razorpayPlanId: string

razorpaySubscriptionId: string

currentStart: Date

currentEnd: Date

nextBillingAt: Date

cancelledAt: Date

createdBy: string

createdAt: Date

updatedAt: Date

## **Required Fields**

agencyId

provider

planKey

status

currency

amountMonthlyInPaise

## **Indexes**

agencyId

agencyId + status

razorpaySubscriptionId unique sparse

razorpayCustomerId

## **Important Rules**

- One agency should have only one active/trialing subscription at a time.
- Backend must not trust frontend payment success alone.
- Razorpay webhook events must update subscription status after signature verification.

# **19. Collection: billing_events**

## **Purpose**

Stores Razorpay webhook/payment/subscription events for audit and debugging.

## **Fields**

_id: ObjectId

agencyId: ObjectId

subscriptionId: ObjectId

provider: razorpay

eventType: string

providerEventId: string

payload: object

processingStatus: processed | failed | ignored

processedAt: Date

errorMessage: string

createdAt: Date

updatedAt: Date

## **Required Fields**

provider

eventType

payload

processingStatus

## **Indexes**

agencyId

subscriptionId

eventType

providerEventId

createdAt

## **Important Rules**

- Store enough event data for support/debugging.
- Do not store Razorpay key secret or webhook secret in MongoDB.
- Failed webhook processing should be visible in logs/admin support workflows.

# **20. Optional Collection: ai_summaries**

## **Purpose**

Stores AI-generated summaries separately.

This is optional for MVP because dashboard can store aiSummary directly.

Use this collection if the team wants better AI audit history.

## **Fields**

\_id: ObjectId

agencyId: ObjectId

clientId: ObjectId

reportUploadId: ObjectId

reportMetricsId: ObjectId

provider: gemini

promptVersion: string

inputSnapshot: object

output: {

summary: string

insights: string\[\]

recommendations: string\[\]

}

status: success \| failed

errorMessage: string

createdAt: Date

updatedAt: Date

## **Indexes**

agencyId

clientId

reportUploadId

status

createdAt

## **Important Rules**

- Do not send raw sensitive lead data to Gemini unless required.

- Prefer sending cleaned metrics only.

- AI failure should not break report processing completely.

# **21. Relationship Summary**

## **Agency Relationship**

agency

→ agency_members

→ users

## **Client Relationship**

agency

→ clients

→ client_users

→ users

## **Report Relationship**

agency

→ client

→ report_upload

→ report_metrics

→ dashboard

## **Lead Relationship**

agency

→ client

→ leads

→ lead_status_history

## **Activity Log Relationship**

user/admin/client/agency action

→ activity_logs

# **22. Ownership Query Rules**

## **22.1 Agency User Query Rule**

When agency user fetches client data:

{ \_id: clientId, agencyId: loggedInUser.agencyId }

When agency user fetches report data:

{ \_id: reportId, agencyId: loggedInUser.agencyId }

When agency user fetches leads:

{ agencyId: loggedInUser.agencyId, clientId }

## **22.2 Client User Query Rule**

When client user fetches dashboard:

{

\_id: dashboardId,

agencyId: loggedInClientUser.agencyId,

clientId: loggedInClientUser.clientId,

status: "published",

visibleToClient: true

}

When client user fetches leads:

{

agencyId: loggedInClientUser.agencyId,

clientId: loggedInClientUser.clientId

}

When client user updates lead:

{

\_id: leadId,

agencyId: loggedInClientUser.agencyId,

clientId: loggedInClientUser.clientId

}

## **22.3 Admin Query Rule**

Admin can query across agencies only through admin routes.

Admin routes must check that the logged-in user is an internal admin.

# **23. Suggested Module to Collection Mapping**

## **auth module**

Uses:

users

agency_members

client_users

admin_users

## **agency module**

Uses:

agencies

agency_members

activity_logs

## **client module**

Uses:

clients

client_users

activity_logs

## **report module**

Uses:

report_uploads

report_metrics

dashboards

activity_logs

## **dashboard module**

Uses:

dashboards

report_metrics

report_uploads

activity_logs

## **lead module**

Uses:

leads

lead_status_history

activity_logs

## **admin module**

Uses:

admin_users

agencies

clients

report_uploads

activity_logs

## **ai module**

Uses:

ai_summaries optional

dashboards

report_metrics

# **24. Mongoose Model Rules**

Every model should:

- Use TypeScript interfaces

- Use Mongoose schema validation

- Use timestamps

- Define indexes

- Use enums from constants

- Avoid magic strings

- Keep schema and model files according to the backend folder structure

Example module structure:

backend/src/modules/client/

├── client.schema.ts

├── client.model.ts

├── client.controller.ts

├── client.routes.ts

├── client.service.ts

├── client.validation.ts

└── client.test.ts

# **25. Data Safety Rules**

## **25.1 Never Trust Frontend**

Frontend can send wrong IDs.

Backend must check ownership every time.

Bad:

Find client by clientId only

Good:

Find client by clientId and agencyId

## **25.2 Never Let Client Edit Metrics**

Client users can update leads only.

Client users cannot update:

- report metrics

- dashboard calculations

- agency notes

- uploaded report data

## **25.3 Never Publish Automatically**

Dashboard must start as draft.

Agency review is required before publishing.

## **25.4 Do Not Store Secrets**

Do not store these in MongoDB:

- Gemini API key

- R2 secret access key

- Better Auth secret

- MongoDB connection string

- AWS credentials

- Razorpay key secret

- Razorpay webhook secret

Secrets belong in environment variables.

# **26. MVP Collections Checklist**

Codex should create Mongoose models for these collections first:

users

agencies

agency_members

clients

client_users

report_uploads

report_metrics

dashboards

leads

lead_status_history

activity_logs

admin_users

subscriptions

billing_events

Optional:

ai_summaries

Better Auth-managed auth collections for sessions, accounts, and
verifications should also be configured by the auth module.

# **27. Final Schema Rule**

The most important database rule is:

Every agency-owned document must include agencyId.  
Every client-specific document must include both agencyId and
clientId.  
Every protected query must check ownership.

This rule is required for production-grade SaaS security.
