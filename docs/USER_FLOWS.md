# **User Flows**

## **1. Purpose**

This document defines the main user journeys for the MVP.

It should help developers and Codex understand:

- Who is using the product

- What action they take

- What the system should do

- What happens next

The MVP has three main sides:

1.  Agency side

2.  Client side

3.  Admin side

# **2. Core MVP Flow**

The main MVP flow is:

Agency registers  
→ Agency activates Razorpay subscription or allowed trial  
→ Agency dashboard opens  
→ Agency adds client  
→ Agency uploads Meta Ads campaign CSV  
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

This flow is the most important product journey.

# **3. Agency Onboarding Flow**

## **User**

Agency owner

## **Goal**

Create an agency account and open the agency workspace.

## **Flow**

Agency owner visits landing page  
→ Clicks “Start Free Trial” or “Register”  
→ Enters agency name, owner name, email, and password  
→ System validates the form  
→ System creates user account  
→ System creates agency workspace  
→ User becomes agency owner  
→ User chooses Starter or Team plan  
→ System creates Razorpay subscription flow  
→ Razorpay confirms payment/subscription through webhook  
→ Agency subscription becomes active or trialing  
→ System redirects user to agency dashboard

## **Success Result**

Agency owner can access the agency dashboard.

## **Error Cases**

- Email already exists

- Required fields are missing

- Password is too weak

- Server error during registration

- Razorpay subscription creation failed

- Payment failed or was cancelled

# **4. Subscription Billing Flow**

## **User**

Agency owner

## **Goal**

Activate or maintain the agency subscription.

## **Plans**

- Starter: ₹499/month, includes 1 agency user
- Team: ₹999/month, includes 2 agency users
- Extra agency user: ₹499/month per extra agency user
- Client users: free

## **Flow**

Agency owner opens pricing or billing page  
→ Chooses Starter or Team plan  
→ Backend creates Razorpay subscription  
→ Frontend opens Razorpay checkout/subscription flow  
→ Razorpay sends webhook to backend  
→ Backend verifies webhook signature  
→ Backend updates subscription status  
→ Agency access is allowed when status is active or trialing

## **Success Result**

Agency can access paid MVP features.

## **Error Cases**

- Payment failed

- Razorpay webhook verification failed

- Subscription status is pending, inactive, cancelled, halted, expired, or payment_failed

- Agency tries to access paid features without active/trialing subscription

## **Important Rule**

Client users are free, but client portal access depends on the agency subscription being active or trialing.

# **5. Agency Login Flow**

## **User**

Agency owner or agency member

## **Goal**

Log in and access agency dashboard.

## **Flow**

User opens login page  
→ Enters email and password  
→ System validates credentials  
→ System checks user role  
→ Agency user is redirected to agency dashboard

## **Success Result**

Agency user reaches /agency/dashboard.

## **Error Cases**

- Invalid email or password

- User account is suspended

- Agency workspace is suspended

- User role is not allowed

# **6. Add Client Flow**

## **User**

Agency owner or agency member

## **Goal**

Add a client under the agency workspace.

## **Flow**

Agency user opens clients page  
→ Clicks “Add Client”  
→ Enters client details  
→ System validates input  
→ System creates client under logged-in agency  
→ System records activity log  
→ User is redirected to client detail page  
→ Agency can create or link client user access when the client needs portal login

## **Client Details**

Required or useful fields:

- Client name

- Business type

- Contact name

- Contact email

- Phone

- Notes

## **Success Result**

Client is created and visible in the agency’s client list.

## **Error Cases**

- Missing required fields

- Invalid email

- Duplicate client email under same agency

- Agency ownership check fails

# **7. Report Upload Flow**

## **User**

Agency owner or agency member

## **Goal**

Upload a Meta Ads campaign CSV for a selected client.

## **Flow**

Agency user opens client detail page  
→ Clicks “Upload Report”  
→ Selects report source as Meta Ads  
→ Selects report type as campaign report  
→ Uploads CSV file  
→ System checks client ownership  
→ System validates file type  
→ System checks whether file is empty  
→ System reads CSV headers  
→ System validates required columns  
→ System maps columns to standard fields  
→ System saves original upload record  
→ System calculates campaign metrics  
→ System calls Gemini for client-friendly summary  
→ System creates dashboard draft  
→ User is redirected to report review page

## **Success Result**

Dashboard draft is created for agency review.

## **Error Cases**

- Wrong file type

