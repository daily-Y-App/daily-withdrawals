# RewardHub - System Architecture

## Overview

RewardHub သည် User ဘက်ခြမ်း နဲ့ Admin ဘက်ခြမ်း အသီးခြားခွဲခြားတဲ့ အဆင့်မြင့် reward system ဖြစ်ပါသည်။

---

## System Structure

```
┌─────────────────────────────────────────────────────────┐
│                    RewardHub System                      │
└─────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│ Share Repo (daily-Y-App/Dailywage-Share)                │
│ ═══════════════════════════════════════════════════════  │
│ User-Side Application                                    │
│                                                          │
│ - index-user.html (User Dashboard)                      │
│   ├── Dashboard (Points & Baht Display)                 │
│   ├── Withdraw Tab (Withdrawal Form)                    │
│   ├── History Tab (Withdrawal History)                  │
│   └── Profile Tab (User Information)                    │
│                                                          │
│ Features:                                                │
│ ✓ User Authentication                                   │
│ ✓ Points Display (100 pts = 10 Baht)                   │
│ ✓ Withdrawal Request Form                              │
│ ✓ Real-time Status Tracking                            │
│ ✓ Transaction History                                  │
│ ✓ LocalStorage Data Persistence                        │
└──────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│ Withdraw Repo (daily-Y-App/daily-withdrawals)           │
│ ═══════════════════════════════════════════════════════  │
│ Admin-Side Application                                   │
│                                                          │
│ - admin-panel.html (Admin Dashboard)                    │
│   ├── Pending Requests Tab                              │
│   ├── Completed Requests Tab                            │
│   ├── Failed Requests Tab                               │
│   └── All Requests Tab                                  │
│                                                          │
│ Features:                                                │
│ ✓ Admin Authentication (admin@rewardhub.com)           │
│ ✓ Pending Withdrawal Management                        │
│ ✓ Approval/Rejection System                            │
│ ✓ Real-time Statistics                                 │
│ ✓ Detailed Request Viewing                             │
│ ✓ Transaction Logging                                  │
└──────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│ Shared Data Storage (Firebase/LocalStorage)             │
│ ═══════════════════════════════════════════════════════  │
│                                                          │
│ Collections:                                             │
│ ├── users/                                              │
│ │   ├── {userId}/                                       │
│ │   │   ├── email                                       │
│ │   │   ├── name                                        │
│ │   │   ├── points                                      │
│ │   │   ├── totalEarned                                 │
│ │   │   ├── totalWithdrawn                              │
│ │   │   └── createdAt                                   │
│ │                                                        │
│ ├── withdrawals/                                         │
│ │   ├── {withdrawalId}/                                 │
│ │   │   ├── userId                                      │
│ │   │   ├── userName                                    │
│ │   │   ├── userEmail                                   │
│ │   │   ├── points                                      │
│ │   │   ├── baht                                        │
│ │   │   ├── status (pending/completed/failed)          │
│ │   │   ├── truemoneyPhone                              │
│ │   │   ├── truemoneyName                               │
│ │   │   ├── notes                                       │
│ │   │   ├── requestedAt                                 │
│ │   │   ├── completedAt                                 │
│ │   │   ├── approvedBy                                  │
│ │   │   └── rejectionReason                             │
│                                                          │
│ └── config/                                              │
│       ├── ADMIN_EMAIL: "admin@rewardhub.com"            │
│       ├── CONVERSION_RATE: 100 pts = 10 Baht           │
│       ├── MIN_WITHDRAWAL: 1000 pts                      │
│       └── SYSTEM_VERSION: "1.0.0"                       │
└──────────────────────────────────────────────────────────┘
```

---

## User Workflow

### 1. User Registration & Login
```
User Browser
    ↓
index-user.html
    ↓
Load User Data (LocalStorage/Firebase)
    ↓
Display Dashboard
```

