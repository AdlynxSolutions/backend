# **Product Requirements Document**

## **1. Product Overview**

This product is a SaaS platform for digital marketing agencies and
freelance marketers.

Agencies upload campaign reports and lead data from platforms like Meta
Ads. The platform converts those uploaded reports into clean,
client-friendly dashboards. Clients can view campaign performance,
understand results in simple language, and update lead status, remarks,
and follow-up dates in one place.

The product helps agencies reduce manual reporting work, improve client
communication, and prove campaign performance more clearly.

## **2. One-Line Product Definition**

A paid SaaS platform where digital marketing agencies subscribe with Razorpay, upload ad reports, automatically create client dashboards, and let clients manage lead statuses in one place.

## **3. Simple Product Positioning**

Upload messy ad reports.  
Turn them into clean client dashboards.  
Track every lead follow-up.  
Prove campaign performance with clarity.

## **4. Target Users**

## **4.1 Digital Marketing Agencies**

Agencies that run paid ad campaigns for multiple clients.

They need to:

- Send campaign reports to clients

- Explain campaign performance

- Track whether clients followed up with leads

- Prove that campaigns generated results

## **4.2 Freelance Marketers**

Freelancers who manage ads for small businesses.

They need to:

- Look professional

- Share simple reports

- Avoid manual reporting work

- Track lead follow-up without using messy spreadsheets

## **4.3 Agency Clients**

Business owners or client-side teams who receive leads from campaigns.

They need to:

- Understand campaign performance

- See how much was spent

- See how many leads came

- Track lead status

- Add remarks and follow-up dates

## **4.4 Internal SaaS Admin**

The platform owner/team.

They need to:

- Monitor agencies

- Track failed uploads

- View platform usage

- Support users

- Suspend or reactivate agencies if needed

## **5. Problem Statement**

Digital marketing agencies often spend too much time preparing client
reports manually.

Ad platform reports from Meta Ads or Google Ads can be difficult for
clients to understand. Clients usually do not care about complicated
marketing metrics. They mostly want to know:

- How much money was spent?

- How many leads came?

- What was the cost per lead?

- Which campaign worked best?

- Which leads need follow-up?

- How many leads converted?

Another major problem is lead follow-up tracking.

Agencies may generate leads, but clients may not contact all leads
properly. Later, the client may blame the agency for poor results, even
if the campaign generated enough leads.

This creates a communication gap:

Agency says: “We generated leads.”  
Client says: “We did not get sales.”  
But nobody clearly tracks whether the leads were contacted, followed up,
or converted.

## **6. Core Problems Solved**

## **Problem 1: Manual Reporting**

Agencies should not have to manually create reports, screenshots, PDFs,
or WhatsApp updates for every client.

The platform helps agencies upload reports and generate clean dashboards
automatically.

## **Problem 2: Client-Friendly Reporting**

Meta Ads and Google Ads reports are often too technical for clients.

The platform simplifies campaign data into clear dashboard sections and
simple explanations.

## **Problem 3: Lead Follow-Up Tracking**

Clients often update lead status in Google Sheets, WhatsApp, or not at
all.

The platform gives clients a structured place to update lead status,
remarks, and follow-up dates.

## **Problem 4: Agency Proof of Performance**

Agencies need to prove whether campaigns generated leads and whether
clients followed up properly.

The platform connects campaign performance with lead follow-up activity.

## **7. Main Value Proposition**

The product helps agencies convert raw ad reports into client-ready
dashboards and track what happens after leads are generated.

The full value chain is:

Raw ad report  
→ Clean dashboard  
→ AI-generated explanation  
→ Lead tracking  
→ Follow-up visibility  
→ Agency proof of performance

## **8. MVP Goal**

The MVP should prove one clear workflow:

Agency registers  
→ Agency chooses a Razorpay subscription or starts an allowed trial  
→ Agency dashboard opens  
→ Agency adds client  
→ Agency uploads Meta Ads campaign CSV  
→ System validates and processes file  
→ System calculates metrics  
→ Gemini creates client-friendly summary  
→ Dashboard draft is created  
→ Agency reviews dashboard  
→ Agency publishes dashboard  
→ Client logs in  
→ Client views dashboard  
→ Agency uploads leads  
→ Client views leads  
→ Client updates lead status  
→ Agency tracks lead follow-up progress

This flow is more important than adding many integrations or advanced
features.

## **9. MVP Scope**

## **9.1 Included in MVP**

The MVP includes:

- Agency registration

- Agency login

- Agency dashboard

- Razorpay subscription billing

- Pricing page

- Agency billing page

- Add client

- Client list

- Client detail page

- Basic client user access/linking for portal login