- Empty CSV

- Missing required columns

- File too large

- CSV parsing failed

- Client does not belong to logged-in agency

- Gemini request failed

## **Important Rule**

If Gemini fails, report processing should not completely fail.

The system should still create a dashboard draft with calculated metrics
and show a fallback summary.

# **8. Dashboard Review Flow**

## **User**

Agency owner or agency member

## **Goal**

Review generated dashboard before client can see it.

## **Flow**

Agency user opens report review page  
→ System shows extracted metrics  
→ System shows campaign breakdown  
→ System shows charts  
→ System shows Gemini-generated summary  
→ Agency reviews the information  
→ Agency edits summary or adds notes if needed  
→ Agency saves draft or publishes dashboard

## **Success Result**

Dashboard remains draft or becomes published based on agency action.

## **Error Cases**

- Report does not belong to agency

- Dashboard not found

- Metrics failed to load

- Save draft failed

- Publish failed

## **Important Rule**

Client must not see the dashboard while it is in draft mode.

# **9. Publish Dashboard Flow**

## **User**

Agency owner or agency member

## **Goal**

Make dashboard visible to the client.

## **Flow**

Agency user opens dashboard review page  
→ Clicks “Publish to Client”  
→ System confirms publish action  
→ Dashboard status becomes published  
→ visibleToClient becomes true  
→ publishedAt is saved  
→ Activity log is created  
→ Client can now view dashboard

## **Success Result**

Published dashboard becomes visible in the client portal.

## **Error Cases**

- Dashboard already published

- Dashboard does not belong to agency

- User does not have permission

- Publish action fails

# **10. Client Access Flow**

## **User**

Client user

## **Goal**

Log in and view published dashboard.

## **Precondition**

Agency has created or linked client user access for the client account. For MVP, this can be a simple `client_users` record connected to a Better Auth user identity.

## **Flow**

Client opens login page  
→ Enters email and password  
→ System validates credentials  
→ System checks client role  
→ Client is redirected to client dashboard  
→ System loads only dashboards assigned to that client  
→ Client views campaign performance

## **Success Result**

Client can see published dashboards assigned to their client account.

## **Error Cases**

- Invalid login

- Client account not active

- No published dashboard available

- Client tries to access another client’s dashboard

- Client tries to access draft dashboard

# **11. Client Dashboard Flow**

## **User**

Client user

## **Goal**

Understand campaign performance in simple language.

## **Flow**

Client opens dashboard  
→ System shows key metrics  
→ System shows campaign performance  
→ System shows Gemini summary  
→ System shows agency notes  
→ System shows lead status summary if leads exist  
→ Client can go to leads page

## **Client Can View**

- Total spend

- Total leads

- Cost per lead

- Impressions

- Clicks

- Campaign summary

- Agency notes

- Lead status summary

## **Client Cannot Do**

- Edit campaign metrics

- Edit report data

- View draft dashboards

- Access other client dashboards

- Access agency dashboard

- Access admin panel

# **12. Lead Upload Flow**

## **User**

Agency owner or agency member

## **Goal**

Upload lead data for a selected client.

## **Flow**

Agency user opens client detail page  
→ Clicks “Upload Leads”  
→ Uploads lead CSV  
→ System checks client ownership  
→ System validates file  
→ System parses lead data  
→ System saves leads under agency and client  
→ Activity log is created  
→ Leads become visible to agency and client

## **Lead Fields**

Supported fields:

- Name

- Phone

- Email

- Campaign source

- Created date

- Status

## **Success Result**

Leads are imported and visible in the client lead tracker.

## **Error Cases**

- Wrong file type

- Empty CSV

- Missing lead fields

- Duplicate leads

- Client ownership check fails

# **13. Client Lead Status Update Flow**

## **User**

Client user

## **Goal**

Update lead status, remarks, and follow-up date.

## **Flow**

Client opens leads page  
→ Client views lead list  
→ Client selects a lead  
→ Client updates status  
→ Client adds remarks if needed  
→ Client adds follow-up date if needed  
→ System validates allowed fields  
→ System updates lead  
→ System creates lead status history record  
→ Agency can see updated lead progress

## **Allowed Statuses**

- New

- Contacted

- Interested

- Not Interested

- Follow-up Required

- Converted

- Invalid

- Not Reachable

## **Client Can Update**

- Status

- Remarks

- Follow-up date

## **Client Cannot Update**

- Campaign metrics

- Report data