### 2. Withdrawal Request Process
```
User Dashboard
    ↓
Click "Withdraw" Tab
    ↓
Fill Withdrawal Form
    ├── Points (Min: 1000)
    ├── TrueMoneyဖုန်းနံပါတ် (10 digits)
    ├── TrueMoneyအကောင့်အမည်
    └── မှတ်ချက် (Optional)
    ↓
Click "ငွေထုတ်တောင်းဆိုခြင်း"
    ↓
Validation Check
    ├── Points ≥ 1000?
    ├── Points ≤ Current Balance?
    ├── Phone Format Valid?
    └── Account Name Provided?
    ↓
Show Confirmation Modal
    ↓
User Confirms
    ↓
Deduct Points from User Account
    ↓
Create Withdrawal Record
    ├── Status: "pending"
    ├── RequestedAt: Current Timestamp
    └── Save to Database
    ↓
Show Success Message
```

### 3. Withdrawal History Tracking
```
User Dashboard → History Tab
    ↓
Display All Withdrawals
    ├── Date
    ├── Points
    ├── Baht
    ├── TrueMoneyအကောင့်အမည်
    └── Status (pending/completed/failed)
    ↓
Real-time Status Updates
```

---

## Admin Workflow

### 1. Admin Login
```
Admin Browser
    ↓
admin-panel.html
    ↓
Check Admin Email (admin@rewardhub.com)
    ↓
Load Pending Requests
    ↓
Display Admin Dashboard
```

### 2. Pending Request Management
```
Admin Dashboard
    ↓
View Pending Requests Tab
    ↓
Display Table with:
    ├── Date
    ├── User Name
    ├── Phone Number
    ├── Points
    ├── Baht
    └── Actions (View/Approve/Reject)
    ↓
Admin Reviews Request
    ├── Click "View" → See Full Details
    ├── Click "✓" → Approve Request
    └── Click "✕" → Reject Request
```

### 3. Approval Process
```
Admin Clicks "✓ Approve"
    ↓
Open Approval Modal
    ├── Show User Details
    ├── Show Baht Amount
    ├── Show TrueMoneyအကောင့်
    └── Optional: Add Approval Notes
    ↓
Admin Confirms Approval
    ↓
Admin Transfers Money to TrueMoneyအကောင့်
    ↓
Admin Clicks "အတည်ပြုခြင်း"
    ↓
Update Withdrawal Status
    ├── Status: "completed"
    ├── CompletedAt: Current Timestamp
    ├── ApprovedBy: Admin Email
    └── ApprovalNotes: Saved
    ↓
System Updates Database
    ↓
Show Success Message
```

### 4. Rejection Process
```
Admin Clicks "✕ Reject"
    ↓
Open Rejection Modal
    ├── Show User Details
    ├── Show Baht Amount
    └── Require Rejection Reason
    ↓
Admin Enters Reason
    ↓
Admin Clicks "ပယ်ဖျက်ခြင်း"
    ↓
Update Withdrawal Status
    ├── Status: "failed"
    ├── RejectedAt: Current Timestamp
    ├── RejectedBy: Admin Email
    └── RejectionReason: Saved
    ↓
Refund Points to User Account
    ├── Add Points Back
    ├── Update User Balance
    └── Log Refund Transaction
    ↓
System Updates Database
    ↓
Show Success Message
```

### 5. Statistics & Reporting
```
Admin Dashboard
    ↓
Display Real-time Stats
    ├── Pending Count
    ├── Completed Count
    ├── Failed Count
    └── Total Baht Processed
    ↓
View All Requests
    ├── Pending Requests
    ├── Completed Requests
    ├── Failed Requests
    └── All Requests Combined
```

---