- Meta Ads campaign CSV upload

- Lead CSV upload

- File validation

- Column mapping for common Meta Ads CSV columns

- Backend metrics calculation

- Gemini-generated summary

- Dashboard draft creation

- Agency review before publish

- Publish dashboard to client

- Client dashboard

- Client lead list

- Client lead status update

- Lead remarks

- Follow-up date

- Basic admin dashboard

- Failed upload monitoring

- Activity logs

## **9.2 Not Included in MVP**

The MVP does not include:

- Meta Ads API integration

- Google Ads API integration

- Google Analytics integration

- WhatsApp integration

- Mobile app

- Advanced white-labeling

- Advanced team permission system

- Advanced dashboard builder

- Automated scheduled reports

- Multi-platform report support

- Real-time ad sync

- Client chat system

- Advanced billing features such as coupons, annual plans, refunds dashboard, tax/GST invoice automation, and in-app plan customization

The MVP is upload-first, not API-first.

## **9.3 Pricing and Billing**

Billing provider:

Razorpay Subscriptions

Currency:

INR (₹)

MVP pricing:

- Starter: ₹499/month, includes 1 agency user
- Team: ₹999/month, includes 2 agency users
- Extra agency user: ₹499/month per extra agency user

Billing rules:

- Agency users are paid seats.
- Client users are free.
- Agency access requires an active or trialing subscription.
- If subscription becomes pending, inactive, cancelled, halted, expired, or payment_failed, agency users should be restricted to billing/account recovery pages.
- Backend must verify Razorpay webhook signatures before updating subscription status.

## **10. Core Product Flow**

## **10.1 Agency Flow**

Agency registers  
→ Agency creates workspace  
→ Agency chooses Starter or Team plan  
→ Razorpay subscription is created  
→ Backend records subscription status from Razorpay  
→ Agency dashboard opens  
→ Agency adds client  
→ Agency uploads Meta Ads campaign report CSV  
→ System validates file  
→ System maps columns  
→ System calculates campaign metrics  
→ Gemini generates a simple summary  
→ Dashboard draft is created  
→ Agency reviews dashboard  
→ Agency edits notes or summary if needed  
→ Agency publishes dashboard  
→ Client gets access

Agency billing rule:

Agency users can access paid product features only when the agency subscription is active or trialing. Client users are free, but client portal access depends on the agency account remaining active or trialing.

## **10.2 Client Flow**

Client logs in  
→ Client views published dashboard  
→ Client sees campaign performance  
→ Client sees AI-generated simple explanation  
→ Client views leads  
→ Client updates lead status  
→ Client adds remarks  
→ Client adds follow-up date  
→ Agency sees lead progress

## **10.3 Admin Flow**

Admin logs in  
→ Admin views platform dashboard  
→ Admin views all agencies  
→ Admin monitors failed uploads  
→ Admin views activity logs  
→ Admin suspends or reactivates agency if needed

## **11. User Roles**

## **11.1 Public Visitor**

A person who visits the landing page.

Can:

- View landing page

- View product explanation

- Register as agency

- Login

Cannot:

- Access agency dashboard

- Access client dashboard

- Access admin panel

## **11.2 Agency Owner**

The main owner of an agency workspace.

Can:

- Register agency

- Login

- Manage agency workspace

- Add clients

- Upload reports

- Upload leads

- Review dashboard drafts

- Publish dashboards

- View lead follow-up progress

- View agency activity

- Manage subscription billing

## **11.3 Agency Member**

A team member inside the agency.

Can:

- Login

- View agency dashboard

- View clients

- Upload reports if allowed

- Review dashboards if allowed

- View leads

For MVP, agency owner and agency member can have mostly similar
permissions.

Advanced team permissions are not required in MVP.

## **11.4 Client User**

A client of an agency.

Can:

- Login

- View published dashboards

- View campaign performance

- View leads

- Update lead status

- Add remarks

- Add follow-up date

Cannot:

- Edit campaign metrics

- Edit dashboard calculations

- Upload campaign reports

- Publish dashboards

- View other clients’ data

- Access agency dashboard

- Access admin panel

## **11.5 Admin User**

Internal SaaS admin.

Can:

- View all agencies

- View platform stats

- View failed uploads

- View activity logs

- Suspend agency

- Reactivate agency

Cannot:

- Access secrets or environment variables from the UI

- Casually edit client campaign metrics

- Use agency features as a normal agency user without clear support
  reason

## **12. Main Features**

## **12.1 Agency Registration and Login**

Agency owner can create an account and agency workspace.

Required fields:

- Agency name

- Owner name

- Email

- Password

After registration:

- User is created

- Agency workspace is created

- User becomes agency owner

