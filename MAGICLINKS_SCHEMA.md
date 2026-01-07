# MagicLinks CMS Collection Schema

## Overview
The **MagicLinks** collection stores access codes (magic link tokens) that allow defendants, indemnitors, and staff to access their respective portals without traditional username/password authentication.

---

## Collection Name
**`MagicLinks`** (exact name, case-sensitive)

---

## Required Fields

### 1. **token** (Text)
- **Type:** Text
- **Required:** Yes
- **Unique:** Yes (recommended)
- **Description:** The unique access code/magic link token (e.g., "ABC12345")
- **Format:** 8-character alphanumeric string (uppercase letters and numbers)
- **Example:** `"XY3K9P2M"`

### 2. **used** (Boolean)
- **Type:** Boolean (Yes/No)
- **Required:** Yes
- **Default:** `false`
- **Description:** Indicates whether the magic link has been used
  - `false` = Active, can be used
  - `true` = Used/inactive, cannot be used again
- **Example:** `false`

### 3. **role** (Text)
- **Type:** Text
- **Required:** Yes
- **Description:** The portal type/user role for this magic link
- **Allowed Values:**
  - `"defendant"` - For defendants accessing their case
  - `"indemnitor"` - For indemnitors managing payments
  - `"staff"` - For staff managing cases
- **Example:** `"defendant"`

### 4. **expiresAt** (Date & Time)
- **Type:** Date & Time
- **Required:** Yes
- **Description:** When the magic link expires and can no longer be used
- **Format:** ISO 8601 date-time
- **Default:** 30 days from creation
- **Example:** `2026-02-06T10:30:00.000Z`

---

## Optional Fields (Recommended)

### 5. **caseId** (Text)
- **Type:** Text
- **Required:** No
- **Description:** Reference to the associated case/bond
- **Example:** `"case-2026-001234"`

### 6. **email** (Text)
- **Type:** Text
- **Required:** No
- **Description:** Email address associated with this magic link
- **Example:** `"john.doe@example.com"`

### 7. **createdAt** (Date & Time)
- **Type:** Date & Time
- **Required:** No
- **Description:** When the magic link was created
- **Format:** ISO 8601 date-time
- **Example:** `2026-01-07T10:30:00.000Z`

---

## Field Mapping (Code → Database)

| Code Variable | Database Field | Type | Description |
|--------------|----------------|------|-------------|
| `accessCode` | `token` | Text | The magic link token |
| `isActive` | `used` (inverted) | Boolean | `used: false` = active |
| `portalType` | `role` | Text | Portal/user role |
| `expiresAt` | `expiresAt` | Date | Expiration timestamp |
| `caseId` | `caseId` | Text | Case reference |
| `email` | `email` | Text | User email |
| `createdAt` | `createdAt` | Date | Creation timestamp |

---

## Example Records

### Example 1: Defendant Magic Link
```json
{
  "_id": "abc123",
  "token": "XY3K9P2M",
  "used": false,
  "role": "defendant",
  "expiresAt": "2026-02-06T10:30:00.000Z",
  "caseId": "case-2026-001234",
  "email": "john.doe@example.com",
  "createdAt": "2026-01-07T10:30:00.000Z"
}
```

### Example 2: Indemnitor Magic Link
```json
{
  "_id": "def456",
  "token": "AB7C2N5Q",
  "used": false,
  "role": "indemnitor",
  "expiresAt": "2026-02-06T14:00:00.000Z",
  "caseId": "case-2026-001234",
  "email": "jane.smith@example.com",
  "createdAt": "2026-01-07T14:00:00.000Z"
}
```

### Example 3: Used/Expired Magic Link
```json
{
  "_id": "ghi789",
  "token": "ZZ9X4M1P",
  "used": true,
  "role": "defendant",
  "expiresAt": "2026-01-15T10:00:00.000Z",
  "caseId": "case-2026-000999",
  "email": "old.user@example.com",
  "createdAt": "2025-12-15T10:00:00.000Z"
}
```

---

## Collection Permissions

### Read Permissions
- **Backend only** - Frontend code should not directly read this collection
- Use backend functions (`accessCodes.jsw`) to validate tokens

### Write Permissions
- **Backend only** - Only backend code can create/update magic links
- Staff portal can trigger generation through backend functions

---

## Backend Functions Available

