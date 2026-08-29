# Lumina

Lumina is a static install page for iPad. People open it in Safari, install a small configuration profile, then tap **Install** on an app card.

The website is the installer. GitHub Actions only publishes the site. It does not sign binaries and it does not push apps onto the device.

## What you get

| Path | Role |
| --- | --- |
| `index.html` | Safari install UI |
| `profile.mobileconfig` | Home-screen web clip for the page |
| `apps/catalog.json` | App list the page renders |
| `apps/*.ipa` | Signed IPA files you add |
| `manifests/*.plist` | OTA manifests iPadOS reads |
| `.github/workflows/pages.yml` | Deploys the folder to GitHub Pages |

An older `build.yml` may still be in the tree. You do not need it to install anything.

## How install works

1. Safari loads the Pages URL.
2. The user installs `profile.mobileconfig` under **Settings → General → VPN & Device Management**. That profile only pins Lumina to the home screen.
3. The user taps **Install** on an app. The page opens an `itms-services://` link that points at that app’s manifest.
4. iPadOS downloads the IPA listed in the manifest and offers the system install sheet.

The IPA must already be signed with an identity the device will accept. Lumina does not sign on tap.

Use Safari. Other browsers drop the OTA prompt.

## Add an app

1. Sign the IPA however you already sign.
2. Upload the signed file to `apps/YourApp.ipa`.
3. Copy `manifests/example.plist` to `manifests/yourapp.plist` and set `url`, `bundle-identifier`, `bundle-version`, and `title`.
4. Append a record to `apps/catalog.json`.
5. Push to `main`. Open the site on the iPad and tap Install.

`__PAGES_URL__` is rewritten to your real Pages origin during deploy.

## GitHub Pages setup

1. Create a repository and upload this project.
2. Settings → Pages → Source: GitHub Actions.
3. Settings → Actions → workflow permissions → Read and write.
4. Run **Deploy Lumina Site** or push to `main`.
5. Public URL: `https://<user>.github.io/<repo>/`.

Large IPAs can live on GitHub Releases. Put that asset URL in the manifest if the file is too big for Pages.

## Limits

- HTTPS is required for OTA.
- The signing identity must be trusted on the iPad.
- If Apple revokes that identity, installed apps stop launching.
- A configuration profile cannot inject a signing certificate or silently install software.
- This project does not include a certificate.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| Install tap does nothing | Not Safari, or the manifest URL 404s |
| Profile downloads but nothing appears | Settings → General → VPN & Device Management |
| Untrusted Enterprise Developer | Trust the signing team in Settings |
| App installs then immediately dies | Signing identity revoked |
| Catalog empty | `catalog.json` missing or invalid |

## License

Use it for apps you are allowed to distribute.
