---
name: Cello
description: Use when building referral programs, integrating referral components into web or mobile apps, tracking referral conversions (signups and purchases), setting up webhooks for payment gateways, or implementing attribution tracking. Reach for this skill when agents need to embed referral functionality, configure API authentication, track user conversions back to referrers, or integrate with Stripe/Chargebee webhooks.
metadata:
    mintlify-proj: cello
    version: "1.0"
---

# Cello Skill Reference

## Product Summary

Cello is a referral platform for building user and partner referral programs. It enables you to embed referral components in web and mobile apps, track and attribute user signups and purchases back to referrers, and process payouts. Key components: **Cello JS** (web referral component SDK), **Attribution JS** (referral code capture library), **Cello API** (REST endpoints for events and referral codes), and **Cello MCP** (Model Context Protocol for AI integration). Access the Cello Portal at https://portal.cello.so. Primary docs: https://docs.cello.so.

---

## When to Use

Reach for this skill when:

- **Embedding referral UI**: Adding a referral component to web apps (React, Next.js, Vue, Angular) or mobile apps (iOS, Android, React Native, Flutter)
- **Capturing referral codes**: Setting up landing page attribution to detect and store `ucc` (unique campaign code) parameters from referral links
- **Tracking conversions**: Sending signup or purchase events to Cello via API, webhooks, or payment gateway metadata
- **Configuring webhooks**: Integrating Stripe or Chargebee webhooks to automatically track referral-driven purchases
- **Authenticating users**: Generating JWT tokens for secure Cello component initialization
- **Querying program data**: Using the Cello API to fetch referral code info or retrieve active links
- **AI-assisted development**: Connecting Cello MCP to Claude, Cursor, or other AI tools for live integration guidance

---

## Quick Reference

### Core Concepts

| Term | Definition |
|------|-----------|
| **UCC** | Unique Campaign Code — 11-character referral identifier (e.g., `pNRB1aYqArN`) |
| **Referrer** | Existing user who shares their referral link |
| **New User** | Person who receives and uses a referral link |
| **Attribution** | Process of capturing and storing referral codes on landing pages |
| **Referral Conversion** | Tracking when referred users complete target actions (signup, purchase) |

### Key Files & Credentials

| Item | Location |
|------|----------|
| **Product ID** | Cello Portal → Integrations → Access Keys |
| **Product Secret** | Cello Portal → Integrations → Access Keys |
| **Access Key ID / Secret** | Cello Portal → Integrations → Access Keys (for REST API) |
| **Stripe Webhook Secret** | Stripe Dashboard → Developers → Webhooks (starts with `whsec_`) |
| **Chargebee Webhook Credentials** | Chargebee Dashboard → Settings → Configure Chargebee → Webhooks |

### API Base URLs

| Environment | URL |
|-------------|-----|
| **Sandbox** | `https://api.sandbox.cello.so/` |
| **Production** | `https://api.cello.so/` |

### Essential SDK Scripts

**Cello JS (Referral Component):**
```javascript
// Sandbox
<script type="module" src="https://assets.sandbox.cello.so/app/latest/cello.js" async></script>

// Production
<script type="module" src="https://assets.cello.so/app/latest/cello.js" async></script>
```

**Attribution JS (Referral Code Capture):**
```javascript
// Sandbox
<script src="https://assets.sandbox.cello.so/attribution/latest/attribution.js" async></script>

// Production
<script src="https://assets.cello.so/attribution/latest/attribution.js" async></script>
```

### Common API Endpoints

| Endpoint | Purpose |
|----------|---------|
| `POST /token` | Authenticate and get access/refresh tokens |
| `POST /events` | Send signup, purchase, or custom events |
| `GET /referral-codes/{code}` | Fetch referral code info |
| `GET /referral-codes/active/{productUserId}` | Fetch active referral link for user |

### Attribution JS Methods

```javascript
// Get referral code from URL or cookie
const ucc = await window.CelloAttribution("getUcc");

// Get referrer's name for personalization
const referrerName = await window.CelloAttribution("getReferrerName");

// Get campaign config (discount %, duration)
const config = await window.CelloAttribution("getCampaignConfig");

// Manage cookie consent
await window.CelloAttribution("allowCookies");
await window.CelloAttribution("deleteCookies");
```

### Cello JS Methods

