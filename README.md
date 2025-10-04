# Umbral Store – iOS WebClip (.mobileconfig)

This folder includes a ready profile that installs a home screen WebClip (iPhone/iPad) which opens your site in full screen without Safari UI.

- **Files**
  - `install.mobileconfig` – the WebClip profile (already branded as Umbral Store, with an embedded placeholder icon).
  - `index.html` – a tiny test page; optional.

## What to change (2-minute setup)

- **Target URL**: edit `install.mobileconfig`, find the `URL` key and set your website (e.g., `https://your-site.com/`). Optionally change `TARGET_URL` in `index.html` too.
- **Icon (optional but recommended)**: replace the embedded placeholder with a base64 of your 180x180 PNG.
  - PowerShell to generate base64:
    ```powershell
    $b64 = [Convert]::ToBase64String([IO.File]::ReadAllBytes("icon.png"))
    (Get-Content -Raw install.mobileconfig) -replace '(?s)(<key>Icon</key>\s*<data>)[\s\S]*?(</data>)', "$1`n$b64`n$2" | Set-Content install.mobileconfig
    ```
- **Identifiers (optional)**: keep `com.umbral.store.webclip` and `com.umbral.store.profile`, or change both to your reverse-DNS IDs.
- **UUIDs (optional)**: generate new ones with PowerShell `[guid]::NewGuid()` and replace `PayloadUUID` values.

## Install on iPhone/iPad

1. Send `install.mobileconfig` to the device (AirDrop, email, Files, or host it and open via Safari).
2. Settings → Profile Downloaded (or General → VPN & Device Management) → select the profile.
3. Tap **Install** → enter passcode → **Install** again.
4. The "Umbral Store" icon appears on the home screen. Tap to launch in full screen.

## Notes

- Works on iPhone and iPad.
- The WebClip launches the `URL` in `install.mobileconfig` directly; `index.html` is only a helper.
- To update in place, keep the same `PayloadIdentifier` values when re-issuing the profile.

## Auto prompt to download profile

- `index.html` now attempts a one-time auto-redirect on iOS to download `install.mobileconfig` and also shows a visible "Install Profile (iOS)" button as fallback.
- iOS may block automatic downloads in some cases; the explicit button remains for user-initiated download.
- If you deploy to a domain, consider setting an absolute URL for the profile in `index.html`:
  - Change `const PROFILE_URL = './install.mobileconfig';` to `const PROFILE_URL = 'https://your-domain.com/install.mobileconfig';`

### Hosting notes

- Serve the profile with content type `application/x-apple-aspen-config` if possible (recommended).
- Ensure the profile file is reachable at the path you use (relative or absolute) from the deployed site.
