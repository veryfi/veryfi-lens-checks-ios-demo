# Install

Checks UI binary. The pod spelling is Cheques: `VeryfiLens-Cheques`. Client settings type: `VeryfiLensSettings()`. Do not use `VeryfiLensChecksSettings`. Bank-check TIFF is not in this specific flavor. The TIFF model is large. TIFF needs Remote Models and `retrieveCheckTiffIsOn`. This recipe does not set that flag.

## Artifact and import

| | |
|--|--|
| Pod | `VeryfiLens-Cheques` |
| CocoaPods repo | `veryfi-lens-cocoapods-cheques` |
| Dedicated SPM | Yes. Repo `veryfi-lens-spm-checks`. Public host URL **Unconfirmed** |
| XCFramework file name | `VeryfiLens.xcframework` |
| Swift import | `VeryfiLens` |

```swift
import VeryfiLens
```



## Swift Package Manager

Xcode → File → Add Package Dependencies. The package credential is the Lens CocoaPods/SPM key from the Veryfi hub.

Dedicated repo name: `veryfi-lens-spm-checks`.

The release pipeline publishes that repo under `shared/lens/veryfi-lens-spm-checks.git` (GitLab remote `git@gitlab.services.veryfi.com:shared/lens/veryfi-lens-spm-checks.git`). The customer host `https://repo.veryfi.com/shared/lens/veryfi-lens-spm-checks.git` is **Unconfirmed**, because public installation pages still show `https://repo.veryfi.com/shared/lens/veryfi-lens-spm.git`.

That Full URL is the Full UI binary. Do not install this flavor from it. If Xcode cannot resolve the dedicated host, ask Veryfi for the package URL rather than substituting the Full package.


## CocoaPods

Public docs call CocoaPods deprecated and prefer SPM where a package exists. CocoaPods remains the pipeline path for this flavor.

```ruby
source 'https://repo.veryfi.com/shared/lens/veryfi-lens-podspec.git'
source 'https://github.com/CocoaPods/Specs.git'

target 'YourApp' do
  use_frameworks!
  pod 'VeryfiLens-Cheques'
end
```

Public pages pin a version with `pod 'VeryfiLens-Cheques', 'x.x.x.x', :linkage => :dynamic`. The current version number is **Unconfirmed** here. Use the version Veryfi gave the customer.


## Manual XCFramework

Embed `VeryfiLens.xcframework`. The pod name selects the flavor. Embedding a Full `VeryfiLens.xcframework` does not turn it into this product.

## Permissions

| Key | When |
|-----|------|
| `NSCameraUsageDescription` | Camera |
| `NSPhotoLibraryUsageDescription` | Gallery is on in this recipe |
| `NSPhotoLibraryAddUsageDescription` | Only if gallery backup is on. This recipe sets it false |
| `NSLocationWhenInUseUsageDescription` | Only if location is on. This recipe sets it false |

Do not add Contacts or Calendar usage strings unless product confirms those features ship in this flavor. The legacy framework README lists them. That list is **Unconfirmed** and likely stale.

iOS 13+ for UIKit Lens. SwiftUI helpers are documented as iOS 15+.

## App size

When installed size matters, recommend the **Remote Models** flavor. A specific flavor embeds its models in the binary. Remote Models keeps that binary smaller and downloads most of the heavy model resources after install, only for the features the host turned on.

Bank-check TIFF follows that split. `retrieveCheckTiffIsOn` is not supported on the Checks-specific flavor yet, because the TIFF model is large. Remote Models downloads it after install.

Measure the final build product, once with this Veryfi Lens SDK linked and once without it. The distributed XCFramework is a fat artifact that includes every architecture and platform slice. That file size is not the size added to the installed app. Store delivery can shrink the download further.

Flavor tables and the artifact-versus-app explanation: [SDK binary sizes](https://docs.veryfi.com/lens/mobile/settings/sdk_binary_sizes/).


## Public installation page

The public [checks installation](https://docs.veryfi.com/lens/mobile/checks/installation/) page shows pod `VeryfiLens-Cheques` and the Full SPM URL. The pod matches, including the Cheques spelling. Use SPM repo `veryfi-lens-spm-checks`.

## Optional local evidence

These paths are evidence from the Veryfi Lens iOS source tree. They are not part of this skill pack. Ignore them when this folder is the only checkout. Nothing in the integration depends on opening them.

- `fastlane/swift/FastlaneSwiftRunner/SPM.swift — repo names and XCFramework file names`
- `fastlane/swift/FastlaneSwiftRunner/Pipeline.swift — supportsSPM and supportsCocoaPods`
- `fastlane/swift/FastlaneSwiftRunner/Cocoapods.swift — pod repos and XCFramework file names`
- `fastlane/swift/FastlaneSwiftRunner/Builder.swift — FrameworkType raw values (pod names)`
- `Veryfi-Lens/Public/VeryfiLens.swift — UI entry point compiled into this module`
- `Veryfi-Lens/Public/Model/VeryfiLensSettings.swift — public UI settings`
- `Veryfi-Lens/Public/Model/VeryfiLensSettings.swift — check flags`