```javascript
// Boot the referral component
await cello.boot({
  productId: "YOUR_PRODUCT_ID",
  token: "JWT_TOKEN",
  productUserDetails: { firstName, lastName, email }
});

// Control visibility
cello.show();
cello.hide();
cello.open();

// Get referral data
const ucc = await cello.getUcc();
const campaignConfig = await cello.getCampaignConfig();
```

---

## Decision Guidance

### When to Use Cello JS vs Attribution JS

| Scenario | Use Cello JS | Use Attribution JS |
|----------|-------------|-------------------|
| Embed referral sharing UI in app | ✓ | |
| Capture referral codes on landing page | | ✓ |
| Display referrer name for personalization | | ✓ |
| Show referral rewards/discounts | ✓ | ✓ |
| Track referrer performance | ✓ | |
| Attach UCC to signup forms | | ✓ |

### When to Track Signups via Stripe Metadata vs API

| Condition | Use Stripe Metadata | Use API Endpoint |
|-----------|-------------------|------------------|
| Create Stripe customer at signup | ✓ | |
| Create Stripe customer at purchase | | ✓ |
| Use Chargebee | | ✓ |
| Use Paddle, Recurly, or custom gateway | | ✓ |
| Need custom event types (demo, trial) | | ✓ |

### When to Use Webhooks vs Manual API Calls

| Approach | Webhooks | Manual API |
|----------|----------|-----------|
| Automatic purchase tracking | ✓ | |
| Real-time event processing | ✓ | |
| Custom business logic | | ✓ |
| Batch event processing | | ✓ |
| Testing/debugging | | ✓ |

---

## Workflow

### 1. Embed Referral Component (Cello JS)

1. **Get credentials**: Retrieve `productId` and `PRODUCT_SECRET` from Cello Portal → Integrations → Access Keys
2. **Add script**: Include Cello JS script tag in your HTML (sandbox or production URL)
3. **Generate JWT token server-side**: Create token with `productId`, `productUserId`, `iat` (current Unix timestamp), signed with `PRODUCT_SECRET` using HS512
4. **Boot component**: Call `cello.boot()` with `productId`, JWT token, and user details (firstName, lastName, email)
5. **Verify**: Check that Cello button appears on page and opens referral component on click

### 2. Capture Referral Codes (Attribution JS)

1. **Add script**: Include Attribution JS script tag on landing pages
2. **Test capture**: Visit landing page with `?ucc=test` parameter and verify `cello-referral` cookie is set
3. **Retrieve code at signup**: Call `window.CelloAttribution("getUcc")` to get the code
4. **Store for later**: Save the UCC with user record if signup happens in separate session
5. **Verify**: Check Integration Status in Cello Portal shows "Connected" for landing page capture

### 3. Track Signups

**Option A: Stripe Metadata (if customer created at signup)**
1. Retrieve UCC using `window.CelloAttribution("getUcc")`
2. Pass to Stripe customer creation in `metadata`: `{ cello_ucc: ucc, new_user_id: userId }`
3. Set up Stripe webhook in Cello Portal with signing secret
4. Verify webhook events appear in Event Feed

**Option B: Chargebee Metadata (if customer created at signup)**
1. Retrieve UCC using `window.CelloAttribution("getUcc")`
2. Pass to Chargebee customer creation in `meta_data`: `{ cello_ucc: ucc, new_user_id: userId }`
3. Set up Chargebee webhook in Cello Portal with basic auth credentials
4. Verify webhook events appear in Event Feed

**Option C: Cello API (all other cases)**
1. Retrieve UCC using `window.CelloAttribution("getUcc")`
2. Get access token: `POST /token` with `accessKeyId` and `secretAccessKey`
3. Send event: `POST /events` with `eventName: "ReferralUpdated"`, `payload.ucc`, `payload.newUserId`, `context.newUser` details
4. Check Event Feed for validation errors

### 4. Track Purchases

1. **Capture purchase event** from payment gateway (Stripe `charge.succeeded`, Chargebee `payment_succeeded`, etc.)
2. **Extract referral data**: Get UCC and new user ID from customer metadata or stored record
3. **Send to Cello**: 
   - Via webhook (automatic if configured)
   - Via API: `POST /events` with `eventName: "ReferralUpdated"`, `payload.ucc`, `payload.newUserId`, `payload.price`, `payload.currency`
