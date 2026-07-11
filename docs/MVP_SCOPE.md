# **MVP Scope**

## **1. Purpose**

This document defines the strict scope for version 1 of the product.

The goal of the MVP is not to build a full agency analytics platform.

The goal is to prove one core paid SaaS workflow:

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

This file should be used by developers and Codex to avoid building
unnecessary features too early.

## **2. MVP Product Direction**

The MVP is an upload-first reporting and lead tracking platform for
digital marketing agencies.

Agencies should be able to upload campaign reports manually instead of
connecting ad platform APIs.

This keeps the first version simpler, faster to build, and easier to
test with real agencies.

## **2.1 Early Staging Deployment**

The MVP should be deployed to a private staging environment from the
initial setup phase.

This staging deployment should be used to test:

- Frontend and backend hosting

- Better Auth authentication URLs

- Razorpay webhook delivery

- CORS between frontend and backend

- MongoDB on EC2 connectivity

- Cloudflare R2 and Gemini environment configuration

Early staging deployment does not expand MVP scope.

It is only for integration testing, demo readiness, and reducing launch
risk. Public production launch should happen only after the complete MVP
flow works end to end.

## **3. MVP One-Line Definition**

A paid SaaS platform where agencies subscribe with Razorpay, upload Meta Ads CSV reports, generate client dashboards, and let clients update lead status in one place.

## **4. Main MVP Users**

The MVP supports these user types:

- Agency owner

- Agency member

- Client user

- Internal admin

Advanced role customization is not required in MVP.

## **5. Included in MVP**

## **5.1 Public Pages**

The MVP includes:

- Landing page

- Pricing page

- Login page

- Agency registration page

The landing page should explain:

- What the product does

- Who it is for

- How it works

- Why agencies should use it

## **5.2 Agency Authentication**

The MVP includes:

- Agency owner registration

- Agency login

- Logout

- Better Auth authentication with backend Better Auth session verification

When an agency owner registers:

- User account is created

- Agency workspace is created

- User becomes agency owner

## **5.3 Subscription Billing**

The MVP includes Razorpay subscription billing for agency accounts.

Currency:

INR (₹)

Billing provider:

Razorpay Subscriptions

Plans:

- Starter: ₹499/month, includes 1 agency user
- Team: ₹999/month, includes 2 agency users
- Extra agency user: ₹499/month per extra agency user

Billing rules:

- Agency users are paid seats.
- Client users are free.
- Agency access requires an active or trialing subscription.
- If subscription becomes pending, inactive, cancelled, halted, expired, or payment_failed, agency users should be restricted to billing/account recovery pages.
- Razorpay webhook events must update subscription status in the backend.
- Backend must verify Razorpay webhook signatures.

## **5.4 Agency Dashboard**

The MVP includes a basic agency dashboard showing:

- Total clients

- Total uploaded reports

- Total dashboards published

- Total leads uploaded

- Reports waiting for review

- Recent activity

The dashboard should have clear actions:

- Add client

- Upload report

- View clients

## **5.5 Client Management**

The MVP includes:

- Add client

- View client list

- View client detail page

- Edit basic client information

- Create or link basic client user access for portal login

Client fields:

- Client name

- Business type

- Contact name

- Contact email

- Phone

- Notes

- Status

Client statuses:

- draft

- invited

- active

- inactive

## **5.6 Meta Ads Campaign CSV Upload**

The MVP includes uploading Meta Ads campaign CSV reports.

The system should support one primary report type first:

- Meta Ads campaign CSV

The upload flow:

Agency selects client  
→ Agency uploads CSV  
→ Backend validates file  
→ Backend processes file  
→ Dashboard draft is created

Validation should check:

- File type

- Empty file

- Required columns

- File size limit

- Client ownership

## **5.7 Basic Column Mapping**

The MVP includes basic mapping for common Meta Ads CSV columns.

Supported mappings should include:

- Campaign name → campaignName

- Amount spent → spend

- Results → leads

- Impressions → impressions

- Link clicks → clicks

- Reach → reach

If a file has missing or unclear columns, the upload should fail with a
clear message.

Advanced custom column mapping UI is not required in the first MVP.

## **5.8 Metrics Calculation**

The backend should calculate:

- Total spend

- Total leads

- Cost per lead

- Total impressions

- Total clicks

- CTR

- CPC

- Campaign-wise breakdown

Important rule:

Backend code calculates the final numbers.  
Gemini does not calculate final numbers.

## **5.9 Gemini AI Summary**

The MVP includes Gemini-generated client-friendly summaries.

Gemini can generate:

- Campaign performance summary

- Simple explanation of results

- Key insights

- Suggested next steps

Gemini should only receive cleaned metrics calculated by the backend.

Gemini must not:

- Calculate final spend

- Calculate final lead count

- Calculate CPL

- Calculate CTR

- Decide permissions

- Publish dashboards automatically

## **5.10 Dashboard Draft**

The MVP includes dashboard draft creation after a report is processed.

Dashboard draft should include:

- Key metric cards

- Campaign breakdown

- Basic charts

- Gemini summary

- Agency notes section

Important rule:

Client users must not see draft dashboards.

## **5.11 Agency Review and Publish**

The MVP includes an agency review page.

Agency can:

- Review extracted metrics

- Review Gemini summary

- Edit or add agency notes

- Publish dashboard to client

When published:

- Dashboard status becomes published

- visibleToClient becomes true

- publishedAt is saved

- Activity log is created

