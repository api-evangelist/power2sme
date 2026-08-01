---
name: power2sme-otp-onboarding
description: Register a new SME contact, verify the mobile number by OTP, and sign in against the Power2SME API.
api: POWER2SME API
base_url: https://api.power2sme.com/api/ws/v4
method: generated
generated: '2026-07-20'
source: openapi/power2sme-openapi.json
operations:
  - registercontactUsingPOST
  - generateOtpUsingPOST_3
  - validateOtpUsingPOST_3
  - customerloginUsingPOST
  - createPasswordUsingPOST
---

# Power2SME OTP Onboarding

Onboard a new SME contact and get them signed in. Every call goes to
`https://api.power2sme.com/api/ws/v4` and returns the shared Response Model
envelope (`Status`, `ErrorCode`, `Message`, `Data`, `TotalRecords`) — always
branch on `ErrorCode`/`Status`, not just the HTTP code. Operations return `401`
when the caller is unauthenticated and `403` when forbidden.

## Steps

1. **Check registration** — `POST /isregister` (`isRegisterUsingPOST`) with the
   contact number to see whether the SME already exists. If already registered,
   skip to sign-in.
2. **Register the contact** — `POST /registercontact` (`registercontactUsingPOST`)
   with `firstname`, `lastname`, `companyname`, `contact_number`, `email`,
   `password`, `confirm_password`, and `leadsource`.
3. **Generate an OTP** — `POST /generateotp` (`generateOtpUsingPOST_3`) with the
   `mobileNumber` to send a one-time passcode.
4. **Validate the OTP** — `POST /validateotp` (`validateOtpUsingPOST_3`) with the
   `mobileNumber` and the `otp` the user received.
5. **Sign in** — `POST /customerlogin` (`customerloginUsingPOST`) with `userId`
   and `password`; capture the returned token for authenticated calls.
6. **(Optional) Set a password** — `POST /createpassword`
   (`createPasswordUsingPOST`) with `userName`, `newPassword`, and `tokenId`
   when the flow issues a one-time token instead of a password.

## Notes

- The Swagger document declares no `securityDefinitions`; treat the sign-in
  response token as a bearer/session credential for subsequent requests.
- There is no documented idempotency key — do not blindly retry POSTs that may
  have partially succeeded (e.g. registration); re-check with `/isregister`.
