# Portal Landing Page - Deployment Instructions

## Overview
This document provides instructions for deploying the new Portal Landing page authentication fix to the live Wix site.

## Files Changed
1. **src/pages/PortalLanding.js** - New file with authentication handlers
2. **src/backend/accessCodes.jsw** - New backend module for access code validation

## Deployment Steps

### Step 1: Access Wix Editor
1. Log in to Wix at https://manage.wix.com
2. Navigate to the Shamrock Bail Bonds site (shamrockbailbonds.biz)
3. Click "Edit Site" to open the Wix Editor

### Step 2: Locate the Portal Landing Page
1. In the Wix Editor, open the Pages panel (left sidebar)
2. Find the "Portal Landing" or "Client Access Portal" page
3. If it doesn't exist, create a new page with URL slug: `/portal-landing`

### Step 3: Add the Page Code
1. Click on the page in the Pages panel
2. Click the "Page Code" icon (looks like `</>`) in the top toolbar
3. Replace any existing code with the contents of `src/pages/PortalLanding.js`
4. Save the page code

### Step 4: Add the Backend Module
1. In the left sidebar, click on "Code Files" (Developer Tools section)
2. Navigate to "Backend" folder
3. If `accessCodes.jsw` doesn't exist, create it:
   - Click the "+" button
   - Select "Backend" → "New .jsw file"
   - Name it `accessCodes`
4. Replace the contents with `src/backend/accessCodes.jsw`
5. Save the file

### Step 5: Verify Element IDs
The code expects these specific element IDs on the portal-landing page:

**Access Code Form:**
- `#comp-mjrvbswh` - Access code input field (TextInput)
- `#comp-mjrvd6m8` - Submit button

**Portal Buttons:**
- `#comp-mjrynime` - Defendant Portal button
- `#comp-mjrynk22` - Indemnitor Portal button  
- `#comp-mjryn7jm` - Staff Portal button

To verify:
1. Click on each element in the Wix Editor
2. Check the element ID in the Properties panel (top right)
3. If IDs don't match, either:
   - Update the element IDs in the editor to match the code, OR
   - Update the code to match the existing element IDs

### Step 6: Set Up Database Collections (If Not Already Done)

The backend code expects two Wix Data collections:

**Collection 1: AccessCodes**
Fields:
- `accessCode` (Text) - The unique access code
- `caseId` (Text) - Reference to the case
- `portalType` (Text) - "defendant", "indemnitor", or "staff"
- `expiresAt` (Date & Time) - When the code expires
- `isActive` (Boolean) - Whether the code is active
- `createdAt` (Date & Time) - When the code was created

**Collection 2: AccessCodeUsage** (for analytics)
Fields:
- `accessCode` (Text) - The code that was used
- `caseId` (Text) - Associated case
- `usedAt` (Date & Time) - When it was used
- `ipAddress` (Text) - Optional
- `userAgent` (Text) - Optional

To create collections:
1. Open the Database panel (left sidebar)
2. Click "Add a Collection"
3. Name it exactly as shown above
4. Add the fields with the correct types
5. Set permissions appropriately (read/write from backend only)

### Step 7: Configure Member Roles

The authentication system uses Wix Members roles:
- `Defendant` - For defendants accessing their portal
- `Indemnitor` - For indemnitors managing payments
- `Staff` - For staff managing cases

To set up roles:
1. Go to Settings → Member Permissions
2. Create these three roles if they don't exist
3. Assign appropriate permissions to each role

### Step 8: Create Dashboard Pages

The code redirects to these member dashboard pages:
- `/members/defendant-dashboard`
- `/members/indemnitor-dashboard`
- `/members/staff-dashboard`

Ensure these pages exist or update the redirect URLs in `PortalLanding.js`.

### Step 9: Test the Authentication Flow

1. **Test Portal Buttons (Not Logged In):**
   - Visit https://shamrockbailbonds.biz/portal-landing
   - Click "Access Portal" button for Defendant
   - Should see Wix login modal
   - Cancel and verify no errors

2. **Test Portal Buttons (Logged In):**
   - Log in as a test user with "Defendant" role
   - Click "Access Portal" button for Defendant
   - Should redirect to `/members/defendant-dashboard`

3. **Test Access Code:**
   - Create a test access code in the AccessCodes collection
   - Enter it in the access code field
   - Click Submit
   - Should validate and redirect appropriately

### Step 10: Publish the Site

1. Click "Publish" in the top right corner
2. Wait for the site to publish
3. Test on the live site at https://shamrockbailbonds.biz/portal-landing

## Troubleshooting

### Portal buttons don't respond
- Check browser console for JavaScript errors
- Verify element IDs match the code
- Ensure page code is saved and published

### Login modal doesn't appear
- Check that `wix-members-frontend` module is available
- Verify Wix Members is enabled for the site
- Check browser console for errors

### Access code validation fails
- Verify `AccessCodes` collection exists with correct fields
- Check that backend file `accessCodes.jsw` is saved
- Look for errors in the Wix Console (Site Monitoring)

### Redirects don't work
- Verify dashboard pages exist at the specified URLs
- Check that member roles are configured correctly
- Ensure user has the appropriate role assigned

## Additional Notes

### Authentication Flow
1. User clicks portal button
2. System checks if user is logged in
3. If not logged in → Show Wix login modal
4. If logged in → Verify role matches portal type
5. If role matches → Redirect to dashboard
6. If role doesn't match → Show error message

### Access Code Flow
1. User enters access code
2. System validates against AccessCodes collection
3. If valid → Store case info in session storage
4. Redirect to appropriate portal based on portalType

### Session Storage
The code uses session storage to maintain state:
- `portal_redirect` - Intended destination after login
- `portal_type` - Type of portal being accessed
- `case_id` - Associated case ID
- `access_code` - The validated access code

## Support

For issues or questions, contact the development team or refer to:
- Wix Velo Documentation: https://dev.wix.com/docs/velo
- Wix Members API: https://dev.wix.com/docs/velo/api-reference/wix-members-frontend
- Project GitHub: https://github.com/WTFlorida239/shamrock-bail-portal-frontend