## Data Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    User Side                                │
│                 (Share Repo)                                │
│                                                             │
│  User Requests Withdrawal                                  │
│  ├── Points: 1000                                          │
│  ├── Phone: 0812345678                                     │
│  ├── Name: အကောင့်အမည်                                    │
│  └── Notes: Optional                                       │
│                                                             │
│  ↓ Validation & Deduction                                  │
│                                                             │
│  Create Withdrawal Record                                  │
│  ├── Status: pending                                       │
│  ├── Points: 1000                                          │
│  ├── Baht: 100                                             │
│  └── RequestedAt: Timestamp                                │
│                                                             │
│  ↓ Save to Database                                        │
│                                                             │
│  User Sees "စောင့်ဆိုင်းနေ" Status                        │
└─────────────────────────────────────────────────────────────┘
                          ↓
                    Shared Database
                    (Firebase/LocalStorage)
                          ↓
┌─────────────────────────────────────────────────────────────┐
│                    Admin Side                               │
│                (Withdraw Repo)                              │
│                                                             │
│  Admin Views Pending Requests                              │
│  ├── User Name                                             │
│  ├── Points: 1000                                          │
│  ├── Baht: 100                                             │
│  ├── Phone: 0812345678                                     │
│  └── Status: pending                                       │
│                                                             │
│  ↓ Admin Reviews & Transfers Money                         │
│                                                             │
│  Admin Clicks Approve/Reject                               │
│                                                             │
│  If Approved:                                              │
│  ├── Status: completed                                     │
│  ├── CompletedAt: Timestamp                                │
│  └── ApprovedBy: Admin Email                               │
│                                                             │
│  If Rejected:                                              │
│  ├── Status: failed                                        │
│  ├── RejectedAt: Timestamp                                 │
│  ├── RejectionReason: Text                                 │
│  └── Refund Points to User                                 │
│                                                             │
│  ↓ Update Database                                         │
└─────────────────────────────────────────────────────────────┘
                          ↓
                    Shared Database
                    (Firebase/LocalStorage)
                          ↓
┌─────────────────────────────────────────────────────────────┐
│                    User Side                                │
│                 (Share Repo)                                │
│                                                             │
│  User Sees Updated Status                                  │
│  ├── If Approved: "အတည်ပြုပြီး"                          │
│  └── If Rejected: "ပယ်ဖျက်ပြီး"                           │
│                                                             │
│  History Tab Shows:                                        │
│  ├── Date                                                  │
│  ├── Amount                                                │
│  ├── Status                                                │
│  └── Completion Details                                    │
└─────────────────────────────────────────────────────────────┘
```

---

## Database Schema

### Users Collection
```
users/
├── {userId}/
│   ├── id: string (unique)
│   ├── email: string
│   ├── name: string
│   ├── points: number
│   ├── totalEarned: number
│   ├── totalWithdrawn: number
│   ├── createdAt: timestamp
│   └── lastUpdated: timestamp
```

### Withdrawals Collection
```
withdrawals/
├── {withdrawalId}/
│   ├── id: string (unique)
│   ├── userId: string
│   ├── userName: string
│   ├── userEmail: string
│   ├── points: number
│   ├── baht: number
│   ├── status: enum (pending/completed/failed)
│   ├── truemoneyPhone: string
│   ├── truemoneyName: string
│   ├── notes: string (optional)
│   ├── requestedAt: timestamp
│   ├── completedAt: timestamp (if completed)
│   ├── rejectedAt: timestamp (if rejected)
│   ├── approvedBy: string (admin email)
│   ├── rejectedBy: string (admin email)
│   ├── approvalNotes: string (optional)
│   └── rejectionReason: string (if rejected)
```

### Config Collection
```
config/
├── ADMIN_EMAIL: "admin@rewardhub.com"
├── CONVERSION_RATE: "100 pts = 10 Baht"
├── MIN_WITHDRAWAL: 1000
├── MAX_WITHDRAWAL: unlimited
├── SYSTEM_VERSION: "1.0.0"
└── LAST_UPDATED: timestamp
```

---

## Security Features

### 1. Authentication
- User Email/Password Authentication
- Admin Email Verification (admin@rewardhub.com)
- Session Management via LocalStorage

### 2. Authorization
- User သာ ကိုယ်ကိုယ်ခွဲခြား data ကြည့်နိုင်ခြင်း
- Admin သာ Admin Panel အသုံးပြုခွင့်
- Points Deduction ချက်ချင်း ပြုလုပ်ခြင်း

### 3. Data Validation
- Points Validation (Min 1000)
- Phone Number Validation (10 digits)
- Account Name Validation
- Status Enum Validation

### 4. Transaction Logging
- အားလုံးသော withdrawal မှုများ မှတ်တမ်းတွင် သိမ်းဆည်းခြင်း
- Admin အတည်ပြုမှု မှတ်တမ်းတွင် သိမ်းဆည်းခြင်း
- Timestamp အားလုံး မှတ်တမ်းတွင် သိမ်းဆည်းခြင်း

### 5. Profit-Safe Logic
- Minimum Withdrawal: 1000 points (100 Baht)
- Admin Verification Required
- Points Refund on Rejection
- Status Tracking (pending/completed/failed)

---

## Conversion Logic

### Points to Baht
```
Formula: Baht = Points ÷ 10

