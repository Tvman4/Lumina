# Lumina

iPad installer page + GitHub Actions signer.

You do not need a PC. GitHub’s runners are the PC. You start the build from the GitHub website or the GitHub iOS app.

Lumina does **not** ship a signing certificate. KSign / ESign look “public” because those apps or their servers load an identity at runtime. A `.p12` is still a private key. This repo will **pull** files from URLs **you** set when you run the workflow. It will not hardcode someone else’s key.

---

## What’s in the repo

| File | What it does |
|---|---|
| `index.html` | Safari page on iPad. Profile button + install button. |
| `profile.mobileconfig` | Home-screen shortcut. Not a signing cert. |
| `.github/workflows/build.yml` | Downloads an unsigned IPA, signs it with zsign, publishes a Release, deploys Pages. |
| `apps/input.ipa` | Optional. Drop an unsigned IPA here instead of a URL. |
| `certs/` | Optional local files. Gitignored. |

---

## One-time GitHub setup (phone is fine)

1. Create a new GitHub repo.
2. Upload this folder (GitHub website → Add file → Upload, or the iOS app).
3. Repo **Settings → Actions → General**
   - Allow Actions
   - Allow GitHub Actions to create and approve pull requests is irrelevant
   - Workflow permissions: **Read and write**
4. Repo **Settings → Pages**
   - Source: **GitHub Actions**
5. Optional, so you don’t type URLs every run — **Settings → Secrets and variables → Actions → Variables**:

| Variable | What to put |
|---|---|
| `IPA_URL` | Direct link to an unsigned `.ipa` |
| `CERT_URL` | Direct link to **your** `.p12` |
| `PROVISION_URL` | Direct link to **your** `.mobileprovision` |
| `CERT_PASSWORD` | Password for that p12 |

If you would rather not leave the password in Variables, put `CERT_PASSWORD` / `P12_PASSWORD` under **Secrets** instead.

Base64 secrets also work if you already have them:

- `P12_BASE64`
- `P12_PASSWORD`
- `MOBILEPROVISION_BASE64`

---

## Run a build with no PC

1. Open the repo on your phone.
2. **Actions → Lumina Sign & Release → Run workflow**.
3. Fill what you didn’t store as variables:

   - `ipa_url` — unsigned IPA
   - `cert_url` — your p12
   - `provision_url` — your mobileprovision
   - `cert_password` — p12 password
   - `app_name` / `bundle_id` if you want overrides

4. Wait for the green check.
5. The signed file lands in **Releases** as `Lumina-signed.ipa`.
6. Pages URL looks like `https://YOURUSER.github.io/YOURREPO/`.

---

## Install on iPad

Safari only.

1. Open the Pages URL.
2. Tap **Download Profile**.
3. Settings → General → VPN & Device Management → Lumina → Install.
4. Back in Safari, tap **Install IPA**.
5. If OTA (`itms-services`) fights you, tap **Get IPA** and share the file into whatever installer you already use.

The profile is a web clip. Trust for the *app* still comes from whatever identity signed the IPA. If that identity is revoked, the app dies. That’s Apple, not Lumina.

---

## How signing resolution works

First match wins:

1. Workflow inputs (`cert_url`, `provision_url`, `cert_password`)
2. Repo variables (`CERT_URL`, `PROVISION_URL`, `CERT_PASSWORD`)
3. Repo secrets (`CERT_URL`, `PROVISION_URL`, or the `P12_BASE64` set)
4. Files in `certs/` if you committed them (not recommended)

There is no built-in fallback cert.

---

## Common failures

| What you see | Likely cause |
|---|---|
| `No IPA` | You didn’t pass `ipa_url` and `apps/input.ipa` isn’t in the repo |
| `No signing files` | No URLs, no secrets, no `certs/` files |
| zsign password error | Wrong `CERT_PASSWORD` |
| zsign provision error | Provision doesn’t match the p12 |
| iPad “Untrusted Developer” | You still have to trust the signing team in Settings |
| App installs then won’t open | That identity got revoked |
| OTA does nothing | Not Safari, or Pages / Release URL isn’t public |

---

## What this is not

- Not KSign, ESign, Feather, or GBox.
- Not a store of leaked enterprise certs.
- Not a way to hide a private key by calling it “public.”

If you already have a p12 + provision you control, Lumina will pull them and sign on GitHub. That’s the whole product.
