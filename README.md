# MAC GLOBAL TRAVELS & TOURS — Website

A responsive, SEO-friendly travel agency website (HTML, Tailwind CSS via CDN, vanilla JS)
with a working admin dashboard for managing tour packages, blog posts, contact
messages, and site branding.

## Opening the site

This is a static multi-page site — no build step, no install. Just open
`index.html` in a browser, or serve the folder with any static server, e.g.:

```
npx serve .
# or
python3 -m http.server 8000
```

Open the folder in a server (not `file://`) if you want the Google Maps embed
and web fonts to behave perfectly consistently across browsers.

## Admin dashboard

Go to **`/admin/login.html`**.

- Email: `macservices950@gmail.com`
- Password: `0000`

**Change this password immediately** from Admin → Settings → Change Admin
Password. The password is hashed with bcrypt (via the `bcryptjs` library)
before it is ever stored — the plaintext password is not saved anywhere.

From the dashboard you can:
- Add / edit / delete **tour packages** (title, price, duration, image, description, featured flag)
- Add / edit / delete **blog posts**
- View and **reply** to contact/booking messages (save a written reply, or jump straight to WhatsApp)
- Change the **logo, brand colours, and all contact/social info** — these changes apply instantly across the public site

## ⚠️ Important: how data is stored (read this before going live)

This build has **no backend server or database** — it's pure HTML/CSS/JS, as
requested. To still deliver working CRUD out of the box, all content
(packages, blog posts, messages, settings, and the hashed admin password) is
stored in the browser's `localStorage` / `sessionStorage` **on whichever
device is used to manage the site**.

What that means in practice:
- ✅ Great for a demo, a single-operator setup, or as a starting point.
- ✅ Admin password is properly hashed with bcrypt, never stored in plaintext.
- ⚠️ Content edited in the admin panel is only visible **in that same browser**
  — it will not appear for site visitors on other devices, and clearing
  browser data will erase it.
- ⚠️ "Protected routes" here means the page checks for a valid session token
  and redirects to login if missing — solid for keeping casual visitors out,
  but not equivalent to real server-side authentication.
- ⚠️ Uploaded images are stored as base64 data (capped at ~1.5MB per image)
  rather than saved to a `/public` folder, since there is no server to write
  files to.

### Making it production-ready

To go live with real, shared, persistent content, wire this same front end
to a backend. The data layer is intentionally isolated in
`assets/js/data.js` (and the admin CRUD in `assets/js/admin.js`) so this is a
contained change:

1. Stand up a small API (Node/Express, Laravel, Django, etc.) with a
   database (Postgres/MySQL/MongoDB) holding `packages`, `blog_posts`,
   `messages`, and `settings` tables.
2. Replace the `mgtGet*` / `mgtSave*` / `mgtUpsert*` functions in
   `data.js` with `fetch()` calls to that API.
3. Move authentication server-side: hash passwords with `bcrypt` on the
   server, issue an HTTP-only signed session cookie or JWT on login, and
   check it on every admin API route.
4. Handle image uploads with `multipart/form-data` to a real `/public/uploads`
   folder (or S3/Cloudinary), storing the resulting URL instead of a data URL.

## Structure

```
/
├── index.html, about.html, services.html, packages.html, package.html,
│   visa.html, blog.html, blog-post.html, contact.html   ← public pages
├── admin/
│   ├── login.html
│   ├── dashboard.html
│   ├── packages.html   ← CRUD: tour packages
│   ├── blog.html        ← CRUD: blog/news
│   ├── messages.html    ← view & reply to enquiries
│   └── settings.html    ← logo, colours, contact info, change password
└── assets/
    ├── css/style.css     ← design tokens + shared components
    └── js/
        ├── data.js       ← localStorage data layer (settings/packages/blog/messages)
        ├── main.js       ← public site behaviour (nav, rendering, contact form)
        ├── auth.js       ← bcrypt-based admin login/session
        └── admin.js      ← admin CRUD rendering + modals
```

## Notes

- All "Book Now" / WhatsApp buttons deep-link to WhatsApp with a prefilled
  message using the number set in Settings (defaults to `+250795761171` as
  provided in the brief).
- Facebook, TikTok and WhatsApp Channel links are in the footer, the
  homepage "Follow Us" section, and the Contact page.
- The Contact page embeds a Google Maps view for the office location
  (defaults to `JG82+38 Ikorodu Lagos Nigeria`), editable from Settings.
- Replace the Unsplash placeholder photography with your own images —
  either via the admin uploaders (packages/blog/logo) or by editing the
  `image` URLs directly in `assets/js/data.js`.
