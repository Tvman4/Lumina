# Lumina

iPad-first IPA install page + GitHub Actions signer.

No third-party sideload certificate is bundled. Those certs are not yours, they get revoked constantly, and putting one in a public repo is how people burn a whole signing identity. Lumina signs with material you put in GitHub Secrets.

## What you get

- Fancy Safari page (`index.html`) with profile download + OTA install button
- `profile.mobileconfig` — home-screen web clip only
- `.github/workflows/build.yml` — pulls an unsigned IPA, signs it with zsign, publishes a Release, deploys Pages

## Repo secrets

Settings → Secrets and variables → Actions:

| Secret | Value |
|---|---|
| `P12_BASE64` | `base64 -i certificate.p12` |
| `P12_PASSWORD` | p12 password |
| `MOBILEPROVISION_BASE64` | `base64 -i embed.mobileprovision` |

Optional local drop (gitignored): `certs/certificate.p12`, `certs/password.txt`, `certs/embed.mobileprovision`

## Run it

1. Push this folder to a GitHub repo
2. Enable Actions + Pages (source: GitHub Actions)
3. Put an unsigned IPA at `apps/input.ipa` **or** run the workflow with `ipa_url`
4. Actions → **Lumina Sign & Release** → Run workflow
5. On the iPad, open the Pages URL in **Safari**
6. Download Profile → Settings → General → VPN & Device Management
7. Tap Install IPA

OTA (`itms-services`) only works over https with a reachable `manifest.plist` and an IPA signed by an identity the device accepts.

## Encode secrets on a Mac or Linux box

```bash
base64 -i certificate.p12 | pbcopy
base64 -i embed.mobileprovision | pbcopy
```
