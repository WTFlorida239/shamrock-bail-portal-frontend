# Quick Deployment Guide - Portal Authentication Fix

## 🚨 IMMEDIATE ACTION REQUIRED

The portal buttons on `/portal-landing` are not working because there's no JavaScript code attached to the page. I've created the fix - you just need to deploy it to Wix.

---

## ⚡ FASTEST PATH TO FIX (5 minutes)

### Step 1: Open Wix Editor
1. Go to https://manage.wix.com
2. Log in with your credentials
3. Select the Shamrock Bail Bonds site
4. Click **"Edit Site"**

### Step 2: Find the Portal Landing Page
1. In the left sidebar, click **"Pages & Menu"** icon
2. Find the page called **"Portal Landing"** or **"Client Access Portal"**
3. Click on it to select it

### Step 3: Add the Page Code
1. Click the **"Page Code"** icon in the top toolbar (looks like `</>`)
2. Delete any existing code in the editor
3. Copy the entire contents of this file:
   - **Location:** `shamrock-bail-portal-frontend/src/pages/PortalLanding.js`
   - **GitHub:** https://github.com/WTFlorida239/shamrock-bail-portal-frontend/blob/main/src/pages/PortalLanding.js
4. Paste it into the Wix code editor
5. Click **"Save"**

### Step 4: Add the Backend Code
1. In the left sidebar, click **"Code Files"** (under Developer Tools)
2. Click the **"+"** button
3. Select **"Backend"** → **"New .jsw file"**
4. Name it **`accessCodes`**
5. Copy the entire contents of this file:
   - **Location:** `shamrock-bail-portal-frontend/src/backend/accessCodes.jsw`
   - **GitHub:** https://github.com/WTFlorida239/shamrock-bail-portal-frontend/blob/main/src/backend/accessCodes.jsw
6. Paste it into the file
7. Click **"Save"**

### Step 5: Publish
1. Click **"Publish"** in the top right corner
2. Wait for deployment to complete
3. Test at https://shamrockbailbonds.biz/portal-landing

---

## ✅ WHAT THIS FIX DOES

**Before:** Clicking portal buttons → Nothing happens

**After:** Clicking portal buttons → 
- If not logged in → Shows Wix login modal
- If logged in → Verifies role and redirects to dashboard
- Tracks analytics for monitoring

---

## 🧪 QUICK TEST

After publishing:

1. Open **incognito/private browser window**
2. Go to https://shamrockbailbonds.biz/portal-landing
3. Click **"Access Portal"** under Defendant Portal
4. **You should see:** Wix login modal appears ✅
5. **Before the fix:** Nothing happened ❌

---

## 📋 ELEMENT IDs TO VERIFY

The code expects these specific element IDs. If buttons still don't work, verify these in the Wix Editor:

| Element | Expected ID | What It Is |
|---------|-------------|------------|
| Defendant Portal Button | `#comp-mjrynime` | Blue "Access Portal" button |
| Indemnitor Portal Button | `#comp-mjrynk22` | Blue "Access Portal" button |
| Staff Portal Button | `#comp-mjryn7jm` | Blue "Access Portal" button |
| Access Code Input | `#comp-mjrvbswh` | Text input field |
| Submit Button | `#comp-mjrvd6m8` | Blue "Submit" button |

**To check an element ID:**
1. Click on the element in Wix Editor
2. Look at the Properties panel (top right)
3. The ID is shown at the top

**If IDs don't match:**
- Option A: Change the element ID in Wix to match the code
- Option B: Update the code to match the existing IDs

---

## 🔧 TROUBLESHOOTING

### Buttons still don't work after deployment
1. **Check browser console** (F12 → Console tab)
2. Look for JavaScript errors
3. Most common issue: Element IDs don't match

### Login modal doesn't appear
1. Verify **Wix Members** is enabled for your site
2. Go to Settings → Members Area
3. Ensure "Enable Members" is turned on

### Can't find the page code option
1. Make sure you're in **Wix Editor** (not Wix Studio)
2. The page code icon is in the top toolbar
3. Looks like `</>` symbol

---

## 📦 FILES CREATED

All code has been pushed to GitHub:

```
Repository: WTFlorida239/shamrock-bail-portal-frontend
Branch: main
Commits: 3c54c7f, 69087f5

Files:
├── src/pages/PortalLanding.js          ← Page code (DEPLOY THIS)
├── src/backend/accessCodes.jsw         ← Backend code (DEPLOY THIS)
├── DEPLOYMENT_INSTRUCTIONS.md          ← Detailed guide
├── PORTAL_FIX_SUMMARY.md              ← Technical summary
└── QUICK_DEPLOYMENT_GUIDE.md          ← This file
```

---

## 🎯 NEXT STEPS AFTER DEPLOYMENT

Once the buttons work and show the login modal:

1. **Create Member Roles**
   - Go to Settings → Member Permissions
   - Create roles: `Defendant`, `Indemnitor`, `Staff`

2. **Create Dashboard Pages**
   - `/members/defendant-dashboard`
   - `/members/indemnitor-dashboard`
   - `/members/staff-dashboard`

3. **Verify MagicLinks Collection**
   - Ensure `MagicLinks` collection exists
   - Verify required fields: token, used, role, expiresAt
   - See MAGICLINKS_SCHEMA.md for complete documentation

4. **Integrate with GAS Dashboard**
   - Generate access codes when packets are created
   - Send codes to users via email/SMS
   - Include link to portal-landing page

---

## 📞 NEED HELP?

**Documentation:**
- Full deployment guide: `DEPLOYMENT_INSTRUCTIONS.md`
- Technical summary: `PORTAL_FIX_SUMMARY.md`
- GitHub repo: https://github.com/WTFlorida239/shamrock-bail-portal-frontend

**Wix Resources:**
- Wix Velo Docs: https://dev.wix.com/docs/velo
- Wix Members API: https://dev.wix.com/docs/velo/api-reference/wix-members-frontend

---

## ⏱️ TIME ESTIMATE

- **Deployment:** 5 minutes
- **Testing:** 2 minutes
- **Total:** 7 minutes to fix the issue

---

**Last Updated:** January 7, 2026
**Created By:** Manus AI Agent
**Status:** Ready to Deploy ✅
