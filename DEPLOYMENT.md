# Deploying the Idris Ibraheem website

The site is a **static website**: `index.html` is the whole app (HTML, CSS, JavaScript and your portrait are inside it). There is no server, no database and no build step, so any host that can serve files will work.

```
idris-ibraheem-website/
├── index.html        the website and the Portal Management admin
├── site-data.json    OPTIONAL: your published content (see "Making portal edits permanent")
├── vercel.json       Vercel settings
├── render.yaml       Render settings
├── netlify.toml      Netlify settings
├── .nojekyll         tells GitHub Pages to serve files as they are
└── DEPLOYMENT.md     this guide
```

Test locally first: double-click `index.html`, or run `npx serve .` inside the folder and open the address it prints.

## Consultation request emails

The consultation form uses FormSubmit to email each request to `idiftech2010@gmail.com`, with a copy to `idiftech2010@yahoo.com`. FormSubmit is a third-party service and receives the submitted form details.

Before relying on delivery, submit a test request and complete the recipient-verification step FormSubmit sends to the primary Gmail address. Check the inbox and spam folder. The form reports a delivery failure if the service rejects a submission; successful delivery depends on FormSubmit and email providers.

---

## 1. Making portal edits permanent (read this first)

The Portal Management admin saves to **the browser you edit in** (localStorage). Visitors on other devices see the content shipped in the files, not your browser's edits. To publish your edits to everyone:

1. Open `#admin`, sign in, go to **Backup and security**, and choose **Export JSON**. A file named `site-data.json` downloads.
2. Put that file next to `index.html` (same folder).
3. Redeploy using any method below.

Visitors who have no saved edits of their own load `site-data.json` automatically. Your own browser keeps using its local edits, so use a private window to preview what visitors see.

Uploaded images are stored inside the JSON as compressed data. Keep each photo modest, and export a backup regularly. If a save fails with "Storage is full", export your backup and clear space by deleting unused images.

**Security note:** the portal password (`fuo2026` by default) is checked in the browser. Change it under **Backup and security** straight away. It keeps casual visitors out of the editor, but it is not server-grade security. Anyone editing in their own browser cannot change what other visitors see, because only your deployed files do that.

---

## 2. Vercel (recommended, free tier)

**Dashboard with GitHub**
1. Create a GitHub repository and upload the files in this folder.
2. Go to vercel.com, choose **Add New → Project**, and import the repository.
3. Framework preset: **Other**. Leave Build Command and Output Directory empty. Choose **Deploy**.
4. Every later `git push` redeploys automatically.

**Command line (no GitHub needed)**
```bash
npm install -g vercel
cd idris-ibraheem-website
vercel          # first run: answer the prompts, accept defaults
vercel --prod   # publish to production
```

**Custom domain:** Project → Settings → Domains → add your domain and follow the DNS instructions. HTTPS is automatic.

## 3. Netlify

- **Drag and drop:** open app.netlify.com/drop and drop the whole folder.
- **From Git:** New site from Git → pick the repository → build command empty, publish directory `.`
- **Domain:** Site configuration → Domain management.

## 4. Render (Static Site)

1. Push the folder to GitHub or GitLab.
2. In Render, choose **New → Static Site** and connect the repository.
3. Build Command: leave empty. Publish Directory: `.`
4. Choose **Create Static Site**. Render also reads `render.yaml` if you use **New → Blueprint**.
5. Add a custom domain under Settings → Custom Domains.

Use a **Static Site**, not a Web Service. Static Sites are free and do not sleep.

## 5. GitHub Pages

1. Create a repository and upload the files (keep `.nojekyll`).
2. Settings → Pages → Source: **Deploy from a branch** → branch `main`, folder `/ (root)`.
3. The site appears at `https://<username>.github.io/<repository>/`. A custom domain can be set on the same page.

## 6. Cloudflare Pages

1. Cloudflare dashboard → Workers & Pages → Create → Pages.
2. Either connect Git (no build command, output directory `/`) or choose **Upload assets** and drop the folder.

## 7. Firebase Hosting

```bash
npm install -g firebase-tools
firebase login
cd idris-ibraheem-website
firebase init hosting     # public directory: .   single-page app: No   overwrite index.html: No
firebase deploy
```

## 8. Normal web hosting (cPanel, Namecheap, Hostinger, Whogohost, Qservers and similar)

1. Log in to cPanel and open **File Manager**.
2. Open `public_html` (or the folder of your domain or subdomain).
3. Upload `index.html` (and `site-data.json` if you have one). You can upload the zip and use **Extract**.
4. Visit your domain. Nothing else is required: no PHP, no database.
5. Turn on free SSL: cPanel → **SSL/TLS Status** → **Run AutoSSL**.

Alternative: upload with FTP/SFTP (FileZilla) to the same `public_html` folder.

## 9. Your own server (VPS with Nginx or Apache)

**Nginx**
```nginx
server {
  listen 80;
  server_name example.com www.example.com;
  root /var/www/idris-ibraheem-website;
  index index.html;
  location / { try_files $uri $uri/ /index.html; }
}
```
Then run `sudo certbot --nginx -d example.com -d www.example.com` for free HTTPS.

**Apache:** copy the files to `/var/www/html` (or your virtual-host folder). No rewrite rules are needed, because navigation uses `#` links.

---

## 10. After deploying: checklist

- Open the live address and click through every menu item, including the Professional Services dropdown.
- Sign in at `#admin` and **change the portal password**.
- Submit a test consultation request and confirm the email and WhatsApp links open.
- Check the site on a phone.
- Update the company email and phone under **Portal management → Company: Idolans Info-Tech** so service pages point to the right contact.
- Export a backup (`site-data.json`) and keep a copy somewhere safe.

## 11. Running the site on a custom domain

Point your domain's DNS to the host you chose:
- Root domain (`example.com`): an `A` record or `ALIAS`/`ANAME` to the host's address, as the host instructs.
- `www`: a `CNAME` to the host's address (for example `cname.vercel-dns.com`).
DNS changes can take from a few minutes up to a day.

## 12. If you later need edits shared across devices

A static site cannot store visitor-visible edits by itself. When you want editing from any device without redeploying, move the portal data to a backend such as Firebase Firestore or Supabase and keep this front end unchanged. Ask for that upgrade when you are ready.
