# joelchen.com

Portfolio site for Joel Chen. Plain HTML, CSS and JS: no framework, no build step.

```
index.html              Home: hero, 3 project cards, about, experience, contact
projects/project-1.html Project pages (one per project, same layout)
projects/project-2.html
projects/project-3.html
styles.css              All styles. Colors and fonts are at the top in :root
script.js               Scroll fade-ins and ← / → keys on project pages
404.html                Not-found page
assets/                 Images, CV, favicon
```

## Adding content

Every spot that needs editing is marked with an `<!-- EDIT: ... -->` comment, and the placeholder text is in `[brackets]`.

**Images.** Gray boxes are placeholders. Their labels show where each file should go, e.g. `assets/project-1/hero.jpg`. To swap one in:

```html
<!-- before -->
<div class="hero-media ph reveal" data-label="Hero image 16:9 — assets/project-1/hero.jpg"></div>

<!-- after -->
<div class="hero-media reveal"><img src="../assets/project-1/hero.jpg" alt="Describe the image"></div>
```

Remove the `ph` class and put an `<img>` inside, or replace the div with the `<img>`. Keep images under about 500 KB each; export JPGs or WebPs at about 2000px wide.

**Files to add:**
- `assets/avatar.jpg`: round photo in the header
- `assets/about.jpg`: large photo in the About section
- `assets/cv.pdf`: CV download
- `assets/og.jpg`: 1200×630 image for link previews
- `assets/project-N/cover.jpg`, `hero.jpg`, `image-1.jpg` … `image-4.jpg`

**Project titles** appear in several places: the home page card, the project page `<title>` and `<h1>`, and the Previous/Next links on the neighboring project pages. Search for `[Project One Title]` and so on to find them all.

**Preview locally:** run `python3 -m http.server` in this folder and open http://localhost:8000.

## Deploying to joelchen.com (Cloudflare Pages)

### 1. Connect the GitHub repo to Cloudflare
1. Log in at https://dash.cloudflare.com, using the same account that holds `joelchen.com`.
2. In the left sidebar, open **Compute (Workers) → Workers & Pages**, then click **Create**.
3. Choose the **Pages** tab, then **Import an existing Git repository** (labeled "Connect to Git" in some dashboard versions).
4. Click **Connect GitHub**. When GitHub asks, install the Cloudflare app and give it access to the `joelchen` repo. ("Only select repositories" is fine.)
5. Select the `joelchen` repo and click **Begin setup**:
   - Project name: `joelchen`
   - Production branch: `main`
   - Framework preset: **None**
   - Build command: *(leave empty)*
   - Build output directory: `/`
6. Click **Save and Deploy**. The site goes live at `https://joelchen.pages.dev` in under a minute.

From then on, every push to `main` redeploys automatically, and branches and PRs get their own preview URLs.

### 2. Point joelchen.com at the site
Because the domain is already registered on Cloudflare, Cloudflare creates the DNS records for you.

1. In the Pages project, go to **Custom domains → Set up a custom domain**.
2. Enter `joelchen.com` and click **Continue → Activate domain**. Cloudflare adds a proxied CNAME record `joelchen.com → joelchen.pages.dev`.
3. Repeat for `www.joelchen.com`.
4. Wait for both to show **Active**, usually a few minutes. HTTPS certificates are issued automatically.

If a domain is stuck on "Verifying", go to **joelchen.com → DNS → Records**. Delete any old `A`, `AAAA` or `CNAME` records for `@` or `www` that point somewhere else, such as parking records. Then click **Retry** in the Pages custom domain screen.

Final DNS records should look like this:

| Type  | Name  | Content              | Proxy      |
|-------|-------|----------------------|------------|
| CNAME | `@`   | `joelchen.pages.dev` | Proxied ☁️ |
| CNAME | `www` | `joelchen.pages.dev` | Proxied ☁️ |

### 3. Redirect www to the bare domain (recommended)
1. Open the **joelchen.com** zone, then go to **Rules → Overview → Create rule → Redirect Rule**.
2. Pick the template **"Redirect from WWW to Root"**, or set it up by hand:
   - If hostname equals `www.joelchen.com`
   - Then: dynamic redirect to `concat("https://joelchen.com", http.request.uri.path)`, status **301**, preserve query string
3. Click **Deploy**.

### 4. Optional settings
- **SSL/TLS → Edge Certificates → Always Use HTTPS**: On
- **Workers & Pages → joelchen → Metrics → Web Analytics**: enable for free, privacy-friendly visitor stats