Examples:
├── 1000 pts = 100 Baht
├── 1500 pts = 150 Baht
├── 2000 pts = 200 Baht
├── 5000 pts = 500 Baht
└── 10000 pts = 1000 Baht
```

---

## Setup Instructions

### 1. User Side (Share Repo)
```
1. Clone repository
   git clone https://github.com/daily-Y-App/Dailywage-Share.git

2. Open index-user.html in browser
   - No build tools required
   - Direct file opening works

3. Configure Firebase (Optional)
   - Replace firebaseConfig in HTML
   - Or use LocalStorage for demo
```

### 2. Admin Side (Withdraw Repo)
```
1. Clone repository
   git clone https://github.com/daily-Y-App/daily-withdrawals.git

2. Open admin-panel.html in browser
   - Admin Email: admin@rewardhub.com
   - No build tools required

3. Configure Firebase (Optional)
   - Replace firebaseConfig in HTML
   - Or use LocalStorage for demo
```

### 3. Firebase Setup (Optional)
```
1. Create Firebase Project
   - Go to https://console.firebase.google.com
   - Create new project

2. Enable Realtime Database
   - Create database
   - Start in test mode

3. Get Firebase Config
   - Copy config from project settings
   - Update firebaseConfig in HTML files

4. Create Collections
   - users/
   - withdrawals/
   - config/

5. Set Security Rules
   - User သာ ကိုယ်ကိုယ်ခွဲခြား data ကြည့်နိုင်ခြင်း
   - Admin သာ အားလုံး data ကြည့်နိုင်ခြင်း
```

---

## Troubleshooting

### Issue: Data Not Syncing
```
Solution:
1. Check Firebase Connection
2. Verify Config Keys
3. Check Browser Console for Errors
4. Use LocalStorage for Demo
```

### Issue: Admin Cannot Approve
```
Solution:
1. Verify Admin Email (admin@rewardhub.com)
2. Check Firebase Rules
3. Verify User Data Exists
```

### Issue: Points Not Deducted
```
Solution:
1. Check LocalStorage Data
2. Verify Withdrawal Record Created
3. Check Browser Console
4. Refresh Page
```

---

## Performance Considerations

### Optimization
- LocalStorage Caching
- Lazy Loading
- Minimal Dependencies
- Direct HTML/CSS/JS

### Scalability
- Firebase Realtime Database
- Cloud Functions for Processing
- Admin Panel Load Balancing
- Batch Processing for Reports

---

## Future Enhancements

### Planned Features
- [ ] Email Notifications
- [ ] SMS Notifications
- [ ] Automated Approval Rules
- [ ] Batch Withdrawal Processing
- [ ] Advanced Reporting
- [ ] User Analytics
- [ ] Mobile App Integration
- [ ] Payment Gateway Integration

---

## Contact & Support

For issues or questions:
- Email: admin@rewardhub.com
- GitHub: https://github.com/daily-Y-App/daily-withdrawals