- Lead ownership

- Agency notes

- Other client leads

## **Success Result**

Lead status is updated and agency can track follow-up progress.

## **Error Cases**

- Lead does not belong to client

- Invalid status

- Unauthorized field update

- Update failed

# **14. Agency Lead Tracking Flow**

## **User**

Agency owner or agency member

## **Goal**

Track whether the client is following up with leads.

## **Flow**

Agency opens client lead page  
→ System shows lead status summary  
→ Agency views leads by status  
→ Agency checks pending leads  
→ Agency checks overdue follow-ups  
→ Agency checks converted leads  
→ Agency uses this data during client communication

## **Agency Can See**

- Total leads

- New leads

- Contacted leads

- Interested leads

- Converted leads

- Not contacted leads

- Follow-up required leads

- Overdue follow-ups

## **Success Result**

Agency can prove both campaign performance and follow-up progress.

# **15. Admin Login Flow**

## **User**

Internal admin user

## **Goal**

Access internal SaaS admin panel.

## **Flow**

Admin opens the shared login page (`/login`)  
→ Enters credentials  
→ System validates admin role  
→ Admin is redirected to admin dashboard

## **Success Result**

Admin can access /admin/dashboard.

## **Error Cases**

- Invalid credentials

- User is not an admin

- Admin account disabled

# **16. Admin Monitoring Flow**

## **User**

Internal admin user

## **Goal**

Monitor platform activity and support agencies.

## **Flow**

Admin opens admin dashboard  
→ System shows platform stats  
→ Admin views agencies  
→ Admin checks failed uploads  
→ Admin views activity logs  
→ Admin opens agency detail if needed  
→ Admin takes support action if needed

## **Admin Can View**

- Total agencies

- Total clients

- Total reports uploaded

- Failed uploads

- Recent activity

- Agency details

- Upload errors

## **Admin Can Act**

- Suspend agency

- Reactivate agency

- View activity logs

## **Success Result**

Admin can operate and support the SaaS platform.

# **17. Agency Suspension Flow**

## **User**

Internal admin user

## **Goal**

Suspend or reactivate an agency.

## **Suspend Flow**

Admin opens agency detail page  
→ Clicks “Suspend Agency”  
→ System asks for confirmation  
→ Agency status becomes suspended  
→ Agency users can no longer access dashboard  
→ Activity log is created

## **Reactivate Flow**

Admin opens suspended agency detail page  
→ Clicks “Reactivate Agency”  
→ System asks for confirmation  
→ Agency status becomes active  
→ Agency users can access dashboard again  
→ Activity log is created

## **Important Rule**

Suspending an agency should not delete its data.

# **18. Permission Rules Across All Flows**

## **Billing Access Rule**

Agency users can access paid product features only when agency subscription status is active or trialing.

If subscription is pending, inactive, cancelled, halted, expired, or payment_failed, agency users should be redirected to billing/account recovery pages.

## **Agency Data Rule**

Agency users can access only documents where:

document.agencyId === loggedInUser.agencyId

## **Client Data Rule**

Client users can access only documents where:

document.clientId === loggedInClientUser.clientId

## **Dashboard Rule**

Client users can access only dashboards where:

dashboard.visibleToClient === true

dashboard.status === "published"

## **Admin Rule**

Only internal admin users can access admin routes.

Agency users and client users must never access admin routes.

# **19. Activity Logs**

The system should create activity logs for important actions.

Important logged actions:

- Agency registered

- Agency subscription created

- Agency subscription activated

- Razorpay payment failed

- Client added

- Report uploaded

- Report processing failed

- Dashboard draft created

- Dashboard published

- Lead CSV uploaded

- Client updated lead status

- Admin suspended agency

- Admin reactivated agency

Activity logs help with support, debugging, and accountability.

# **20. Most Important MVP Journey**

The first demo must support this journey:

1.  Agency registers

2.  Agency activates Razorpay subscription or allowed trial

3.  Agency dashboard opens

4.  Agency adds client

5.  Agency uploads Meta Ads CSV

6.  System validates and processes report

7.  System calculates metrics

8.  Gemini generates summary

9.  Dashboard draft is created

10. Agency reviews dashboard

11. Agency publishes dashboard

12. Client logs in

13. Client views dashboard

14. Agency uploads leads

15. Client views leads

16. Client updates lead status

17. Agency sees updated lead progress

If this journey works smoothly, the MVP is ready for early user testing.
