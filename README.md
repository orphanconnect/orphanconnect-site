# OrphanConnect

Static website for [OrphanConnect](https://orphanconnect.org/), a Christian nonprofit in Centreville, Virginia. The pages are plain HTML and CSS. There is no build step and no application server.

## Edit the site

Each page is one HTML file in the repository root:

| Page | File | Public URL |
| --- | --- | --- |
| Home | `index.html` | `/` |
| About | `about.html` | `/about` |
| Gallery | `gallery.html` | `/gallery` |
| Get Involved | `get-involved.html` | `/get-involved` |
| Contact | `contact.html` | `/contact` |
| Not found | `404.html` | served for unknown URLs |

Shared layout (header, navigation, footer, and the organization schema block) is copied in each file. If you change the phone number, email, Donate button, or footer, update every page, `404.html`, and the JSON-LD script in the `<head>`.

Keep these facts as they are unless the organization decides otherwise:

- Phone: `703-881-8486` (`tel:+17038818486`) everywhere a number appears.
- Email: `dcho@orphanconnect.org`.
- Location: **Centreville, VA** only. Do not publish a street address, ZIP code, or map of a home address.
- Donate button: `https://paypal.me/orphanconnect?country.x=US` (header and footer).

After changing the JSON-LD script text, recompute its CSP hash or the browser will block the inline script. From the repository root:

```bash
python3 << 'PY'
import base64, hashlib, re
from pathlib import Path
text = Path("index.html").read_text()
script = re.search(r'<script type="application/ld\+json">(.*?)</script>', text).group(1)
print(base64.b64encode(hashlib.sha256(script.encode()).digest()).decode())
PY
```

Put the printed value in `script-src` inside `_headers` (the `sha256-...` token). The same JSON-LD block is on every page, so one hash covers all of them.

Copyright in the footer is the static line `© 2026 OrphanConnect`. Change the year in each HTML file when the year changes.

Styles live in `assets/css/styles.css`. Photos and graphics live in `assets/img/`. Do not hotlink images from Google or elsewhere. New photos should be WebP or optimized JPEG, at most about 1600 pixels wide, with a written `alt` and `width` and `height` attributes. Use `loading="lazy"` except for the image at the top of the home page.

`/assets/` is cached for one year (`_headers`). When you replace a picture, use a new filename or purge the Cloudflare cache, or visitors may keep the old file.

### Photos of children

The gallery, home page, and about page include photographs of identifiable children that were already public on the previous site. Captions do not add names or locations. Confirm you still have permission to publish each photo. To take one down, delete the file below and remove the matching `<figure>` from the HTML page.

Children’s faces:

- `assets/img/home-two-children.webp` (home)
- `assets/img/home-child-outdoors.webp` (home)
- `assets/img/home-tablets.webp` (home)
- `assets/img/home-meal.webp` (home)
- `assets/img/about-child.webp` (about)
- `assets/img/gallery-01.webp` through `assets/img/gallery-14.webp` (gallery)

These files do **not** show children’s faces: the logo, sunset banner, satellite illustration, volunteer graphics, handshake, heart graphics, and the social share image `assets/img/og.jpg`. The sunset banner is silhouettes only.

## Preview locally

From the repository root:

```bash
python3 -m http.server 8080
```

Open `http://127.0.0.1:8080/`. Cloudflare redirects in `_redirects` (for example `/home` and `/involved`) apply on Cloudflare Pages, not on this local server.

## Cloudflare Pages

Connect this repository and deploy from the `main` branch.

| Setting | Value |
| --- | --- |
| Framework preset | None |
| Build command | *(leave empty — there is no build)* |
| Output directory | `/` or `.` (the repository root) |

The output is the repository itself. Pages reads `_redirects`, `_headers`, `404.html`, `robots.txt`, and `sitemap.xml` from that root.

`_redirects` sends the old Google Sites paths to the new pages, with and without a trailing slash:

- `/home` and `/home/` → `/`
- `/about/` → `/about` (`/about` already serves `about.html`)
- `/gallery/` → `/gallery`
- `/involved` and `/involved/` → `/get-involved`
- `/contact/` → `/contact`
- `/get-involved/` → `/get-involved`