## **5.12 Client Dashboard**

The MVP includes a simple client dashboard.

Client dashboard should show:

- Total spend

- Total leads

- Cost per lead

- Impressions

- Clicks

- Campaign performance

- Gemini summary

- Agency notes

- Lead status summary if leads exist

The client dashboard should be simple and easy for non-marketing users
to understand.

## **5.13 Lead CSV Upload**

The MVP includes uploading lead CSV files.

Lead CSV should support basic lead fields:

- Name

- Phone

- Email

- Campaign source

- Created date

Agency can upload leads for a client.

The system should save leads under the correct agency and client.

Lead CSV uploads use the lead upload API and do not create campaign
metrics or dashboard drafts.

## **5.14 Client Lead Tracking**

The MVP includes a client lead tracking page.

Client users can:

- View leads

- Update lead status

- Add remarks

- Add follow-up date

Allowed lead statuses:

- New

- Contacted

- Interested

- Not Interested

- Follow-up Required

- Converted

- Invalid

- Not Reachable

Client users cannot:

- Delete leads

- Edit campaign metrics

- Edit uploaded report data

- Access another client’s leads

## **5.15 Lead Status History**

The MVP includes lead status history.

Every time a lead status changes, the system should record:

- Lead ID

- Old status

- New status

- Remarks

- Updated by

- Updated at

This helps agencies track whether clients are following up properly.

## **5.16 Basic Admin Panel**

The MVP includes a basic internal admin panel.

Admin can:

- View platform stats

- View all agencies

- View agency details

- View failed uploads

- View recent activity logs

- Suspend agency

- Reactivate agency

Admin panel should be functional, not fancy.

## **5.17 Activity Logs**

The MVP includes activity logs for important events.

Log events such as:

- Agency registered

- Client added

- Report uploaded

- Report processing failed

- Dashboard created

- Dashboard published

- Client viewed dashboard

- Client updated lead status

- Admin suspended agency

- Admin reactivated agency

## **6. Not Included in MVP**

Do not build these features in version 1:

- Meta Ads API integration

- Google Ads API integration

- Google Analytics integration

- Instagram Insights integration

- LinkedIn Ads integration

- WhatsApp integration

- Mobile app

- Advanced white-label dashboard

- Advanced dashboard builder

- Drag-and-drop dashboard editor

- Automated scheduled reporting

- Real-time ad sync

- Multi-language support

- Client chat system

- Advanced team permission customization

- Advanced billing features such as coupons, annual plans, refunds dashboard, tax/GST invoice automation, and in-app plan customization

- Google Sheets import

- CRM integrations

- PDF report export

- Email report scheduling

These features can be considered after the MVP flow is working.

## **7. MVP Pages**

## **Public Pages**

- /

- /pricing

- /login

- /register

## **Agency Pages**

- /agency/dashboard

- /agency/billing

- /agency/billing/success

- /agency/billing/failed

- /agency/clients

- /agency/clients/new

- /agency/clients/:clientId

- /agency/clients/:clientId/upload-report

- /agency/reports/:reportId/review

- /agency/clients/:clientId/leads

## **Client Pages**

- /client/dashboard

- /client/leads

- /client/reports/:dashboardId

## **Admin Pages**

- /admin/dashboard

- /admin/agencies

- /admin/agencies/:agencyId

- /admin/failed-uploads

- /admin/activity-logs

## **8. MVP Backend Modules**

The backend should include:

- Auth module

- Billing module

- Agency module

- Client module

- Report upload module

- CSV parser service

- Metrics calculator service

- Gemini summary service

- Dashboard module

- Lead module

- Lead status history module

- Activity log module

- Admin module

## **9. MVP Data Collections**

The MVP should include these MongoDB collections:

- users

- Better Auth sessions

- Better Auth accounts

- Better Auth verifications

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

Optional for MVP:

- ai_summaries

## **10. MVP Success Criteria**

The MVP is successful when the team can complete this flow without
manual database editing, including Razorpay billing status setup through the application/webhook flow:

1.  Agency owner registers

2.  Agency activates Razorpay subscription or allowed trial

3.  Agency dashboard opens

4.  Agency adds a client

5.  Agency uploads Meta Ads campaign CSV

6.  Backend validates the file

7.  Backend calculates campaign metrics

8.  Gemini generates simple summary

9.  Dashboard draft is created

10. Agency reviews dashboard

11. Agency publishes dashboard

12. Client logs in

13. Client views dashboard

14. Agency uploads leads

15. Client views leads

16. Client updates lead status and remarks

17. Agency sees updated lead progress

## **11. MVP Quality Rules**

The MVP must follow these quality rules:

- Agency data must be isolated by agencyId

- Client data must be isolated by clientId

- Client users must not see draft dashboards

- Client users must not edit report metrics

- All backend inputs must be validated with Joi

- All important MongoDB models must use Mongoose schemas

- Report upload errors must be clear

- Dashboard should start as draft

- AI output must be reviewed before publishing

- Sensitive actions should create activity logs

## **12. First Launch Goal**

The first launch should target a small number of real agencies or
freelance marketers.

The purpose of the first launch is to learn:

- Can agencies upload reports easily?

- Does the generated dashboard save time?

- Do clients understand the dashboard?

- Do clients actually update lead status?

- Does lead tracking help agencies prove performance?

- Which report formats create upload problems?

The first launch is for validation, not perfection.

## **13. Final MVP Rule**

If a feature does not help this flow, do not build it yet:

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

Everything else is later.
