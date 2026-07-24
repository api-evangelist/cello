---
name: Fetch a share link and check referee reward eligibility
description: Retrieve a user's active invite link for contextual sharing, and check whether a referred new user is eligible for a signup reward/discount.
api: openapi/cello-openapi-original.json
operations:
  - "POST /token"
  - "GET /referral-codes/active-link/{productUserId}"
  - "GET /new-users/{productUserId}/reward"
---

# Fetch a share link and check referee reward eligibility

Use this skill to power in-product sharing and new-user discount flows.

## Prerequisites
- A Cello `accessKeyId` / `secretAccessKey`.
- The `productUserId` of the user in your system.
- Base URL: `https://api.cello.so` or `https://api.sandbox.cello.so`.

## Steps
1. **Get a token** — `POST /token` with `{ accessKeyId, secretAccessKey }`; use the returned `accessToken` as `Authorization: Bearer <accessToken>`.
2. **Fetch the active share link** — `GET /referral-codes/active-link/{productUserId}` to get the user's active UCC and invite `link` for contextual sharing at a key moment.
3. **Check referee reward** — `GET /new-users/{productUserId}/reward` to read `eligible`, the `reward` (type/interval/percentage), and the originating `referralUcc` / `campaignId`, then apply the discount at checkout.

## Rules
- All three endpoints require the bearer `accessToken`; `401` means the token is missing or expired.
- The reward object describes the discount (`type`, `interval`, `intervalCount`, `percentage`) — apply it per your billing integration (see the Apply Discounts guide).
- Use sandbox keys and `api.sandbox.cello.so` while testing.