- User is sent to billing or allowed trial setup

- Agency dashboard opens only after the agency subscription is active or
  trialing

## **12.2 Agency Dashboard**

Agency dashboard should show:

- Total clients

- Total reports uploaded

- Total dashboards published

- Total leads uploaded

- Pending follow-ups

- Reports waiting for review

- Recent activity

- Setup checklist

Primary actions:

- Add client

- Upload report

- View clients

- View reports

## **12.3 Client Management**

Agency can add and manage clients.

Client fields:

- Client name

- Business type

- Contact name

- Contact email

- Phone

- Notes

- Status

Client status values:

- draft

- invited

- active

- inactive

Agency can:

- Add client

- View client details

- Edit basic client information

- Create or link client user access for portal login

- View client reports

- View client dashboards

- View client leads

- Invite client

## **12.4 Report Upload**

Agency can upload a Meta Ads campaign CSV report for a selected client.

Upload flow:

Agency selects client  
→ Agency chooses report type  
→ Agency uploads CSV  
→ Backend validates file  
→ Backend processes file  
→ Dashboard draft is generated

Allowed MVP CSV upload types:

- Meta Ads campaign CSV

- Lead CSV

Meta Ads campaign CSV uploads create report metrics and dashboard drafts.

Lead CSV uploads are handled by the lead module through the lead upload
flow. They import leads under the selected agency and client, but do not
create campaign metrics or dashboard drafts.

Validation should check:

- File type

- Empty file

- Required columns

- Duplicate upload

- File size limit

- Client ownership

If upload fails:

- Report status becomes failed

- Error message is shown

- Agency can upload again

## **12.5 Column Mapping**

System should map common Meta Ads CSV columns into standard fields.

Examples:

- Campaign name → campaignName

- Amount spent → spend

- Results → leads

- Link clicks → clicks

- Impressions → impressions

- Reach → reach

If column mapping is unclear, the system can flag it for review.

Gemini can suggest column mappings, but agency/backend rules should
confirm final mapping.

## **12.6 Metrics Calculation**

Backend code must calculate all final numbers.

Metrics for MVP:

- Total spend

- Total leads

- Cost per lead

- Impressions

- Clicks

- CTR

- CPC

- Campaign-wise breakdown

- Best performing campaign

- Worst performing campaign

Important rule:

Gemini must not calculate final numbers.  
Backend code calculates numbers.  
Gemini explains numbers.

## **12.7 Gemini AI Summary**

Gemini should generate a simple client-friendly explanation from cleaned
metrics.

Gemini can generate:

- Campaign summary

- Performance explanation

- Simple insights

- Suggested next steps

- Client-friendly observations

Gemini should not:

- Calculate final metrics

- Decide permissions

- Publish dashboards

- Change report data

- Make billing decisions

Example summary:

“Your campaign generated 126 leads at an average cost of ₹84 per lead.
The Lead Generation campaign performed best because it produced the
highest number of leads at the lowest cost. However, 48 leads are still
not contacted, so follow-up should be improved.”

## **12.8 Dashboard Draft**

After report processing, the system creates a dashboard in draft mode.

Draft dashboard should include:

- Key metrics

- Charts

- Campaign breakdown

- Gemini summary

- Agency notes

- Lead summary if available

Important rule:

Client cannot view draft dashboards.

## **12.9 Agency Review and Publish**

Agency must review the dashboard before publishing.

Agency can:

- View extracted metrics

- Review charts

- Edit Gemini summary

- Add agency notes

- Hide/show selected sections if supported

- Publish dashboard

- Keep dashboard as draft

When published:

- Dashboard status becomes published

- visibleToClient becomes true

- publishedAt is saved

- Activity log is created

- Client can view dashboard

## **12.10 Client Dashboard**

Client dashboard should be simple and non-technical.

Client should see:

- Total spend

- Total leads

- Cost per lead

- Impressions

- Clicks

- Campaign performance

- Simple summary

- Agency notes

- Lead status summary

- Leads requiring follow-up

Client should not see unnecessary complex ad platform data.

## **12.11 Lead Tracking**

Agency can upload lead data.

Lead fields:

- Name

- Phone

- Email

- Campaign source

- Status

- Remarks

- Follow-up date

- Last updated by

- Created date

Default lead statuses:

- New

- Contacted

- Interested

- Not Interested

- Follow-up Required

- Converted

- Invalid

- Not Reachable

Client can update:

- Status

- Remarks

- Follow-up date

Every lead status update should create a history record.

## **12.12 Lead Follow-Up Proof**

The platform should help agencies show whether clients followed up with
leads.

Example:

- Campaign generated: 150 leads

- Client contacted: 62 leads

