# Testing Guide - AdopTree World

## Overview

This comprehensive testing guide provides end-to-end test scenarios for all user roles on the AdopTree World platform. Use this guide to verify functionality, identify bugs, and ensure quality before production deployment.

**Testing Philosophy:**
- Test on staging environment only
- Test all user journeys end-to-end
- Verify both happy paths and error cases
- Document any issues found
- Retest after fixes

---

## Table of Contents

1. [Testing Environment](#testing-environment)
2. [Donor Test Scenarios](#donor-test-scenarios)
3. [Merchant Test Scenarios](#merchant-test-scenarios)
4. [Admin Test Scenarios](#admin-test-scenarios)
5. [Corporate Test Scenarios](#corporate-test-scenarios)
6. [Edge Case Testing](#edge-case-testing)
7. [Performance Testing](#performance-testing)
8. [Security Testing](#security-testing)
9. [Pre-Launch Checklist](#pre-launch-checklist)

---

## Testing Environment

### Staging Environment

**Purpose**: Safe testing environment isolated from production

**Staging Details:**
- **URL**: https://staging.adoptreeworld.com/
- **Environment**: Separate database, separate payment test mode
- **Data**: Can be reset/modified without affecting real users
- **Payment**: Midtrans test mode (no real charges)
- **Emails**: Sent to real addresses but marked as [STAGING]

**⚠️ CRITICAL**: Never test on production (https://adoptreeworld.com/)

---

### Super Admin Access

**Credentials for Staging:**
```
Email: admin@adoptreeworld.com
Password: Admin123!
Role: SuperAdmin (full platform access)
```

**SuperAdmin Capabilities:**
- Access all admin functions
- Approve/reject merchants
- Manage all users
- View all transactions
- Modify any data
- Access system monitoring

**Use SuperAdmin account to:**
- Approve test merchant applications
- Verify admin workflows
- Check transaction processing
- Test moderation features

---

### Test User Accounts

**Create test accounts for each role:**

1. **Test Donor**
   - Register: donor_test@example.com
   - Purpose: Test donation flows

2. **Test Merchant**
   - Register: merchant_test@example.com
   - Purpose: Test land/tree management

3. **Test Corporate**
   - Register: corporate_test@example.com
   - Purpose: Test B2B features

**Keep credentials organized**: Use password manager or documented spreadsheet

---

### Payment Testing

**Midtrans Test Mode:**

**Test Credit Card:**
```
Card Number: 4811 1111 1111 1114
Expiry: 01/25
CVV: 123
OTP: 112233
```

**Test E-Wallet (GoPay):**
```
Phone Number: 0812345678
OTP/PIN: 111111
```

**Test Bank Transfer:**
- Select any bank
- Virtual account auto-generated
- Payment auto-confirmed after 2 minutes

**Test QRIS:**
- QR code generated
- Scan with staging Midtrans simulator
- Auto-confirmation

**Important**: No real money charged in staging environment

---

## Donor Test Scenarios

### Scenario 1: Complete Adoption Journey (Email Registration)

**Objective**: Test full user flow from registration to certificate download

**Prerequisites**: None

**Steps:**

#### 1. Registration via Email

1. Navigate to `https://staging.adoptreeworld.com/auth/register`
2. Fill registration form:
   - Full Name: `Test Donor User`
   - Email: `donor_test_{timestamp}@example.com`
   - Password: `TestPass123!`
   - Account Type: Select "Donor"
3. Click "Register"

**✓ Expected Result:**
- Redirect to `/auth/verify-pending`
- Page shows "Verify your email" message
- Email sent to inbox

#### 2. Email Verification

1. Check email inbox (use real email or temp email service)
2. Open "Verify Your Email - AdopTree World" email
3. Click verification link

**✓ Expected Result:**
- Redirect to staging site
- Success message: "Email verified successfully"
- Redirect to `/auth/login`

#### 3. Login

1. Enter registered email and password
2. Click "Sign In"

**✓ Expected Result:**
- Redirect to `/dashboard`
- Dashboard loads with empty state (no adoptions yet)
- User profile icon shows in top-right corner

#### 4. Browse Conservation Lands

1. Click "Explore Lands" in navigation or go to `/explore`
2. View featured lands

**✓ Expected Result:**
- At least 3-5 lands displayed
- Each land shows:
  - Land photo
  - Land name
  - Location
  - Available trees count
  - "View Details" button

#### 5. View Land Details

1. Click on any land (e.g., "Papua Reforestation")
2. Land detail page loads

**✓ Expected Result:**
- Land photos gallery
- Land description
- Location with map
- Tree species list
- Available trees for adoption
- Product tier options:
  - Donation ($8, 1 year)
  - Wakaf ($10, perpetual)
  - GreenSociety ($12, 1 year)
  - AdoptTree ($75, 5 years)

#### 6. Add Trees to Cart

1. Select product tier: "Donation ($8)"
2. Click "Add to Cart" button
3. Cart counter in navbar should increase
4. Toast notification: "Added to cart"

**✓ Expected Result:**
- Cart counter shows "1"
- Success message appears

5. Navigate to different land
6. Add another tree (different tier: "Wakaf ($10)")
7. Cart counter should show "2"

#### 7. View Shopping Cart

1. Click cart icon in navbar
2. Cart page loads (`/cart`)

**✓ Expected Result:**
- Shows 2 items
- Each item displays:
  - Tree species
  - Land name
  - Product tier and price
  - Quantity selector
  - Remove button
- Cart summary shows:
  - Subtotal: $18
  - Total: $18

#### 8. Adjust Cart

1. Change quantity of first item to 2
2. Cart total updates to $26 ($8 × 2 + $10)
3. Click "Remove" on second item
4. Cart now shows 1 item (quantity 2)
5. Total: $16

**✓ Expected Result:**
- Quantities update instantly
- Prices recalculate correctly
- Remove works without errors

#### 9. Proceed to Checkout

1. Click "Proceed to Checkout"
2. Redirect to `/checkout`

**✓ Expected Result:**
- Checkout page loads
- Step 1: Authentication (already logged in, auto-advance)
- Step 2: Dedication form appears

#### 10. Checkout - Step 1 (Authentication)

**✓ Expected Result:**
- Step auto-completed (user already logged in)
- Shows: "Logged in as Test Donor User"
- Option: "Gift this adoption" checkbox

**Test Gift Option (Optional):**
1. Check "Gift this adoption"
2. Recipient fields appear:
   - Recipient Name
   - Recipient Email
   - Gift Message
3. Uncheck for now (self-adoption)

#### 11. Checkout - Step 2 (Dedication)

1. Enter dedication message: `For a greener future!`
2. Click "Continue to Payment"

**✓ Expected Result:**
- Step 3: Payment method selection appears

#### 12. Checkout - Step 3 (Payment Method)

1. View available payment methods:
   - Midtrans Snap (all-in-one)
   - Bank Transfer
   - E-Wallet (GoPay, OVO, LinkAja, DANA)
   - QRIS
   - Solana (crypto)

2. Select "E-Wallet - GoPay"
3. Click "Complete Payment"

**✓ Expected Result:**
- System creates adoption (status: PaymentPending)
- Midtrans Snap popup opens
- Shows payment amount: Rp ~240,000 (depends on exchange rate)

#### 13. Complete Payment (Midtrans)

1. In Midtrans popup:
   - Select "GoPay"
   - Enter phone: `0812345678`
   - Click "Pay"
2. Mock OTP screen appears
3. Enter OTP: `111111`
4. Confirm payment

**✓ Expected Result:**
- Payment success message in Midtrans
- Midtrans popup closes
- Redirect to `/checkout/success`

#### 14. Payment Success Page

**✓ Expected Result:**
- Success page displays:
  - ✅ "Payment Successful!" message
  - Order ID
  - Adoption ID
  - Total amount paid
  - "View Certificate" button
  - "Go to Dashboard" button

#### 15. View Certificate

1. Click "View Certificate"
2. Redirect to `/dashboard/certificates`

**✓ Expected Result:**
- Certificates tab loads
- New certificate listed:
  - Certificate ID
  - Tree species
  - Land name
  - Adoption date
  - "Download PDF" button
  - "Share" button

#### 16. Download Certificate PDF

1. Click "Download PDF"
2. PDF file downloads

**✓ Expected Result:**
- PDF file named: `AdopTree_Certificate_{ID}.pdf`
- PDF contains:
  - Certificate header with logo
  - Adopter name: "Test Donor User"
  - Tree details (species, location)
  - Adoption date
  - QR code for verification
  - Certificate ID
  - Professional design

#### 17. Verify Dashboard Stats

1. Click "Dashboard" or go to `/dashboard`
2. View "My Journey" tab

**✓ Expected Result:**
- Stats cards show updated data:
  - Trees Adopted: 2
  - CO2 Absorbed: ~X kg (calculated based on species)
  - Active Certificates: 1
  - Environmental Impact: visualization
- Map shows adopted tree locations (2 pins)
- Click tree pin → Shows tree details popup

#### 18. View Activity Timeline

1. Click "Activity" tab in dashboard

**✓ Expected Result:**
- Timeline shows recent events:
  - "Adopted 2 trees" with timestamp
  - "Payment completed" event
  - "Certificate generated" event

#### 19. Scan QR Code (Optional)

1. Open certificate PDF on computer
2. Scan QR code with phone camera
3. QR code opens URL

**✓ Expected Result:**
- URL opens: `https://staging.adoptreeworld.com/certificates/verify/{ID}`
- Public verification page shows:
  - Certificate valid ✅
  - Adopter name
  - Tree details
  - Land information
  - Adoption date
  - No authentication required (public)

---

**✅ Test Complete: Full adoption journey successful**

**Bugs to Report:**
- List any errors encountered
- Screenshot any issues
- Note steps where behavior differs from expected

---

### Scenario 2: Google OAuth Registration and Login

**Objective**: Test Google OAuth authentication flow

**Prerequisites**: Google account

**Steps:**

#### 1. Register via Google OAuth

1. Navigate to `/auth/register`
2. Click "Sign up with Google" button
3. Google OAuth popup opens
4. Select Google account
5. Authorize AdopTree World

**✓ Expected Result:**
- OAuth callback successful
- Account created automatically
- Email auto-verified (no verification step)
- Redirect to `/dashboard`
- Profile shows Google avatar
- Success message: "Signed in with Google"

#### 2. Verify Profile

1. Click profile icon → "Profile Settings"
2. Check profile information

**✓ Expected Result:**
- Name populated from Google account
- Email verified ✅
- Avatar shows Google profile picture
- Authentication method: "Google OAuth"
- No password set (OAuth user)

#### 3. Test Add Password Feature

OAuth users can optionally add password for email/password login.

1. Go to "Security" tab
2. Find "Add Password" section
3. Enter new password: `NewPass123!`
4. Confirm password
5. Click "Add Password"

**✓ Expected Result:**
- Success message: "Password added successfully"
- Section changes to "Change Password"
- User can now login with email/password OR Google

#### 4. Logout and Test Dual Login

1. Logout
2. **Test 1**: Login with Google
   - Click "Sign in with Google"
   - Auto-login (session cached)
   - ✅ Success
3. Logout again
4. **Test 2**: Login with Email/Password
   - Enter email and new password
   - Click "Sign In"
   - ✅ Success

**✓ Expected Result:**
- Both login methods work
- Same account accessed
- Profile consistent

---

### Scenario 3: Guest Checkout

**Objective**: Test adoption without creating account

**Prerequisites**: None (use incognito/private browsing)

**Steps:**

#### 1. Browse as Guest

1. Open staging site in incognito mode
2. Navigate to `/explore` (no login)
3. View land details
4. Add tree to cart

**✓ Expected Result:**
- Can browse without authentication
- Can add to cart
- Cart stored in session

#### 2. Proceed to Checkout as Guest

1. Click cart → "Proceed to Checkout"
2. Checkout page loads
3. Step 1: Authentication

**✓ Expected Result:**
- Shows two options:
  - "Login" button
  - "Continue as Guest" button

#### 3. Continue as Guest

1. Click "Continue as Guest"
2. Guest form appears

**Required Fields:**
- Full Name: `Guest Test User`
- Email: `guest_test@example.com`
- Phone: `+628123456789`

3. Fill form and submit

**✓ Expected Result:**
- Guest account created in background
- Proceed to payment step
- No password required

#### 4. Complete Payment

1. Select payment method: "Bank Transfer"
2. Complete payment (auto-confirm in 2 min)

**✓ Expected Result:**
- Payment success
- Certificate sent to guest email
- Guest can view certificate via link (no login)

#### 5. Verify Guest Email

1. Check guest email inbox
2. Open "Your AdopTree Certificate" email

**✓ Expected Result:**
- Email contains:
  - Certificate attachment PDF
  - View certificate link (public, no auth)
  - "Create Account" CTA (upgrade from guest)

#### 6. Upgrade Guest to Full Account

1. Click "Create Account" in email
2. Redirect to registration page with email pre-filled
3. Set password: `GuestUpgrade123!`
4. Complete registration

**✓ Expected Result:**
- Guest account upgraded to full account
- Previous adoption now linked to account
- Can login and view adoption in dashboard

---

### Scenario 4: Gift Adoption

**Objective**: Test gifting trees to another person

**Prerequisites**: Donor account

**Steps:**

#### 1. Add Tree to Cart

1. Login as donor
2. Browse lands and add tree to cart
3. Proceed to checkout

#### 2. Enable Gift Option

1. In checkout Step 1
2. Check "Gift this adoption" checkbox
3. Recipient form appears

**Recipient Details:**
- Recipient Name: `Gift Recipient Name`
- Recipient Email: `recipient_test@example.com`
- Gift Message: `Happy Birthday! Here's a tree for you! 🌳`

4. Fill form and continue to payment

**✓ Expected Result:**
- Gift details stored
- Payment proceeds normally

#### 3. Complete Payment

1. Complete payment (any method)

**✓ Expected Result:**
- Payment success
- Adoption created
- Certificate generated for recipient (not donor)

#### 4. Verify Gift Email

1. Check recipient email inbox
2. Open "You've Received a Tree Adoption Gift!" email

**✓ Expected Result:**
- Email contains:
  - Gift message from donor
  - Tree details
  - Certificate link
  - Download PDF button
  - "Create account to track your tree" CTA

#### 5. Recipient Views Certificate

1. Recipient clicks certificate link (no login required)
2. Public certificate page loads

**✓ Expected Result:**
- Certificate displays publicly
- Shows: "Adopted by [Recipient Name]"
- Shows: "Gift from [Donor Name]"
- Can download PDF
- Doesn't require account

---

### Scenario 5: Password Reset Flow

**Objective**: Test forgot password functionality

**Prerequisites**: Existing donor account

**Steps:**

#### 1. Initiate Password Reset

1. Go to `/auth/login`
2. Click "Forgot Password?" link
3. Redirect to `/auth/forgot-password`
4. Enter email: `donor_test@example.com`
5. Click "Send Reset Link"

**✓ Expected Result:**
- Success message: "Password reset link sent to your email"
- Email sent with reset link

#### 2. Check Reset Email

1. Open email inbox
2. Find "Reset Your Password - AdopTree World" email
3. Click "Reset Password" button

**✓ Expected Result:**
- Redirect to `/auth/reset-password?token={token}`
- Reset password form loads
- Token valid

#### 3. Set New Password

1. Enter new password: `NewTestPass123!`
2. Confirm password
3. Click "Reset Password"

**✓ Expected Result:**
- Success message: "Password reset successful"
- Redirect to `/auth/login`
- Can login with new password

#### 4. Verify Old Password Doesn't Work

1. Try logging in with old password
2. Should fail with error: "Invalid credentials"

**✓ Expected Result:**
- Old password rejected
- New password works

---

### Scenario 6: Profile Management

**Objective**: Test profile update and avatar upload

**Prerequisites**: Logged in donor

**Steps:**

#### 1. Navigate to Profile Settings

1. Click profile icon → "Profile Settings"
2. Profile tab loads

#### 2. Update Profile Information

1. Change name to: `Updated Test User`
2. Change phone: `+628987654321`
3. Click "Save Changes"

**✓ Expected Result:**
- Success message: "Profile updated"
- Changes saved to database
- Navbar shows updated name

#### 3. Upload Avatar

1. Go to Profile tab
2. Click "Upload Avatar" button
3. Select image file (JPG/PNG, < 5MB)
4. Crop if needed
5. Click "Upload"

**✓ Expected Result:**
- Upload progress shown
- Avatar updates immediately
- New avatar shown in navbar
- Avatar shown throughout platform (dashboard, certificates)

#### 4. Verify Avatar Persistence

1. Logout and login again
2. Check navbar and profile page

**✓ Expected Result:**
- Avatar persists after logout/login
- Shows in all locations

---

## Merchant Test Scenarios

### Scenario 7: Merchant Registration and Verification Workflow

**Objective**: Test complete merchant onboarding

**Prerequisites**: User account with verified email

**Steps:**

#### 1. Apply for Merchant Account

1. Login as regular user
2. Navigate to `/merchant/register`
3. Merchant application form loads

**Fill Application:**
- Business Name: `Test Conservation Initiative`
- Tax ID: `12.345.678.9-012.345`
- Business Registration Number: `REG-123456`
- Business Contact:
  - Phone: `+628123456789`
  - Email: `business@testconservation.com`
  - Address: `123 Green Street, Jakarta, Indonesia`
- Business Description: `A test conservation organization focused on reforestation.`

4. Click "Submit Application"

**✓ Expected Result:**
- Success message: "Application submitted successfully"
- Redirect to `/merchant/dashboard`
- Application status: "Pending Review"
- Progress stepper shows:
  - Stage 1: Application Submitted ✓
  - Stage 2: Under Review (pending)
  - Stage 3: Verification Complete (pending)
  - Stage 4: Account Active (pending)

#### 2. Check Merchant Dashboard (Pending State)

**✓ Expected Result:**
- Dashboard shows waiting message
- Limited features (cannot create lands yet)
- Status banner: "Your application is under review"
- Help section with contact info

#### 3. Admin Approves Merchant (Switch to SuperAdmin)

1. Logout from merchant account
2. Login as SuperAdmin: `admin@adoptreeworld.com` / `Admin123!`
3. Navigate to `/admin/merchants`
4. Filter: "Pending"
5. Find "Test Conservation Initiative" in list
6. Click on merchant name

**Merchant Detail Page:**
- Review business information
- Check tax ID
- Verify registration number
- Review contact details

7. Click "Approve Merchant" button
8. Confirm approval

**✓ Expected Result:**
- Merchant status → Verified
- Approval email sent
- Merchant appears in "Verified" filter

#### 4. Verify Merchant Access (Switch Back to Merchant)

1. Logout from admin
2. Login as merchant account
3. Navigate to `/merchant/dashboard`

**✓ Expected Result:**
- Full dashboard access
- Progress stepper shows all stages complete
- Can create lands and trees
- Statistics cards visible:
  - Total Lands: 0
  - Total Trees: 0
  - Total Earnings: $0.00
  - Recent Activity: empty
- "Add New Land" button active

---

### Scenario 8: Land Creation with Boundary Drawing

**Objective**: Test land creation with GPS boundary

**Prerequisites**: Verified merchant account

**Steps:**

#### 1. Navigate to Land Creation

1. Go to `/merchant/dashboard/lands`
2. Click "Add New Land" button
3. Land creation form loads

#### 2. Fill Land Details

**Basic Information:**
- Land Name: `Test Rainforest Conservation Area`
- Description: `A 10-hectare rainforest reserve dedicated to biodiversity conservation and carbon sequestration. This area supports endangered species habitat and provides environmental education opportunities.`
- Location: `Papua, Indonesia`
- Land Type: `Reforestation`

#### 3. Upload Land Photos

1. Click "Upload Photos"
2. Select 2-3 photos (use any landscape images)
3. Wait for upload

**✓ Expected Result:**
- Photos upload successfully
- Thumbnails displayed
- Can remove and re-upload

#### 4. Draw Land Boundary

1. Interactive map loads
2. Search for location: "Papua, Indonesia"
3. Zoom in to reasonable level
4. Click "Draw Boundary" button
5. Map enters drawing mode

**Draw Polygon:**
1. Click points around desired boundary
2. Draw irregular polygon (5-10 points)
3. Connect back to start point
4. Double-click to finish

**✓ Expected Result:**
- Polygon drawn on map
- Polygon filled with semi-transparent color
- Area auto-calculated
- Displays: "Area: 10.5 hectares" (varies by actual drawing)

**Boundary Tools:**
- Test "Edit" button: Drag points to adjust
- Test "Delete" button: Removes boundary, can redraw
- Verify "Undo" button works

#### 5. Select Tree Species

1. Species multi-select dropdown
2. Select species:
   - ✓ Mahogany (Swietenia macrophylla)
   - ✓ Teak (Tectona grandis)
   - ✓ Bamboo (Bambusa sp.)

**✓ Expected Result:**
- Multiple species selected
- Shows count: "3 species selected"

#### 6. Review and Submit Land

1. Review all entered information
2. Click "Create Land"
3. Processing spinner

**✓ Expected Result:**
- Success message: "Land created successfully"
- Redirect to `/merchant/dashboard/lands`
- New land appears in list
- Land status: "Active"

#### 7. Verify Land Details

1. Click on created land
2. Land detail page loads

**✓ Expected Result:**
- Shows all land information
- Photos displayed
- Map shows boundary polygon
- Species list displayed
- Stats: 0 trees (not added yet)
- "Manage Trees" button visible

---

### Scenario 9: Tree Management (Individual and Bulk)

**Objective**: Test both individual tree addition and CSV bulk import

**Prerequisites**: Created land from Scenario 8

#### Part A: Add Individual Tree

**Steps:**

1. From land detail page, click "Manage Trees"
2. Tree list loads (empty)
3. Click "Add New Tree" button

**Fill Tree Details:**
- Species: Select "Mahogany"
- GPS Coordinates:
  - **Option 1 - Manual Entry:**
    - Latitude: `-2.5489`
    - Longitude: `140.7184`
  - **Option 2 - Map Picker:**
    - Click "Pick Location on Map"
    - Map opens showing land boundary
    - Click inside boundary
    - Coordinates auto-filled

4. Planted Date: `2024-01-15` (past date)
5. Height: `2.5` meters
6. Diameter: `0.3` meters
7. Upload tree photo (any tree image)
8. Notes: `Healthy young mahogany tree`

9. Click "Add Tree"

**✓ Expected Result:**
- Success message: "Tree added successfully"
- Tree appears in tree list
- Tree has unique ID
- QR code auto-generated
- Tree plotted on map
- Tree status: "Available"

#### Part B: Validate GPS Within Boundary

**Test Invalid GPS (should fail):**

1. Click "Add New Tree" again
2. Enter coordinates outside boundary:
   - Latitude: `0.0`
   - Longitude: `100.0`
3. Try to submit

**✓ Expected Result:**
- Error message: "Coordinates are outside land boundary"
- Tree not added
- Map shows error indicator

---

#### Part C: Bulk Import via CSV

**Steps:**

1. Go back to tree management
2. Click "Bulk Import" button
3. Bulk import page loads

**Download Template:**

4. Click "Download CSV Template"
5. Template file downloads: `tree_import_template.csv`
6. Open in spreadsheet or text editor

**Prepare CSV File:**

Create file `test_trees.csv` with 10 trees:

```csv
species,latitude,longitude,planted_date,height,diameter,notes
Mahogany,-2.5490,140.7185,2024-01-16,2.2,0.25,Good growth condition
Teak,-2.5491,140.7186,2024-01-17,1.9,0.22,Needs monitoring
Bamboo,-2.5492,140.7187,2024-01-18,3.1,0.18,Fast growing specimen
Mahogany,-2.5493,140.7188,2024-01-19,2.8,0.32,Excellent health
Teak,-2.5494,140.7189,2024-01-20,2.0,0.24,Strong trunk
Bamboo,-2.5495,140.7190,2024-01-21,2.9,0.16,Dense foliage
Mahogany,-2.5496,140.7191,2024-01-22,2.4,0.28,Well established
Teak,-2.5497,140.7192,2024-01-23,1.8,0.21,Young tree
Bamboo,-2.5498,140.7193,2024-01-24,3.3,0.19,Mature specimen
Mahogany,-2.5499,140.7194,2024-01-25,2.6,0.30,Healthy and strong
```

**Important**: Adjust GPS coordinates to be within your land boundary polygon!

**Upload CSV:**

7. Click "Upload CSV File"
8. Select prepared `test_trees.csv`
9. Click "Upload and Validate"

**✓ Expected Result:**
- Upload progress shown
- Validation starts
- If valid: "10 trees ready to import"
- Preview of first 5 trees shown
- Click "Confirm Import"

**Validation Checks (system performs):**
- ✅ Species exist in land's species list
- ✅ GPS coordinates within boundary
- ✅ No duplicate coordinates
- ✅ Dates not in future
- ✅ All required fields present

10. Click "Confirm and Import Trees"
11. Progress bar shows import status

**✓ Expected Result:**
- Success: "10 trees imported successfully"
- Redirect to tree list
- Tree count: 11 trees (1 manual + 10 bulk)
- All trees have QR codes
- All trees plotted on map

---

#### Part D: Verify Tree List

1. View tree table

**✓ Expected Result:**
- Shows 11 trees
- Table columns:
  - Tree ID
  - Species
  - Status (all "Available")
  - Planted Date
  - Location (lat, long)
  - Actions (View, Edit, Download QR, Delete)

**Test Filters:**
- Filter by species: "Mahogany" → Shows 5 trees
- Filter by status: "Available" → Shows all 11
- Clear filters

**Test Search:**
- Search by species name
- Search by tree ID

**Test Actions:**
- Click "View" → Opens tree detail modal
- Click "Download QR" → Downloads QR code image
- Click "Edit" → Opens edit form, can update measurements

---

### Scenario 10: Merchant Earnings and Payout

**Objective**: Test merchant earning tracking and payout request

**Prerequisites**: Merchant with trees available for adoption

**Setup**: Need donor to adopt tree from merchant land first

**Steps:**

#### 1. Create Adoption (as Donor)

1. Logout from merchant
2. Login as donor (or create new donor)
3. Navigate to `/explore`
4. Find merchant's land: "Test Rainforest Conservation Area"
5. Adopt one tree (Donation tier $8)
6. Complete payment

**✓ Expected Result:**
- Adoption successful
- Payment completed

#### 2. Check Merchant Earnings (as Merchant)

1. Logout from donor
2. Login as merchant
3. Navigate to `/merchant/dashboard`

**✓ Expected Result:**
- Stats updated:
  - Total Adoptions: 1
  - Total Earnings: $6.40 (80% of $8, after 20% commission)
  - Trees Available: 10 (one adopted)
- Recent activity shows adoption event

#### 3. View Earnings Details

1. Click "Earnings & Payouts" tab
2. Earnings dashboard loads

**✓ Expected Result:**
- Summary cards:
  - Total Earnings: $6.40
  - Available Balance: $6.40
  - Total Payouts: $0
  - Pending Payouts: 0
- Transaction history shows:
  - Adoption ID
  - Buyer name
  - Tree details
  - Your earnings: $6.40
  - Date
  - Status: Completed

#### 4. Add Bank Account (First Time)

1. Click "Bank Account Settings"
2. Add bank account form

**Fill Bank Details:**
- Bank Name: Select "Bank Mandiri"
- Account Number: `1234567890`
- Account Holder Name: `Test Conservation Initiative` (must match business name)
- Branch Code: `001` (if required)

3. Click "Save Bank Account"

**✓ Expected Result:**
- Success: "Bank account added"
- Account appears in list (masked: `****7890`)
- Can add multiple accounts

#### 5. Request Payout

**Note**: In this test scenario, balance is $6.40 which is below minimum $50. For testing, admin can manually adjust or wait for more adoptions.

**Workaround for Testing:**
1. Have admin temporarily lower minimum payout to $5 (if possible)
2. OR create multiple test adoptions to reach $50

**Assuming balance ≥ $50:**

1. Click "Request Payout" button
2. Payout form appears

**Fill Payout Request:**
- Amount: Enter available balance (e.g., $50.00)
- Select Bank Account: Choose saved account
- Note (optional): `Test payout - December 2024`

3. Review payout summary:
   - Amount: $50.00
   - Bank: Bank Mandiri (****7890)
   - Processing fee: $0 (or as configured)
   - Net amount: $50.00

4. Click "Submit Payout Request"

**✓ Expected Result:**
- Success: "Payout request submitted"
- Payout status: "Pending"
- Available balance reduced by payout amount
- Payout appears in "Payout History" table

#### 6. Track Payout Status

1. Go to "Payout History" tab

**✓ Expected Result:**
- Shows payout request:
  - Request ID
  - Amount: $50.00
  - Status: Pending
  - Requested Date
  - Expected Completion: 3-5 business days
  - Bank account (masked)

#### 7. Admin Processes Payout (SuperAdmin)

1. Logout as merchant
2. Login as SuperAdmin
3. Navigate to admin payout management (if implemented)
4. Find pending payout
5. Review details
6. Approve payout

**✓ Expected Result:**
- Payout status → Processing
- Merchant receives email notification
- After processing: Status → Completed

#### 8. Verify Completed Payout (as Merchant)

1. Login as merchant
2. Check payout history

**✓ Expected Result:**
- Payout status: Completed
- Completion date shown
- Reference number provided
- Can download payout statement

---

## Admin Test Scenarios

### Scenario 11: Admin Dashboard and System Monitoring

**Objective**: Test admin dashboard statistics and monitoring

**Prerequisites**: SuperAdmin access

**Steps:**

#### 1. Login as SuperAdmin

1. Navigate to `/auth/login`
2. Email: `admin@adoptreeworld.com`
3. Password: `Admin123!`
4. Click "Sign In"

**✓ Expected Result:**
- Login successful
- Redirect to `/admin` dashboard
- Admin navigation menu appears

#### 2. Verify Dashboard Statistics

**Main Statistics (check all cards load):**

**✓ Expected Results:**

1. **Total Users**
   - Shows number > 0
   - Shows "+X new this week"
   - Click card → Navigates to user list

2. **Total Donors**
   - Shows donor count
   - Click → Filters users by "Donor" role

3. **Merchants**
   - Shows merchant count
   - Badge shows pending count (if any)
   - Click → Navigates to merchant management

4. **Total Revenue**
   - Shows revenue in USD
   - "This month: $X" comparison
   - Click → Navigates to transactions

5. **Conservation Lands**
   - Shows land count
   - Total area in hectares
   - Click → Views land list

6. **Trees Available**
   - Available vs Adopted split
   - Adoption rate percentage
   - Click → Views tree statistics

7. **Total Adoptions**
   - All-time count
   - "+X today" indicator
   - Click → Adoption history

8. **Today's Activity**
   - New signups count
   - Today's adoptions
   - Merchant applications

**Verify all stats are numeric and links work.**

#### 3. Check Quick Actions Panel

**✓ Expected Result:**
- "Pending Merchant Approvals" section visible
- Shows list of pending merchants (if any)
- Quick action buttons work
- Recent activity feed updates

#### 4. System Health Monitoring

1. Navigate to `/admin/system` (if available)

**✓ Expected Result:**
- Service status indicators:
  - Database: 🟢 Healthy
  - Redis Cache: 🟢 Healthy
  - Email Service: 🟢 Healthy
  - Payment Gateway: 🟢 Healthy
  - File Storage: 🟢 Healthy
- Connection stats displayed
- System metrics visible

---

### Scenario 12: Merchant Approval Workflow

**Objective**: Test complete merchant approval process

**Prerequisites**: Pending merchant application (from Scenario 7)

**Steps:**

#### 1. Navigate to Merchant Management

1. From admin dashboard
2. Click "Merchants" in sidebar
3. Or go directly to `/admin/merchants`

**✓ Expected Result:**
- Merchant list loads
- Shows all merchants with filters

#### 2. Filter Pending Merchants

1. Click filter dropdown
2. Select "Pending"

**✓ Expected Result:**
- Shows only pending applications
- Badge shows count
- Table updates

#### 3. Review Merchant Application

1. Click on pending merchant name
2. Merchant detail page loads

**✓ Expected Result:**
- Displays all merchant information:
  - Business Name
  - Tax ID
  - Registration Number
  - Contact details
  - Business description
  - Application date
  - Applicant user details

#### 4. Verify Business Information

**Verification Checklist (manual):**
- ✓ Business name looks legitimate
- ✓ Tax ID format correct
- ✓ Contact info complete
- ✓ No duplicate applications
- ✓ User email verified

**In real scenario**: Would verify with external sources

#### 5. Approve Merchant

1. Click "Approve Merchant" button
2. Confirmation modal appears
3. Confirm approval

**✓ Expected Result:**
- Success message: "Merchant approved successfully"
- Merchant status → Verified
- Approval email sent to merchant
- Redirect to merchant list
- Merchant removed from "Pending" filter
- Appears in "Verified" filter

#### 6. Test Rejection (with another merchant)

**If you have another pending merchant:**

1. Click on pending merchant
2. Click "Reject Merchant" button
3. Rejection reason modal appears

**Fill Rejection Reason:**
- Select reason: "Invalid business documents"
- Detailed explanation: `The business registration number could not be verified with the national registry. Please provide valid documentation and reapply.`

4. Confirm rejection

**✓ Expected Result:**
- Merchant status → Rejected
- Rejection email sent with reason
- Merchant can reapply after fixing issues
- Appears in "Rejected" filter

---

### Scenario 13: User Management and Moderation

**Objective**: Test user management actions

**Prerequisites**: SuperAdmin access, test users exist

**Steps:**

#### 1. Navigate to User Management

1. Click "Users" in admin sidebar
2. Or go to `/admin/users`

**✓ Expected Result:**
- User list loads
- Shows all users
- Filters available

#### 2. Filter Users by Role

1. Click role filter
2. Select "Donor"

**✓ Expected Result:**
- Shows only donor users
- Table updates
- Count displayed

#### 3. View User Details

1. Click on any user
2. User detail page loads

**✓ Expected Result:**
- Shows comprehensive user information:
  - User ID
  - Name, email, phone
  - Role and status
  - Registration date
  - Last login
  - Login method
  - Activity summary
  - Adoption count (if donor)
  - Activity timeline

#### 4. Test Update User Role

1. Click "Change Role" button
2. Role selection modal appears
3. Select new role: "Admin"
4. Confirm role change

**✓ Expected Result:**
- Success: "User role updated"
- User role → Admin
- User receives notification email
- Permissions recalculated

#### 5. Test Ban User

**⚠️ Important**: Don't ban test users you need!

1. Select a test user to ban
2. Click "Ban User" button
3. Ban reason modal appears

**Fill Ban Reason:**
- Select: "Terms of service violation"
- Explanation: `Test ban for demonstration purposes. This is a test user.`

4. Confirm ban

**✓ Expected Result:**
- User status → Banned
- User cannot login (test in incognito)
- Ban notification sent (optional)
- Sessions terminated

#### 6. Test Unban User

1. Filter users by status: "Banned"
2. Click on banned test user
3. Click "Unban User" button
4. Provide unban reason (optional)
5. Confirm unban

**✓ Expected Result:**
- User status → Active
- User can login again
- Unban notification sent
- Full access restored

#### 7. Test Manual Email Verification

1. Find user with unverified email
2. Click "Verify Email Manually"
3. Confirm action

**✓ Expected Result:**
- Email status → Verified
- Verification email sent as confirmation
- User can access all features

---

### Scenario 14: Transaction Monitoring

**Objective**: Test transaction list and verification

**Prerequisites**: Completed adoptions exist

**Steps:**

#### 1. Navigate to Transactions

1. Go to `/admin/transactions`

**✓ Expected Result:**
- Transaction list loads
- Shows all payments
- Filters available

#### 2. Filter Transactions

**Test Filters:**

1. **By Status:**
   - Select "Success" → Shows completed payments
   - Select "Pending" → Shows awaiting payment
   - Select "Failed" → Shows failed payments

2. **By Payment Method:**
   - Select "E-Wallet" → Shows GoPay, OVO, etc.
   - Select "Bank Transfer" → Shows VA payments

3. **By Date Range:**
   - Select "Last 7 days"
   - Select "Last 30 days"
   - Custom range

**✓ Expected Result:**
- Filters work correctly
- Table updates
- Counts displayed

#### 3. View Transaction Details

1. Click on any successful transaction
2. Transaction detail page loads

**✓ Expected Result:**
- Shows complete transaction info:
  - Order ID, Adoption ID
  - Amount (USD and IDR)
  - Payment method
  - Transaction status
  - User information
  - Midtrans details:
    - Transaction ID
    - Payment type
    - Bank/E-Wallet provider
    - Transaction timestamp
    - Settlement timestamp
  - Adoption details
  - Certificate status

#### 4. Download Invoice

1. Click "Download Invoice" button

**✓ Expected Result:**
- PDF invoice downloads
- Contains:
  - Order details
  - User information
  - Payment breakdown
  - Adoption list
  - Platform info

#### 5. Test Manual Payment Verification

**Scenario**: Webhook failed but payment actually successful

**Steps:**

1. Find transaction with "Pending" status
2. Verify in Midtrans dashboard (external) that payment succeeded
3. Click "Verify Payment Manually" button
4. Enter Midtrans transaction ID: `{midtrans_tx_id}`
5. Confirm verification

**✓ Expected Result:**
- Adoption status → Active
- Certificate generated
- User notified
- Transaction marked complete
- Merchant earnings updated

**⚠️ Warning**: Only use when certain payment succeeded!

---

## Corporate Test Scenarios

### Scenario 15: Corporate Registration and Team Management

**Objective**: Test corporate account setup and team invitations

**Prerequisites**: None

**Steps:**

#### 1. Corporate Registration

1. Navigate to `/corporate/register`
2. Fill corporate form:

**Company Details:**
- Company Name: `EcoTech Solutions Inc.`
- Industry: `Technology`
- Company Size: `51-200 employees`
- Registration Number: `CORP-123456`
- Address: `456 Innovation Drive, San Francisco, CA, USA`

**Contact Person:**
- Name: `Jane Corporate`
- Job Title: `Sustainability Director`
- Email: `jane@ecotech-test.com`
- Phone: `+1-415-555-0123`

**Subscription:**
- Select Tier: `Professional`
- Expected Monthly Adoptions: `50`

3. Submit application

**✓ Expected Result:**
- Application submitted
- Verification pending (3-5 days message)
- Email confirmation sent

#### 2. Admin Approves Corporate (SuperAdmin)

1. Login as SuperAdmin
2. Navigate to corporate applications (if separate from merchant)
3. Review "EcoTech Solutions Inc." application
4. Approve application

**✓ Expected Result:**
- Corporate account activated
- Login credentials sent to Jane
- Account provisioned with Professional tier features

#### 3. Login to Corporate Dashboard

1. Login as jane@ecotech-test.com
2. Navigate to `/corporate/dashboard`

**✓ Expected Result:**
- Corporate dashboard loads
- Role: Owner
- Stats show:
  - Team Members: 1 (just owner)
  - Trees Adopted: 0
  - Environmental Impact: 0
  - Team Activity: empty

#### 4. Invite Team Member

1. Go to "Team" tab
2. Click "Invite Team Member"

**Invitation Details:**
- Email: `john.member@ecotech-test.com`
- Role: `Member`
- Personal Message: `Welcome to our sustainability team!`

3. Click "Send Invitation"

**✓ Expected Result:**
- Invitation sent
- Appears in "Pending Invitations" list
- Email sent to john.member@ecotech-test.com

#### 5. Member Accepts Invitation

1. Check john's email inbox
2. Open invitation email
3. Click "Accept Invitation"
4. **If John has no account:**
   - Register first: john.member@ecotech-test.com
   - Then accept invitation
5. **If John has account:**
   - Login
   - Accept invitation

**✓ Expected Result:**
- John linked to corporate account
- Role: Member
- Can access corporate dashboard
- Welcome email sent

#### 6. Verify Team List

1. Login as Jane (Owner)
2. Go to "Team" tab

**✓ Expected Result:**
- Shows 2 team members:
  - Jane Corporate (Owner)
  - John Member (Member)
- Can view roles
- Can change John's role
- Can remove John (but not remove self as Owner)

---

### Scenario 16: Bulk Adoption with Employee Gifting

**Objective**: Test corporate bulk adoption and employee gifting

**Prerequisites**: Corporate account with team access

**Steps:**

#### 1. Navigate to Bulk Adoptions

1. Login as corporate admin or member
2. Go to "Bulk Adoptions" tab
3. Click "Create Bulk Adoption"

#### 2. Select Trees for Bulk Adoption

1. Browse available lands
2. Select land: Any available land
3. Select species: Mahogany
4. Select tier: `GreenSociety ($12)`
5. Enter quantity: `50` trees
6. Total displayed: $600

**✓ Expected Result:**
- Land details shown
- Product tier selected
- Quantity accepted (minimum 10)
- Total calculated correctly

#### 3. Enable Employee Gifting

1. Toggle "Gift to Employees" ON
2. Employee CSV upload section appears

#### 4. Download and Prepare Employee CSV

1. Click "Download Template"
2. Template downloads: `employee_template.csv`
3. Create `test_employees.csv`:

```csv
name,email
John Doe,john.doe@ecotech-test.com
Jane Smith,jane.smith@ecotech-test.com
Bob Johnson,bob.j@ecotech-test.com
Alice Williams,alice.w@ecotech-test.com
Charlie Brown,charlie.b@ecotech-test.com
```

**Note**: Add 50 employees to match 50 trees (or adjust quantity to match CSV)

#### 5. Upload Employee CSV

1. Click "Upload CSV"
2. Select `test_employees.csv`
3. Click "Upload and Validate"

**✓ Expected Result:**
- Validation runs
- Success: "50 employees ready for gifting"
- Preview shows first 5 employees
- Count matches tree quantity

#### 6. Review and Complete Payment

1. Review bulk adoption summary:
   - 50 trees
   - 50 employees
   - Total: $600
2. Select payment method: Corporate invoice or card
3. Complete payment

**✓ Expected Result:**
- Payment successful
- Bulk adoption created
- Processing message shown

#### 7. Verify Processing

**System Processing (takes 1-5 minutes):**
- Creates 50 individual adoptions
- Generates 50 certificates
- Sends 50 gift emails

**✓ Expected Result:**
- Bulk adoption status: "Processing" → "Completed"
- Success notification

#### 8. Check Employee Gift Emails

1. Check each employee's email inbox
2. Open "Tree Adoption Gift from EcoTech Solutions" email

**✓ Expected Result:**
- Each employee receives personalized email:
  - Gift message
  - Tree details (species, land)
  - Certificate link
  - Download PDF button
  - Company branding (if configured)

#### 9. Employee Views Certificate

1. Employee clicks certificate link (no login)
2. Public certificate page loads

**✓ Expected Result:**
- Shows certificate
- "Adopted by [Employee Name]"
- "Gift from EcoTech Solutions"
- Tree details
- Can download PDF
- Optional: Create account to track tree

---

## Edge Case Testing

### Scenario 17: Error Handling and Edge Cases

**Objective**: Test application behavior under error conditions

#### Test 1: Invalid Email Verification Token

**Steps:**
1. Go to `/auth/verify?token=invalid_token_12345`
2. Submit verification

**✓ Expected Result:**
- Error: "Invalid or expired verification token"
- Link to request new verification email
- Graceful error handling (no crash)

---

#### Test 2: Expired Password Reset Token

**Steps:**
1. Initiate password reset
2. Wait for token expiration (or manually expire in DB if possible)
3. Try to use expired token

**✓ Expected Result:**
- Error: "Reset token expired"
- Link to request new reset
- No security vulnerability

---

#### Test 3: Payment Timeout

**Steps:**
1. Add tree to cart and proceed to checkout
2. Complete checkout to payment step
3. In Midtrans, wait for timeout (don't pay)
4. Close Midtrans popup

**✓ Expected Result:**
- Adoption status remains "PaymentPending"
- Can retry payment later
- Order not lost
- No duplicate charges

---

#### Test 4: Duplicate Coordinate Import

**Steps:**
1. Bulk import CSV with duplicate GPS coordinates:
```csv
species,latitude,longitude,planted_date
Mahogany,-2.5489,140.7184,2024-01-15
Teak,-2.5489,140.7184,2024-01-16
```
2. Upload CSV

**✓ Expected Result:**
- Validation error: "Duplicate coordinates found"
- Error report shows which rows
- Import blocked
- Clear error message

---

#### Test 5: GPS Outside Boundary

**Steps:**
1. Try to add tree with GPS far outside land boundary
2. Submit tree

**✓ Expected Result:**
- Error: "Coordinates outside land boundary"
- Map shows error location
- Tree not added
- Can correct and retry

---

#### Test 6: Future Planted Date

**Steps:**
1. Try to add tree with planted date in future
2. Submit tree

**✓ Expected Result:**
- Validation error: "Planted date cannot be in future"
- Date field highlighted
- Tree not added

---

#### Test 7: Unauthorized Access Attempts

**Steps:**
1. **As regular user**, try to access:
   - `/admin` → Should redirect to dashboard with error
   - `/merchant/dashboard` (if not merchant) → Should redirect with error

2. **As donor**, try to access:
   - Admin routes → Blocked
   - Merchant routes → Blocked

**✓ Expected Result:**
- Access denied
- Redirect to appropriate page
- Error toast: "Unauthorized access"
- No sensitive data exposed

---

#### Test 8: Concurrent Cart Modifications

**Steps:**
1. Open site in two browser tabs
2. Add tree to cart in Tab 1
3. Add different tree to cart in Tab 2
4. View cart in both tabs

**✓ Expected Result:**
- Cart syncs across tabs (if using cookies/localStorage)
- OR shows most recent state
- No cart corruption
- Quantities correct

---

#### Test 9: Large File Upload

**Steps:**
1. Try to upload image > 5MB as avatar or land photo
2. Submit upload

**✓ Expected Result:**
- Error: "File too large (max 5MB)"
- Upload rejected
- Clear error message
- Can compress and retry

---

#### Test 10: Special Characters in Input

**Steps:**
1. Try registering with name: `<script>alert('XSS')</script>`
2. Try creating land with description containing SQL: `'; DROP TABLE lands;--`

**✓ Expected Result:**
- Input sanitized
- No XSS execution
- No SQL injection
- Special characters escaped or rejected
- Application secure

---

## Performance Testing

### Scenario 18: Load and Performance Tests

**Objective**: Verify application performance under load

#### Test 1: Page Load Times

**Measure load times for key pages:**

1. **Landing Page** (`/`)
   - ✓ Target: < 3 seconds
   - Measure: Use browser DevTools Network tab

2. **Dashboard** (`/dashboard`)
   - ✓ Target: < 2 seconds after login
   - Check: Stats cards load

3. **Explore Lands** (`/explore`)
   - ✓ Target: < 3 seconds
   - Check: Land grid renders

4. **Checkout** (`/checkout`)
   - ✓ Target: < 2 seconds
   - Check: Payment methods load

**✓ Expected Result:**
- All pages load within targets
- No excessive API calls
- Images optimized
- Lazy loading works

---

#### Test 2: Map Rendering Performance

**Steps:**
1. Navigate to land with 100+ trees
2. View map with all trees plotted
3. Zoom in/out
4. Pan around

**✓ Expected Result:**
- Map renders smoothly
- No lag with 100+ markers
- Clustering works (if implemented)
- Zoom/pan responsive

---

#### Test 3: Large CSV Import

**Steps:**
1. Create CSV with 1000 trees (maximum)
2. Upload to bulk import
3. Monitor processing time

**✓ Expected Result:**
- Import completes in < 2 minutes
- Progress indicator shows status
- No timeout errors
- All trees imported correctly

---

#### Test 4: Dashboard with Large Data

**Scenario**: User with 100+ adoptions

**Steps:**
1. Create test user (or use existing)
2. Adopt 100+ trees (via script or manual)
3. View dashboard

**✓ Expected Result:**
- Dashboard loads without timeout
- Stats calculate correctly
- Map handles 100+ markers
- Pagination works (if implemented)
- No memory leaks

---

## Security Testing

### Scenario 19: Security Checks

**Objective**: Verify security measures

#### Test 1: Password Strength Enforcement

**Steps:**
1. Try registering with weak passwords:
   - `123` → Rejected
   - `password` → Rejected
   - `abcd1234` → Rejected (no uppercase)
   - `Abcd1234` → Accepted ✓

**✓ Expected Result:**
- Weak passwords rejected
- Clear requirements shown:
  - Minimum 8 characters
  - At least 1 uppercase
  - At least 1 lowercase
  - At least 1 number
  - Optional: 1 special character

---

#### Test 2: SQL Injection Prevention

**Steps:**
1. Try SQL injection in search fields:
   - Land search: `' OR '1'='1`
   - User search: `admin'--`
   - Email: `test@test.com' OR '1'='1`

**✓ Expected Result:**
- Input sanitized
- No SQL errors
- No unauthorized data access
- Application secure

---

#### Test 3: XSS Prevention

**Steps:**
1. Try XSS in text inputs:
   - Name: `<script>alert('XSS')</script>`
   - Bio: `<img src=x onerror=alert('XSS')>`
   - Review: `<iframe src="javascript:alert('XSS')"></iframe>`

**✓ Expected Result:**
- Scripts not executed
- Input escaped/sanitized
- Rendered as plain text
- No XSS vulnerability

---

#### Test 4: CSRF Protection

**Steps:**
1. Try making state-changing requests from external site
2. Check CSRF tokens in forms

**✓ Expected Result:**
- CSRF tokens required for POST/PUT/DELETE
- Invalid tokens rejected
- Cross-origin requests blocked (unless CORS configured)

---

#### Test 5: API Rate Limiting

**Steps:**
1. Make rapid API requests (100+ per minute)
2. Monitor responses

**✓ Expected Result:**
- Rate limiting kicks in
- Returns 429 Too Many Requests
- Retry-After header present
- Normal requests resume after cooldown

---

#### Test 6: Sensitive Data Exposure

**Steps:**
1. Check API responses for sensitive data:
   - Password hashes
   - API secrets
   - Internal IDs that shouldn't be public

2. Check browser console for logs with sensitive data

**✓ Expected Result:**
- No passwords in responses (even hashed)
- API keys not exposed
- Minimal data exposure
- Logs sanitized

---

## Pre-Launch Checklist

### Final Verification Before Production

**Complete this checklist before deploying to production:**

#### Authentication & Authorization
- [ ] All 4 registration methods work (Email, Google OAuth, Wallet, Guest)
- [ ] Email verification sends and verifies correctly
- [ ] Password reset flow complete and secure
- [ ] OAuth doesn't expose tokens in URL
- [ ] Role-based access control enforced
- [ ] Unauthorized access properly blocked

#### Core Features - Donor
- [ ] Can browse lands without login
- [ ] Can add trees to cart
- [ ] Checkout process works (all 3 steps)
- [ ] All 6 payment methods functional
- [ ] Certificates generate correctly
- [ ] QR codes verify publicly
- [ ] Dashboard stats accurate
- [ ] Gift adoptions send notifications
- [ ] Guest checkout and upgrade works

#### Core Features - Merchant
- [ ] Merchant registration submits successfully
- [ ] Merchant approval workflow complete
- [ ] Can create lands with boundaries
- [ ] Boundary validation works (GPS within polygon)
- [ ] Can add individual trees
- [ ] Bulk CSV import validates properly
- [ ] QR codes generate for all trees
- [ ] Earnings calculate correctly (80% commission)
- [ ] Payout requests submit
- [ ] Bank account management works

#### Core Features - Admin
- [ ] Admin dashboard loads all stats
- [ ] Merchant approval/rejection works
- [ ] User management actions work (ban, unban, role change)
- [ ] Transaction monitoring functional
- [ ] Manual payment verification works
- [ ] Content moderation (hide/show/delete reviews)
- [ ] System health monitoring works
- [ ] All admin actions logged

#### Core Features - Corporate
- [ ] Corporate registration submits
- [ ] Team invitations send and accept
- [ ] Role management works
- [ ] Bulk adoptions create successfully
- [ ] Employee CSV import validates
- [ ] Employee gift emails sent to all
- [ ] ESG reports generate with correct data
- [ ] API credentials create and work

#### Payments & Transactions
- [ ] Midtrans integration working (all methods)
- [ ] Webhook signature verification works
- [ ] Payment status updates correctly
- [ ] Failed payments handled gracefully
- [ ] Refunds process correctly (if implemented)
- [ ] No duplicate charges possible
- [ ] Payment timeout handled

#### Data & Files
- [ ] Avatar upload works and displays
- [ ] Land photos upload and display
- [ ] Tree photos upload and display
- [ ] PDF certificates generate correctly
- [ ] QR codes render and scan
- [ ] CSV templates download
- [ ] CSV import validates thoroughly
- [ ] File size limits enforced (5MB)

#### Performance
- [ ] All pages load < 3 seconds
- [ ] Dashboard handles 100+ adoptions
- [ ] Map renders 100+ trees smoothly
- [ ] Bulk import handles 1000 trees
- [ ] No memory leaks observed
- [ ] Images optimized and lazy-loaded

#### Security
- [ ] Password strength enforced
- [ ] SQL injection prevented
- [ ] XSS attacks prevented
- [ ] CSRF tokens implemented
- [ ] API rate limiting works
- [ ] Sensitive data not exposed
- [ ] HTTPS enforced
- [ ] Secure headers configured

#### Email Notifications
- [ ] Verification emails sent
- [ ] Password reset emails sent
- [ ] Adoption confirmation emails sent
- [ ] Gift adoption emails sent
- [ ] Merchant approval emails sent
- [ ] Payout status emails sent
- [ ] All emails have correct branding
- [ ] Email links work correctly

#### Mobile Responsiveness
- [ ] Landing page responsive
- [ ] Registration/login responsive
- [ ] Dashboard responsive
- [ ] Explore lands responsive
- [ ] Cart and checkout responsive
- [ ] Admin panel responsive (if needed)
- [ ] Certificates display on mobile

#### Error Handling
- [ ] Invalid URLs show 404 page
- [ ] Server errors show 500 page (user-friendly)
- [ ] Form validation shows clear messages
- [ ] API errors handled gracefully
- [ ] Network errors handled
- [ ] Timeout errors handled

#### Browser Compatibility
- [ ] Works on Chrome (latest)
- [ ] Works on Firefox (latest)
- [ ] Works on Safari (latest)
- [ ] Works on Edge (latest)
- [ ] Works on mobile browsers

#### Monitoring & Logging
- [ ] Application errors logged
- [ ] Payment events logged
- [ ] Admin actions logged (audit trail)
- [ ] System health monitored
- [ ] Error tracking configured (Sentry, etc.)

---

## Testing Notes Template

**Use this template to document test results:**

```markdown
# Test Session: [Date]

**Tester**: [Name]
**Environment**: Staging
**Browser**: Chrome 120.0.0

## Tests Completed
- [x] Scenario 1: Complete Adoption Journey
- [x] Scenario 2: Google OAuth
- [ ] Scenario 3: Guest Checkout
- [ ] Scenario 4: Gift Adoption

## Issues Found

### Issue 1: Certificate PDF not downloading
**Severity**: High
**Steps to Reproduce**:
1. Complete adoption
2. Go to certificates page
3. Click "Download PDF"

**Expected**: PDF downloads
**Actual**: 404 error

**Screenshot**: [attach screenshot]
**Console Errors**: [paste errors]

### Issue 2: Cart total calculation wrong
**Severity**: Medium
**Steps to Reproduce**:
1. Add 2 trees to cart ($8 each)
2. Change quantity of first to 3
3. Check total

**Expected**: $32 (3×$8 + 1×$8)
**Actual**: $24

---

## Performance Metrics
- Landing page load: 2.1s ✓
- Dashboard load: 1.8s ✓
- Checkout load: 2.3s ✓
- Map with 50 trees: Smooth ✓

---

## Next Steps
1. Report Issue 1 to development team
2. Retest after fix
3. Continue with Scenario 3
```

---

## Conclusion

This testing guide covers comprehensive end-to-end scenarios for all user roles. Use it to:
- Verify functionality before deployment
- Train new team members
- Document test coverage
- Identify and report bugs

**Remember:**
- Always test on staging, never production
- Document all issues found
- Retest after fixes
- Update this guide as features evolve

**Need Help?**
- Technical Issues: Email dev team
- SuperAdmin Access: admin@adoptreeworld.com / Admin123!
- Staging URL: https://staging.adoptreeworld.com/

**Related Documentation:**
- [Feature Status Report](FEATURE-STATUS.md) - What's implemented
- [Donor Guide](DONOR-GUIDE.md) - Donor workflows
- [Merchant Guide](MERCHANT-GUIDE.md) - Merchant workflows
- [Admin Guide](ADMIN-GUIDE.md) - Admin workflows
- [Corporate Guide](CORPORATE-GUIDE.md) - Corporate workflows

---

*Happy Testing! 🧪🌳*
