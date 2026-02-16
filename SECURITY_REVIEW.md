# Security Evaluation Report: `tqheel/noTunes`

## Scope and Method
- Reviewed all source files in this repository (`AppDelegate.swift`, `Info.plist`, Xcode project settings, and README instructions).
- Checked for unsafe process execution, untrusted input handling, persistence behavior, and suspicious network or data exfiltration patterns.
- Reviewed build/dependency footprint and installation guidance for untrusted operations.

## Findings

### 1. Malicious behavior indicators
- **No malicious behavior found.**
- The app does not include code that harvests credentials, exfiltrates local files, installs persistence outside normal macOS login item behavior, or contacts remote command-and-control services.

### 2. Process execution and input handling
- `AppDelegate.swift` listens for `NSWorkspace.willLaunchApplicationNotification` and force-terminates only:
  - `com.apple.Music`
  - `com.apple.iTunes`
- Replacement launch behavior uses `/usr/bin/open` with arguments passed via `Process` (no shell invocation), which avoids shell-injection patterns.
- Replacement target comes from local `UserDefaults` and is only changed by local user actions/commands.

### 3. Permissions and least privilege
- App is a standard menu bar utility (`LSUIElement`), with no elevated privilege requests.
- No privileged helper tools or installer scripts are present in this repository.
- No custom entitlements were found that indicate broadened system access.

### 4. Dependencies and supply-chain exposure
- No third-party package manager manifests (e.g., CocoaPods, SwiftPM, npm, pip) are present.
- The app relies on Apple system frameworks (`Cocoa`, `ServiceManagement`) and bundled assets only.
- As a result, dependency attack surface in this fork is minimal.

### 5. Installation/script safety
- README installation options are:
  - Direct download from GitHub releases
  - Homebrew cask install
- No install-time scripts in this repository execute untrusted code.
- User-facing `defaults` and `osascript` commands in README are standard local macOS commands.

## Risk Notes and Recommendations
- **Low residual risk:** if a local attacker already has ability to modify this app’s `UserDefaults`, they could change the replacement target to an unexpected app/URL. This does not create privilege escalation by itself, but could alter user behavior.
- Recommended hardening (optional):
  - Validate replacement target before launch (e.g., allow expected URL schemes/paths only).
  - Consider checking that replacement app paths exist before calling `/usr/bin/open`.

## Conclusion
- Based on this review, **this fork appears safe for local installation on a MacBook** for its stated purpose.
- No critical or high-severity security issues were identified in the repository contents reviewed.
