---
name: veryfi-lens-ios-checks-integrate
description: >-
  Integrates the Veryfi Lens iOS Checks UI SDK. Covers pod VeryfiLens-Cheques, SPM repo veryfi-lens-spm-checks, front-and-back check capture, VeryfiLensSettings, cloud extraction, gallery and Files entry points, and callbacks. Use when adding bank-check capture on iOS. Bank-check TIFF requires Remote Models, not this Checks flavor.
---

# Veryfi Lens iOS Checks

Walk a host app from install to a working first scan. Change the fewest flags. Do not invent SDK APIs.

**Public settings type:** `VeryfiLensSettings()`. Never recommend `VeryfiLensReceiptsSettings`, `VeryfiLensChecksSettings`, or other Demo `VeryfiLens*Settings` subclasses.

**No SDK schema changes.** The recipe is property assignments only.

## Quick flow

Use this when the host already asked for this flavor and did not leave a choice open.

Assumptions:

- UIKit or SwiftUI camera
- Document type `check`
- Cloud extraction, front and optional back, no TIFF
- Gallery on, More menu off, backups off


1. Install the artifact and plist keys in [install.md](install.md).
2. Apply the recipe in [settings.md](settings.md).
3. Run the conflict table in [troubleshooting.md](troubleshooting.md#conflicts).
4. `configure`, then present, as in [implementation.md](implementation.md).
5. Implement [callbacks.md](callbacks.md).
6. Track [checklist.md](checklist.md).

State those assumptions in the sample. Do not ask extra questions on this path.

## Guided flow

Ask only what is still missing:

| Topic | Options |
|-------|---------|
| UI | UIKit or SwiftUI |
| Sides | Front and optional back (recipe), or back required |
| Corners | Leave `validateCheckCorners` false unless the customer wants the algorithmic check |
| Extract | Cloud API, or decoupled `.none` |
| TIFF | Not this binary. Remote Models when TIFF is required |
| Sources | Camera, gallery, or standalone Files |
| Privacy | Photos backup, iCloud backup, location, keep local files after process |

If the host needs a document type this binary does not ship, stop. Say which types this flavor supports. Do not retarget `documentTypes` onto models this XCFramework does not contain. Any-Documents templates, including nutrition facts, US passport, driver license, and prescription labels, are a different solution. Contact support for a template that is not already confirmed. Credit-card capture stays on the credit-card flavor and on device. Bank-check TIFF needs Remote Models.

## Workflow

```
Lens integrate:
- [ ] Install artifact and plist
- [ ] Recipe on VeryfiLensSettings
- [ ] Conflict check
- [ ] configure and delegate
- [ ] One presentation, or uploadImages when the host already has files
- [ ] close / update / success / error
```

Read one level deep, and only inside this folder:

- [install.md](install.md) — artifact, SPM or CocoaPods, plist, app size
- [implementation.md](implementation.md) — credentials, configure, present, Files and gallery, uploadImages, utils
- [settings.md](settings.md) — recipe and the public settings catalog
- [callbacks.md](callbacks.md) — delegate payloads
- [troubleshooting.md](troubleshooting.md) — symptoms and conflicts
- [clarifications.md](clarifications.md) — Unconfirmed items
- [checklist.md](checklist.md) — done when

## Warnings

- Leave `backupDocsToGallery` and `backupDocsToiCloud` false unless the host wants every scan copied into Photos or iCloud. Gallery backup still defaults to true. Hide the More menu, or the iCloud row, so a user cannot turn the copies back on.
- Any-Documents is marketed as a broad solution: `any_document` plus `anyDocumentTemplate`. The template list is not closed. Nutrition facts, US passport, driver license, and prescription labels are shortcut flavors of Any-Documents, not separate products. Contact support for another template, including one that is not supported yet. Do not set `anyDocumentType` (deprecated local regex OCR). This binary is not that solution.
- Credit card data is local only and is never sent to the backend. `.inApp` (`"mobile"`) is only for `credit_card`, `code`, and `barcode`.
- Local fraud detection (`fraudDetectionIsOn`) and local field detection (`documentFieldsToDetect`) are paid features gated by the validated account and backend entitlement. Settings cannot unlock them. Field detection also needs the Field Detection or Remote Models flavor.
- Bank-check TIFF (`retrieveCheckTiffIsOn`) requires Remote Models. It is not in the Checks-specific flavor yet, because the TIFF model is large.
- When size matters, recommend Remote Models. Measure the final app with the SDK linked and with it removed. Do not quote the XCFramework file size. [SDK binary sizes](https://docs.veryfi.com/lens/mobile/settings/sdk_binary_sizes/).
- `uploadImages(paths:)` and `headlessUpload` on `VeryfiLens` are UI SDK helpers. They are not the Headless product (`VeryfiLensHeadless`).
- Public UI settings are `VeryfiLensSettings()`. Do not use Demo subclasses in client code.
- Headless products use `VeryfiLensCreditCardsSettings` or `VeryfiLensReceiptSettings` (singular), `VeryfiLensHeadlessCredentials`, and `VeryfiLensHeadless.shared()`.
- Leave `dictateIsOn` false. It is obsolete and not partner available. The SDK default is still true.
- Leave `ignoreRemoteSettings` true unless the host wants dashboard `lens_settings` to override the recipe.
- Leave `glareDetectionIsOn` false unless the host explicitly wants the current glare gate. It is a suboptimal algorithmic detector.


## Suggestions

- Turn `checksBackIsRequired` on only when both sides are mandatory.
- Leave corner validation off unless the customer accepts extra retakes.
- If TIFF or a smaller install size matters, recommend Remote Models and measure the final app with the SDK linked and without it. [SDK binary sizes](https://docs.veryfi.com/lens/mobile/settings/sdk_binary_sizes/).
- Any-Documents is a different solution. Do not set `anyDocumentTemplate` on this check recipe.


## Do not

- Do not promise TIFF on `VeryfiLens-Cheques`.
- Do not use the Demo checks settings subclass.
- Do not leave `backupDocsToGallery` true.
- Do not quote the XCFramework file size as the installed app size.


## Output

When generating host code, produce:

1. Info.plist keys actually needed
2. `configure` plus a retained delegate
3. One presentation path
4. The recipe’s `VeryfiLensSettings` mutations, with a one-line comment on each non-obvious flag
5. A short conflict note if any residual risk remains
