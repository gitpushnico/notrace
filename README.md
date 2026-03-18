# NoTrace — Private Metadata Remover

Strip GPS coordinates, device info, timestamps, and all hidden EXIF/XMP/IPTC metadata from photos.  
**100% client-side. Your images never leave your device.**

---

## Privacy Architecture

NoTrace is built around a single guarantee: **your photos are never transmitted anywhere**.

This is enforced at multiple levels:

1. **Canvas API stripping** — Images are re-drawn through the browser's Canvas API, which physically only copies pixel data. All EXIF, XMP, IPTC, ICC, and Maker Note fields are discarded at the OS level.

2. **`connect-src 'none'` CSP header** — Even if malicious code were somehow injected, the browser blocks all outbound network connections. Your images are literally unable to leave the page.

3. **Object URL lifecycle** — Every `URL.createObjectURL()` call is revoked within seconds. No image data lingers in memory longer than needed.

4. **No third-party scripts** — Google Fonts CSS only (no JS). Zero analytics, zero tracking pixels, zero telemetry.

5. **Static hosting only** — There is no backend, no server-side code, no database.

6. **Filenames anonymised** — Output files are renamed to `image_001.jpg`, `image_002.jpg` etc. Android and other devices embed date/time in filenames (e.g. `IMG_20250723_103045.jpg`) which can reveal when a photo was taken even after metadata is stripped.

---

## Security Headers (vercel.json)

| Header | Value | Purpose |
|--------|-------|---------|
| `Content-Security-Policy` | `connect-src 'none'` | Blocks all outbound network requests |
| `Permissions-Policy` | `camera=(), microphone=(), geolocation=()` | Denies hardware access |
| `Referrer-Policy` | `no-referrer` | No data leaked via referer |
| `Strict-Transport-Security` | `max-age=63072000; includeSubDomains; preload` | HTTPS only, 2 years |
| `Cross-Origin-Opener-Policy` | `same-origin` | Isolates browsing context |
| `Cross-Origin-Embedder-Policy` | `require-corp` | Prevents cross-origin data leakage |
| `X-Frame-Options` | `DENY` | Cannot be iframed / clickjacked |
| `X-Content-Type-Options` | `nosniff` | MIME type cannot be overridden |

---

## What Gets Stripped

- GPS coordinates (latitude, longitude, altitude, bearing, speed)
- Device make and model (iPhone 16 Pro, Samsung Galaxy S24, etc.)
- Date and time the photo was taken
- Camera settings (ISO, shutter speed, aperture, focal length, flash)
- Software used (iOS Camera, Photoshop, Lightroom, etc.)
- Author / copyright / owner information
- Device serial number and unique identifiers
- Embedded thumbnail previews
- XMP and IPTC editorial metadata
- ICC color profile data
- Maker Notes (proprietary Apple / Samsung / Canon data)
- User comments and descriptions

---

## Supported Formats

| Format | Input | Output |
|--------|-------|--------|
| JPEG   | ✅    | JPEG (95% quality) |
| PNG    | ✅    | PNG (lossless) |
| WebP   | ✅    | WebP (95% quality) |
| HEIC   | ✅    | JPEG (export as JPEG from iPhone Photos first) |
| GIF    | ✅    | JPEG |
| BMP    | ✅    | JPEG |
| TIFF   | ✅    | JPEG |
| AVIF   | ✅    | JPEG |

> **Note on HEIC:** Apple has used HEIC as the default photo format since iPhone 7 (iOS 11, 2017). HEIC is not supported in browsers via Canvas — export your photos as JPEG from the iPhone Photos app before using NoTrace.

---

## Deploy to Vercel

### One-click (recommended)

1. Fork or clone this repo
2. Go to [vercel.com](https://vercel.com) → New Project
3. Import your GitHub repo (`github.com/gitpushnico/notrace`)
4. Click **Deploy** — done in ~30 seconds

No build step. No framework. Just static HTML.

### CLI

```bash
npm i -g vercel
vercel --prod
```

### Verify your security headers after deploy

```bash
curl -I https://your-domain.vercel.app
```

You should see `content-security-policy`, `permissions-policy`, `referrer-policy`, etc. in the response.

Or use [securityheaders.com](https://securityheaders.com) for a visual report.

---

## Local Development

No build tools needed. Just open the file:

```bash
# macOS
open index.html

# Or serve locally
npx serve .
# → http://localhost:3000
```

---

## Project Structure

```
notrace/
├── index.html      # Entire app — single file
├── vercel.json     # Deployment config + security headers
├── README.md       # This file
├── LICENSE         # MIT
└── .gitignore
```

---

## License

MIT — free to use, modify, and deploy.

---

Made by [@gitpushnico](https://github.com/gitpushnico)