### From `accessCodes.jsw`:

1. **`validateCode(accessCode)`**
   - Validates a magic link token
   - Checks if active (used=false) and not expired
   - Marks as used after successful validation
   - Returns portal type and redirect URL

2. **`generateAccessCode(caseId, role, email, expirationDays)`**
   - Creates a new magic link
   - Generates random 8-character token
   - Sets expiration date
   - Returns the token

3. **`deactivateAccessCode(accessCode)`**
   - Marks a magic link as used
   - Prevents further use

4. **`reactivateAccessCode(accessCode)`**
   - Marks a magic link as unused (for testing)
   - Only works if not expired

5. **`getCaseByAccessCode(accessCode)`**
   - Retrieves case information by token
   - Returns null if not found or used

6. **`getActiveMagicLinksByCase(caseId)`**
   - Gets all active magic links for a case
   - Filters out expired links

7. **`cleanupExpiredLinks()`**
   - Marks all expired links as used
   - Maintenance function

---

## Usage Flow

### 1. **Generation (from Dashboard.html or Staff Portal)**
```javascript
// Generate magic link when packet is created
const token = await generateAccessCode(
    "case-2026-001234",  // caseId
    "defendant",          // role
    "john.doe@email.com", // email
    30                    // expires in 30 days
);

// Send token to user via email/SMS
// Include link: https://shamrockbailbonds.biz/portal-landing?code=XY3K9P2M
```

### 2. **Validation (from Portal Landing Page)**
```javascript
// User enters code or clicks link with code parameter
const result = await validateCode("XY3K9P2M");

if (result.valid) {
    // Redirect to appropriate portal
    wixLocation.to(result.redirectUrl);
} else {
    // Show error message
    console.error(result.message);
}
```

### 3. **Automatic Expiration**
- Links automatically become invalid after `expiresAt` date
- Validation function checks expiration
- Expired links are marked as `used: true`

---

## Integration with Dashboard.html (Google Apps Script)

When the GAS Dashboard generates a bail packet:

1. **Create magic link** for defendant
2. **Create magic link** for indemnitor (if different person)
3. **Send links via email/SMS** with instructions
4. **Include in packet** as QR code or URL

Example email:
```
Subject: Your Shamrock Bail Bonds Portal Access

Hi John,

Your bail paperwork is ready. Access your portal here:
https://shamrockbailbonds.biz/portal-landing?code=XY3K9P2M

Or enter this code manually: XY3K9P2M

This link expires on February 6, 2026.

Questions? Call (239) 332-2245
```

---

## Testing Checklist

- [ ] Create test magic link in MagicLinks collection
- [ ] Set `used: false` and future `expiresAt` date
- [ ] Enter token on portal-landing page
- [ ] Verify validation works
- [ ] Check that token is marked `used: true` after validation
- [ ] Test expired link (past `expiresAt` date)
- [ ] Test already-used link (`used: true`)
- [ ] Test invalid token (doesn't exist)

---

## Maintenance

### Regular Cleanup
Run `cleanupExpiredLinks()` periodically (e.g., daily) to mark expired links as used:

```javascript
// In a scheduled job or maintenance function
const cleanedCount = await cleanupExpiredLinks();
console.log(`Cleaned up ${cleanedCount} expired magic links`);
```

### Monitoring
Track magic link usage through:
- `AccessCodeUsage` collection (if created)
- Wix Analytics events
- Console logs

---

## Security Considerations

1. **One-time use:** Links are marked as used after first validation
2. **Expiration:** All links have expiration dates
3. **Backend validation:** Frontend cannot bypass validation
4. **No sensitive data:** Tokens don't contain case information
5. **Random generation:** Tokens are cryptographically random

---

## Troubleshooting

### Magic link doesn't work
- Check `used` field - should be `false`
- Check `expiresAt` - should be in the future
- Verify `token` matches exactly (case-sensitive)
- Check browser console for errors

### Link works multiple times
- Ensure validation function marks as `used: true`
- Check database permissions
- Verify `wixData.update()` is executing

### Wrong portal redirect
- Check `role` field value
- Verify role mapping in `accessCodes.jsw`
- Ensure dashboard pages exist at redirect URLs

---

**Last Updated:** January 7, 2026  
**Version:** 2.0 (Updated for MagicLinks collection)
