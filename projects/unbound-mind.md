# Unbound Mind — offline-first OCD self-help app

> **Status:** Private, pre-release · **Code:** private, available on request · **Role:** solo design, development & security engineering
>
> *Not a medical device and not a substitute for professional care. No clinical validation is claimed.*

An offline-first Flutter app for people managing OCD through CBT and ERP techniques. The core design constraint: **nothing ever leaves the device** — the app ships with no backend, no analytics, and no `INTERNET` permission in the Android manifest at all.

## What it does

- **6-step thought journal** — trigger → intrusion → emotion → interpretation → strategy → compulsion
- **Exposure ladder** with SUDS tracking and habituation curves
- **"Courtroom"** cognitive restructuring — put a thought on trial, weigh evidence for and against, deliver a verdict
- Quick thought capture, progress analytics, grounding & breathing exercises
- Bilingual (Polish + English), ~500 localization keys per language
- Anti-compulsion guardrails enforced *in code*, not just copy — e.g. "letting go" of a thought persists nothing, and the released-today counter stores only a date and a count, never content

## Tech stack

Flutter/Dart · Provider · GoRouter · Hive · PointyCastle (AES-GCM) · native Kotlin & Swift for key management · fl_chart · offline PDF export · local notifications

## Engineering highlights

**Hand-rolled auth-bound encryption keys — because the standard plugin was silently broken.** During security review I verified on-device that a popular Flutter secure-storage plugin's "biometric" mode actually stored keys in a shared, non-auth-protected store and dropped the authentication requirement entirely. I replaced it with a native AndroidKeyStore implementation: the data-encryption key is sealed by an AES/GCM key created with `setUserAuthenticationRequired(true)` and unwrapped only inside a `BiometricPrompt.CryptoObject` — every unlock requires a live biometric or device-credential check, not merely an unlocked device.

**App-layer authenticated encryption over local storage.** Every stored value is individually AES-256-GCM encrypted (nonce + ciphertext + auth tag) behind a wrapper that keeps call sites on a normal get/put API. A tampered or corrupted value degrades to that key's default instead of crashing the store. A two-tier key model lets the pre-auth entry funnel work while all clinical data stays sealed until unlock.

**Fail-safe destructive paths.** Platform key errors are classified by a pure, separately-tested function whose contract is: *never wipe on an outcome we couldn't positively identify.* Only a positively-identified permanent key invalidation reaches the wipe path; everything ambiguous resolves to "cancelled".

**Threat-model-driven lifecycle handling.** The app relocks and evicts keys from memory on every resume from background — but deliberately not on transient `inactive` states, because biometric prompts and permission dialogs themselves trigger those, and relocking there would re-prompt on top of the prompt.

**A self-audit that found (and fixed) real bugs.** A documented internal security audit caught two high-severity issues: the PDF export was fetching a font from Google's CDN — meaning a "nothing leaves the device" app made a network call at the exact moment a user exported a therapy report — and a shared error path could make panic-wipe silently abort offline. Both fixed and verified: the codebase now contains zero remote URLs and the manifest requests no network permission.

## Scale & quality

~16,000 lines of Dart application code (excluding generated localization) across 67 source files, with **77 test files (~6,800 LOC)** — better than 1:1 test-file-to-source-file ratio. Security has its own test cluster: encryption round-trips, key migration, unlock/relock cycles, hardware attestation probing, panic wipe. Development followed a written plan-and-spec process — 71 planning/spec documents, one pair per feature slice.

## Screenshots

*Coming soon.*
