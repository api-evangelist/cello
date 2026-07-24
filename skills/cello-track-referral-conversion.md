---
name: Track a referral conversion with Cello
description: Authenticate, then report a signup or purchase conversion back to Cello so a referral is attributed to its referrer.
api: openapi/cello-openapi-original.json
operations:
  - "POST /token"
  - "POST /events"
  - "GET /referral-codes/{code}"
---

# Track a referral conversion with Cello

Use this skill to attribute a signup or purchase to the referrer who drove it.

## Prerequisites
- A Cello `accessKeyId` and `secretAccessKey` from the Access Keys section of the dashboard.
- The referral code (UCC) captured on the client by the Attribution JS SDK.
- Base URL: `https://api.cello.so` (production) or `https://api.sandbox.cello.so` (sandbox).

## Steps
1. **Get a token** — `POST /token` with `{ accessKeyId, secretAccessKey }`. Read `accessToken` (and `refreshToken`) from the response; note `expiresIn`. Send `Authorization: Bearer <accessToken>` on every subsequent call.
2. **(Optional) Validate the referral code** — `GET /referral-codes/{code}` to confirm the UCC is `valid` and to discover its `productUserId` / `campaignId` before reporting.
3. **Report the conversion** — `POST /events` with an event envelope: `eventName` (e.g. `ReferralUpdated`), a `payload` (`ucc`, `newUserId`, `price`, `currency`), and `context`.

## Rules
- Auth is bearer only; there are no OAuth scopes on the REST API. Refresh with `POST /token` using the `refreshToken` when `accessToken` expires.
- No idempotency key is supported — do not blindly retry `POST /events`; on a network failure, verify via the portal Event Feed before re-sending.
- Errors return a simple `{ message }` envelope. `400` = malformed payload, `401` = bad/expired token.
- Prefer sandbox (`api.sandbox.cello.so`) with sandbox keys while integrating.