4. **Verify**: Check Event Feed for successful event processing and referrer reward calculation

### 5. Set Up Webhooks (Stripe Example)

1. **In Stripe Dashboard**: Developers → Webhooks → Add endpoint
2. **Enter endpoint URL**: Use Cello Portal → Integrations → Stripe Webhook (copy provided URL)
3. **Select events**: `customer.created`, `customer.updated`, `charge.succeeded`, `charge.refunded`, `invoice.paid`
4. **Get signing secret**: Reveal and copy the `whsec_` secret
5. **In Cello Portal**: Integrations → Stripe Webhook → paste signing secret
6. **Verify**: Check Integration Status shows "Connected"

---

## Common Gotchas

- **JWT token `iat` validation**: Token's `iat` (issued at) must be current (within 24 hours) and not in the future. Server clock skew causes boot failures. Use `Math.floor(Date.now() / 1000)` to generate dynamically, not hardcoded values.

- **Missing `productUserId` as string**: JWT token requires `productUserId` as a **string**, not a number. Numeric IDs cause "User is not authorized" errors.

- **UCC not persisting across sessions**: Attribution JS stores UCC in first-party cookies for 3 months. If cookies are blocked or cleared, UCC is lost. Always retrieve and store UCC server-side during signup.

- **Webhook signing secret mismatch**: Stripe and Chargebee webhook secrets must match exactly in Cello Portal. Mismatched secrets cause webhook validation failures and events are rejected.

- **Missing required fields in events**: `ucc` and `newUserId` are always required for signup/purchase events. Missing fields cause validation errors visible in Event Feed. Email is required if using auto-attribution.

- **Organization-level attribution**: If rewarding on organization level (not individual user), always pass organization ID in `payload.newUserId` and include `newUser.organizationId` in context.

- **Stripe customer created at purchase, not signup**: If you create Stripe customer at purchase (not signup), you cannot use Stripe metadata for signup tracking. Use Cello API `POST /events` instead.

- **CSP headers blocking Cello**: If site enforces Content Security Policy, Cello domains must be explicitly allowed in `script-src`, `connect-src`, `img-src`, `style-src`, `font-src` directives. Missing CSP rules cause widget load failures.

- **Sandbox vs Production mismatch**: Credentials, URLs, and webhooks are environment-specific. Using sandbox credentials with production URLs (or vice versa) causes authentication failures.

- **Refresh token expiry**: REST API refresh tokens expire after 5 days. After expiry, must re-authenticate with `accessKeyId` and `secretAccessKey` to get new tokens. No separate endpoint to refresh only the refresh token.

---

## Verification Checklist

Before submitting work:

- [ ] **Cello JS**: Verify referral component button appears on page and opens on click
- [ ] **JWT token**: Decode token at jwt.io and confirm `productUserId` is a string, `iat` is current, algorithm is HS512
- [ ] **Attribution JS**: Test landing page with `?ucc=test` parameter and confirm `cello-referral` cookie is set
- [ ] **UCC retrieval**: Call `window.CelloAttribution("getUcc")` in browser console and confirm it returns the code
- [ ] **Signup tracking**: Send test signup event and verify it appears in Cello Portal Event Feed with no validation errors
- [ ] **Purchase tracking**: Send test purchase event and verify referrer reward is calculated correctly
- [ ] **Webhook setup**: Confirm webhook endpoint URL is correct and signing secret matches in Cello Portal
- [ ] **Integration Status**: Check Cello Portal → Integrations → Integration Status shows all components "Connected"
- [ ] **Environments**: Confirm all credentials, URLs, and webhooks match the target environment (sandbox or production)
- [ ] **Error handling**: Test error scenarios (missing UCC, invalid token, network failures) and confirm graceful fallbacks

---

## Resources

**Comprehensive page listing**: https://docs.cello.so/llms.txt

**Critical documentation**:
- [Integration Overview](https://docs.cello.so/integration-overview) — 4-step setup guide
- [Referral Component Quickstart](https://docs.cello.so/referral-component/quickstart) — Cello JS installation and JWT authentication
- [Attribution Introduction](https://docs.cello.so/attribution/introduction) — Referral code capture and conversion tracking
- [API Reference](https://docs.cello.so/api-reference/introduction) — REST endpoints and authentication

---

> For additional documentation and navigation, see: https://docs.cello.so/llms.txt