- Interested: 24 leads

- Converted: 9 leads

- Not contacted: 88 leads

This helps the agency prove:

“The campaign generated leads, but many leads are still pending
follow-up.”

This is a key product value.

## **12.13 Admin Panel**

Admin panel is for the internal SaaS team.

MVP admin features:

- Admin login

- View total agencies

- View total clients

- View uploaded reports

- View failed uploads

- View recent activity

- View agency details

- Suspend agency

- Reactivate agency

Admin panel should be functional and secure.

It should not be overbuilt in MVP.

## **13. Data and Security Requirements**

## **13.1 Multi-Tenant Data Rule**

This is the most important security rule:

Agency A must never access Agency B’s data.  
Client A must never access Client B’s data.

Every tenant-owned document should include:

- agencyId

Every client-specific document should include:

- agencyId

- clientId

Backend queries must check ownership before returning data.

## **13.2 Client Access Rule**

Clients can only access dashboards and leads assigned to their client
account.

Clients cannot view:

- Draft dashboards

- Other client dashboards

- Other client leads

- Agency internal dashboard

- Admin panel

## **13.3 Upload Security**

Uploaded files must be validated.

The system should reject:

- Wrong file types

- Empty files

- Files with missing required columns

- Very large files

- Duplicate uploads if detected

Raw uploaded files should not be publicly accessible without proper
authorization.

## **13.4 AI Safety Rule**

Gemini output must be reviewed by the agency before reaching the client.

Dashboards should always be created in draft mode first.

Agency review is required before publish.

## **14. Success Metrics**

The MVP is successful if:

- Agencies can complete the full upload-to-dashboard flow

- Agencies can publish dashboards to clients

- Clients can understand reports without explanation

- Clients can update lead status

- Agencies can track follow-up progress

- Report upload errors are understandable

- Permission rules prevent cross-agency/client access

Business success indicators:

- Number of agencies registered

- Number of clients added

- Number of reports uploaded

- Number of dashboards published

- Number of leads updated by clients

- Number of agencies returning weekly

- Number of failed uploads

- Time saved in report creation

## **15. MVP Demo Scenario**

The first complete demo should follow this exact path:

1.  Agency owner registers

2.  Agency activates Razorpay subscription or allowed trial

3.  Agency dashboard opens

4.  Agency adds a client

5.  Agency uploads Meta Ads campaign CSV

6.  System validates the file

7.  System calculates metrics

8.  Gemini creates summary

9.  Dashboard draft is created

10. Agency reviews dashboard

11. Agency publishes dashboard

12. Client logs in

13. Client views dashboard

14. Agency uploads leads

15. Client opens leads page

16. Client updates lead status

17. Agency sees updated lead progress

This demo should work end-to-end without manual database edits or
developer help.

## **16. Product Principles**

## **16.1 Upload-First, Not API-First**

The MVP should allow agencies to upload reports manually.

Do not start with Meta Ads API or Google Ads API.

This keeps the MVP simpler and faster to launch.

## **16.2 Client-Friendly First**

The dashboard should be designed for business owners, not technical
marketers.

Avoid unnecessary complex metrics in the client view.

## **16.3 Agency Review Before Client View**

AI-generated or auto-generated dashboards should always be reviewed by
the agency before publishing.

## **16.4 Backend Calculates, AI Explains**

Numbers must be calculated by backend code.

Gemini should only explain already calculated numbers.

## **16.5 Prove One Workflow Deeply**

The MVP should not try to support every ad platform.

Start with Meta Ads campaign CSV and lead CSV.

## **16.6 Deploy Staging Early**

The MVP should have a private staging deployment from the initial setup
phase.

This helps test real hosted behavior for authentication, billing
webhooks, CORS, file upload configuration, database connectivity, and
environment variables throughout development.

Early staging deployment is not a public launch. Production launch
should wait until the complete MVP demo flow works end to end.

## **17. Future Features**

Possible future features after MVP:

- Google Ads report upload

- Google Analytics report upload

- Meta Ads API connection

- Google Ads API connection

- Scheduled reports

- WhatsApp notifications

- PDF export

- White-label dashboards

- Agency team permissions

- Advanced billing features such as coupons, annual plans, refunds dashboard, tax/GST invoice automation, and in-app plan customization

- Client comments

- Automated weekly/monthly summaries

- Advanced dashboard builder

- More chart types

- Google Sheets import

- CRM integrations

## **18. Final MVP Statement**

The first version should prove that an agency can upload a Meta Ads
report, automatically generate a clean client dashboard, publish it to a
client, and track what happens to the generated leads.

If this works smoothly, the product has a strong foundation for a real
SaaS startup.
