# Verification report — repaired package

Verification date: 2026-11-15

## Source-level repairs applied

- Firestore cabinet writes now use an explicit allowlist of public profile fields, validate status/name/role/photo URL/storage path, and reject unapproved extra fields.
- Admin-profile, content and activity-log fields have tighter type/length checks; activity-log writes require all audit fields.
- Admin and public Firebase content loaders now read in bounded cursor batches instead of silently stopping at the first 150/60/30 records. The admin dashboard still sorts loaded records by timestamp while retaining legacy records without `createdAt`.
- Recent activity uses a descending `createdAt` query.
- Editing optional fields now writes empty values so an old venue/body/photo URL does not remain unintentionally. Replacing or clearing uploaded media removes the stale storage reference and attempts to clean up the old object.
- Admin create/update/delete operations for admin accounts are included in activity logging.
- Uploaded images receive client-side MIME/signature checks and, when supported by the browser, dimension/pixel-count checks. Failed download-URL retrieval attempts clean up the just-uploaded object.
- Optional Firebase App Check integration is included for both the public loader and admin app. It remains inactive until a real reCAPTCHA v3 site key is configured in `admin/firebase-config.js` and App Check is registered in Firebase Console.
- Added Open Graph/Twitter metadata and lazy image loading/async decoding while preserving existing site layout and colors.
- Replaced the three literal `Alumni Name 1/2/3` demo cards with a neutral “profiles coming soon” state. No real alumni names were invented.
- Optimized the largest cabinet JPEGs while keeping original filenames and all 17 photo references. Total JPEG bytes reduced by approximately 977 KB (about 23%).
- Expanded `scripts/check-project.mjs` to check the new rules and batch-loading safeguards.

## Passed locally

- `npm run check` — all project integrity and added source checks passed.
- Node syntax checks — `script.js`, `public-content.js`, `admin/admin.js`, and `admin/firebase-config.js` passed.
- HTML checks — no duplicate IDs; all image elements have `alt` attributes; local references resolve.
- Configuration — `vercel.json`, `firebase.json`, `.firebaserc`, and `package.json` are valid JSON; Firebase project IDs match.
- All 17 JPEG cabinet photos decode successfully.
- Local HTTP smoke checks returned HTTP 200 for the homepage, admin page, CSS/JS, Firebase rules, one cabinet photo and one gallery placeholder.
- ZIP archive integrity checked after packaging.

## Not verified from this environment

- Firestore and Storage rules have not been compiled/deployed or tested in the Firebase Emulator Suite.
- Firebase Authentication, real Firestore reads/writes, Storage upload/deletion and App Check enforcement have not been tested against the live Firebase project.
- Vercel production routing and deployed browser behavior have not been tested.
- App Check requires a reCAPTCHA v3 site key and Firebase Console setup; no key was fabricated or enabled.
- Storage Rules can check the declared MIME type and file size but cannot inspect the uploaded file's byte signature. The signature check is client-side; strict server-side file inspection would require a trusted backend/Cloud Function.

These checks require the owner's Firebase/Vercel access and a configured Emulator/deployment environment. Passing the local checks is not proof that external services are live or production-ready.
