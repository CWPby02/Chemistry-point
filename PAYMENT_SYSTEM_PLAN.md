# Chemistry Point Payment System — Implementation Plan

Branch: `payment-system-work`

## Existing data contract confirmed

### Quiz
- Metadata: `quizzes/{quizId}`
- Relevant fields: `title`, `type`, `chapter`, `duration`, `questionCount`, `active`, `accessType` (`free|paid`), `price`
- Questions: `questions/{questionId}`
- Question matching: `questions/{questionId}.quizId === quizId`
- Current student quiz page reads quiz metadata and the complete `questions` node, then filters questions in browser.

### Objectives / PDFs
- Chapter metadata and public Drive URLs: `chapters/{chapterId}`
- Chapter fields: `name`, `slug`, `vviPdf`, `pyqPdf`, `allPdf`
- Access fields: `vviAccessType`, `vviPrice`, `pyqAccessType`, `pyqPrice`, `allAccessType`, `allPrice`
- Student PDF page reads `chapters` and opens the stored Google Drive URL directly.
- Chapter save currently does not persist a reliable `classLevel`; student PDF page treats missing class as Class 12.

## Target database layout

### Public catalogue (metadata only)
- Keep `quizzes/{quizId}` for catalogue metadata; paid question bodies must not remain publicly readable.
- Keep `chapters/{chapterId}` for chapter name, slug, class, access type, and price only.
- Do not store paid PDF download URLs in a publicly readable chapter record.
- `paymentPrices/{productKey}` remains the source for displayed purchase price, but price must be rechecked by trusted server logic at request creation.

### Payment records
`paymentRequests/{requestId}`
- `requestId`: random Firebase push key
- `studentName`, `phone`, `studentId`
- `productType`: `quiz|pdf`
- `productId`: quiz key or stable PDF product key
- `productName`, `amount`
- `utr` (submitted by student after paying)
- `status`: `pending|approved|rejected`
- `createdAt`, `submittedAt`, `reviewedAt`, `reviewedBy`, `rejectionReason`
- A student-created request must never be able to set `status=approved`, `reviewedBy`, or entitlement data.

### Private dashboard
`paymentDashboards/{tokenHash}`
- `studentName`, `studentId`, `createdAt`
- `payments/{requestId}`: minimal status/product summary, no public listing
- The browser URL contains a high-entropy random secret token; only its SHA-256 hash is used as the database key.
- The raw token must never be posted in a public WhatsApp group. Treat the URL as a password: anyone holding it can view that dashboard.

### Entitlements and protected material
- `entitlements/{tokenHash}/{productKey}`: admin/trusted-server controlled grant record.
- A browser-only approval flag is not a security boundary.
- Quiz question bodies and paid PDF files must be moved to protected storage or delivered through a trusted backend that checks the token's entitlement on every request.
- Free content may remain public if that is intended.

## Security blocker and required backend

The current site is a static client-side app. Realtime Database Rules cannot safely verify a manual payment screenshot/UTR, generate trusted approvals, or protect a public Google Drive URL once that URL is known. Rules also cannot hide paid question records that are currently under a publicly readable `questions` node.

For a genuinely protected no-login flow, implement Firebase Cloud Functions (or an equivalent trusted server):
1. Create payment request: validate product exists and is paid, read authoritative price, generate random dashboard secret, store only token hash, return the raw token once.
2. Submit UTR: allow only the request's private capability to update UTR while status is pending; validate UTR format and limit edits.
3. Admin approval/rejection: require Firebase Admin authentication; verify request is pending; atomically update request, dashboard status, and entitlement.
4. Serve paid quiz data / PDF download only after checking the dashboard token hash has the matching entitlement.
5. Keep private request and entitlement paths unreadable/writable to anonymous clients except through the trusted endpoints.

## Important migration work before enabling protection
- Split paid questions from the public `questions` node (or migrate them to protected storage). Until this is done, a determined visitor can read paid question data directly.
- Move paid PDFs away from public Google Drive sharing links. A public Drive link cannot be revoked by Firebase Rules.
- Preserve existing quiz IDs and PDF product identity during migration.
- Add and save `classLevel: 11|12` for chapter records; the current admin chapter form does not save it consistently.
- Add a stable PDF product identity: `{chapterId}/{type}`, where type is `vvi|pyq|all`.

## Implementation order
1. Finalize the product key and class-level data contract.
2. Add trusted backend endpoints and protected storage.
3. Publish final RTDB and Storage Rules matching those endpoints.
4. Update `payment.html`, `admin/payments.html`, `dashboard.html`, `quiz.html`, `student/quiz.html`, `student/objective.html`, and `student/pdf.html`.
5. Test pending, approved, rejected, invalid token, cross-student access, free products, and direct-URL access.
6. Do not deploy or merge to `main` until the owner explicitly approves.
