# 🏛️ Sarkari Naukri Portal (GovLinks) — पूरी Documentation

> **यह दस्तावेज़ हिंदी में है।** हर सेक्शन में स्क्रीनशॉट (hosted link) + हर मार्क का मतलब +
> "क्या करने से क्या होता है" लिखा है।

| | |
|---|---|
| **Project** | GovLinks — Sarkari Naukri Information Portal (PHP 8 + SQLite/MySQL) |
| **Live preview** | **[[http://srkari.rf.gd/](http://srkari.rf.gd/)](http://srkari.rf.gd/)** |
| **Admin panel** | **[[http://srkari.rf.gd/](http://srkari.rf.gd//admin/login.php)](http://srkari.rf.gd/)/admin/login.php** |
| **Admin email** | `dkkr5558@gmail.com` |
| **Admin password (demo)** | `Admin@123` *(बदल दें — Admin → Profile)* |
| **Documentation date** | 2026-10-07 |
| **PHP version (preview)** | PHP 8.4.26 · SQLite |

---

## 📖 मार्किंग का मतलब (Legend)

स्क्रीनशॉट में जो रेखाएँ/बॉक्स/तीर हैं:

| रंग | मतलब |
|-----|-------|
| 🟢 **Green (हरा बॉक्स + तीर)** | यह एक **फीचर / सही action** है — यहाँ क्लिक या भरें |
| 🔴 **Red (लाल बॉक्स)** | **खतरा / गलती** — यहाँ गलत करने से data loss, security risk या site टूट सकती है |
| 🟡 **Amber (पीला)** | **ध्यान दें** — auto behaviour या जिसका असर अपने-आप होता है |
| 🔵 **Blue (नीला)** | **जानकारी / लिंक** — कहाँ जाता है, क्या जोड़ता है |

हर स्क्रीनशॉट के ऊपर एक **title bar** है (पेज का नाम) और फीचर पर **numbered badge (1, 2, 3…)** —
नीचे टेबल में उसी नंबर का मतलब लिखा है।

---

## 🗂️ Table of Contents

1. [झटपट शुरू कैसे करें (Quick Start)](#1-झटपट-शुरू-कैसे-करें-quick-start)
2. [Site खोलने के बाद सबसे पहले क्या करें](#2-site-खोलने-के-बाद-सबसे-पहले-क्या-करें)
3. [Features — एक नज़र में](#3-features--एक-नज़र-में)
4. [Front Site — पेज दर पेज (हर सेक्शन की समझ)](#4-front-site--पेज-दर-पेज)
5. [Admin Panel — पेज दर पेज](#5-admin-panel--पेज-दर-पेज)
6. [क्या करने से क्या होता है — Master Table](#6-क्या-करने-से-क्या-होता-है--master-table)
7. [Customization — कहाँ क्या बदलें](#7-customization--कहाँ-क्या-बदलें)
8. [File Structure — कौन सी फाइल क्या करती है](#8-file-structure--कौन-सी-फाइल-क्या-करती-है)
9. [`.env` Configuration](#9-env-configuration)
10. [SEO — क्या-क्या पहले से चालू है](#10-seo--क्या-क्या-पहले-से-चालू-है)
11. [Security — क्या सुरक्षा है](#11-security--क्या-सुरक्षा-है)
12. [Live कराने से पहले Checklist + Troubleshooting](#12-live-कराने-से-पहले-checklist--troubleshooting)

---

## 1. झटपट शुरू कैसे करें (Quick Start)

### A) Localhost / किसी भी PHP host पर

```bash
# 1) Folder copy करें (Apache/nginx/PHP built-in server — कोई भी चलेगा)
cd govlinks

# 2) सबसे आसान local preview
php -S 0.0.0.0:8000 server.php

# 3) Browser में installer खोलें
http://localhost:8000/install.php
```

Installer 4 steps में: **requirements check → DB choose (SQLite/MySQL) → site name + admin account → install**.
Install होते ही `storage/installed.lock` बनता है और installer खुद lock हो जाता है।

### B) Web installer के बिना (जब DB पहले से बनी हो — जैसे इस zip में)

```bash
cd govlinks
php -S 0.0.0.0:8000 server.php
# .env में APP_URL अपना domain लिखें, फिर /admin/login.php से लॉगिन करें
```

### C) Apache / nginx

- **Apache:** `.htaccess` पहले से मौजूद है (pretty URLs + gzip + cache + security headers)।
- **nginx:** जो real file नहीं, सब `/index.php` को भेजें:
  ```nginx
  location / { try_files $uri $uri/ /index.php$is_args$args; }
  ```
- `storage/` और `uploads/` **writable** होने चाहिए (755/775)।

### D) दोबारा install / reset

```bash
rm storage/installed.lock .env      # फिर /install.php खोलें
# या Admin → Backup → Factory Reset
```

### Requirements

| चीज़ | ज़रूरी |
|------|--------|
| PHP | **8.1+** (यहाँ 8.4.26 चल रहा है) |
| Extensions | `pdo_sqlite` (या `pdo_mysql`), `mbstring`, `fileinfo`, `curl` |
| Optional | `gd` (WebP thumbnails), `openssl` |
| DB | SQLite (zero-config) **या** MySQL/MariaDB |
| Cron | ❌ नहीं चाहिए |
| Composer | ❌ नहीं चाहिए |

---

## 2. Site खोलने के बाद सबसे पहले क्या करें

| क्रम | काम | कहाँ |
|------|-----|------|
| 1 | **Admin password बदलें** | Admin → Profile |
| 2 | Site name, tagline, logo | Admin → Settings → Site Identity |
| 3 | Hero title/subtitle (Hindi) | Admin → Settings → Homepage |
| 4 | Demo jobs/exam/posts हटाएँ | Admin → Backup → Delete Demo Content |
| 5 | `APP_URL` अपना असली domain | `.env` |
| 6 | Social links + Analytics code | Admin → Settings |
| 7 | SMTP सेट करें | Admin → SMTP |
| 8 | Sitemap submit करें | Google Search Console (`/sitemap.xml`) |

> ⚠️ **Demo content** में dates install-day के आसपास की हैं और links असली official sites के हैं —
> **live जाने से पहले असली notification से replace करें।**

---

## 3. Features — एक नज़र में

### Front site
- Mobile-first, light + dark mode (pure black + `#C6FF33` accent)
- Homepage: Trust card → Hero (Hindi tagline + live search + chips) → Categories → Latest Jobs (AJAX Load More) → Closing Soon → Results → Qualification → State → Organizations → Newsletter → Footer
- Single job page: quick facts, dates, fees, eligibility, selection, exam pattern, syllabus, how-to-apply, documents, **official links box**, related jobs, share, comments + star rating, sticky mobile apply bar
- Live AJAX search (suggestions dropdown) + full filter page
- 6 exam sections: Admit Card · Result · Answer Key · Cut Off · Syllabus · Updates
- `/go/...` interstitial — हर official link पर साफ चेतावनी + click tracking
- Notification bell, off-canvas mobile menu, breadcrumbs, sitemap.xml, robots.txt, 301 redirects

### Admin panel
- Dashboard (stats + 30-day SVG chart + top jobs + top searches + status bars)
- Jobs CRUD (har field, rich editor, media picker, tags, SEO, featured, draft/publish, bulk actions)
- Exam Updates CRUD (6 types, one page) · Posts & Pages CRUD · Media library (multi-upload, WebP thumbs, alt text)
- Taxonomy: Organizations, Categories (icon + color), Tags
- Users (admin/editor), Comments & Reviews moderation, Newsletter + Job Alerts (CSV export)
- Ads manager (5 positions, impressions/clicks/CTR) · Menus (header + footer)
- SEO + 301 redirects · Settings (identity, homepage, social, custom code) · SMTP email (test button)
- **AI Writer (Groq)**: summary, SEO title, meta description, tags, article, FAQ, rewrite, Hindi↔English
- Backup & Reset (SQLite/MySQL dump, demo delete, factory reset) · Profile

---

## 4. Front Site — पेज दर पेज


### Homepage — Topbar + Hero + Official Apply Trust Card

**URL:** `/`  
![Homepage — Topbar + Hero + Official Apply Trust Card](https://i.imgur.com/FQcE4Jh.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Header search — type करने पर AJAX suggestions |
| 2 | 🟢 | Hindi H1 — SEO का सबसे important tag |
| 3 | 🟢 | Live search box — All Jobs / Admit Card / Result tabs |
| 4 | 🟢 | Popular chips — 1 click में filtered search |
| 5 | 🟢 | CTA buttons — Get Started / Closing Soon / Free Job Alert |
| 6 | 🟢 | Live counters — jobs, official links, organizations |

**करने पर क्या होता है (Cause → Effect):**
- Hero title/subtitle Admin → Settings → Homepage से बदलें → homepage पर तुरंत बदल जाता है (DB setting)।
- Search box में type करें → 300ms बाद `/api.php?action=suggest` hit होता है और dropdown में jobs आते हैं (page reload नहीं)।
- Popular chips पर click → `/search?q=…` filtered results page खुलता है।
- Theme toggle (header) → `localStorage['gl-theme']` में save; visitor की next visit पर वही theme।

---

### Homepage — Popular Categories Section

**URL:** `/`  
![Homepage — Popular Categories Section](https://i.imgur.com/l05rxoE.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Category grid — icon + live job count |
| 2 | 🔵 | View All Categories → /jobs listing |

**करने पर क्या होता है (Cause → Effect):**
- Category card पर click → `/category/<slug>` page (उस category के सारे jobs)।
- Admin → Categories में icon/color बदलें → homepage card का icon + color बदलता है।
- Job count auto है → जैसे ही नया job उस category में publish होता है count बढ़ता है।

---

### Homepage — Latest Government Jobs + Sidebar

**URL:** `/`  
![Homepage — Latest Government Jobs + Sidebar](https://i.imgur.com/iWa95af.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Job card — vacancy, qualification, last date |
| 2 | 🟢 | Load More — AJAX, page reload नहीं होता |
| 3 | 🟢 | Sidebar — Featured jobs / Free Alert / Articles |

**करने पर क्या होता है (Cause → Effect):**
- Load More → AJAX से अगले 6 jobs जुड़ते हैं, scroll position और पेज वहीं रहता है।
- Job card पर click → single job page (views +1)।
- Free Job Alert → modal में email डालें → `job_alerts` table में save, Admin → Alerts में CSV export।

---

### Homepage — Closing Soon + Latest Results

**URL:** `/`  
![Homepage — Closing Soon + Latest Results](https://i.imgur.com/hTzAV9I.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟡 | Closing Soon — last date 7 दिन के अंदर |
| 2 | 🟢 | Latest Results — click पर detail page |

**करने पर क्या होता है (Cause → Effect):**
- Last date ≤ 7 दिन बचे → job अपने-आप 'Closing Soon' section + badge 'N days left' में आता है; कोई manual काम नहीं।
- Last date निकल जाए → badge 'Application closed' और listing से filter हो जाता है।
- Result row पर click → exam detail page।

---

### Homepage — Qualification + State Wise Browse

**URL:** `/`  
![Homepage — Qualification + State Wise Browse](https://i.imgur.com/JgAiF67.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Qualification wise filter — 12th / Graduate / ITI… |
| 2 | 🟢 | State wise filter — 33 states & UTs |

**करने पर क्या होता है (Cause → Effect):**
- Qualification chip → `/qualification/<slug>`; State chip → `/state/<slug>` — दोनों SEO friendly URLs।
- नया state/qualification सिर्फ Admin → Organizations/Categories या DB से जुड़ता है (homepage auto-read करता है)।

---

### Homepage — Organizations + Newsletter/Job Alert

**URL:** `/`  
![Homepage — Organizations + Newsletter/Job Alert](https://i.imgur.com/kblkgxG.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Top organizations — SSC, Railway, UPSC… |
| 2 | 🟢 | Newsletter — email DB में save, CSV export admin में |

**करने पर क्या होता है (Cause → Effect):**
- Organization card → `/organization/<slug>` — उस org के सारे jobs + exam updates।
- Newsletter email submit → `subscribers` table (double opt-in नहीं, सीधे active) → Admin → Newsletter में CSV export।

---

### Footer — Important Links + Disclaimer

**URL:** `/`  
![Footer — Important Links + Disclaimer](https://i.imgur.com/0Bh5qSN.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Footer columns — menu admin से control होता है |
| 2 | 🔴 | Disclaimer — जरूरी: site application accept नहीं करती |
| 3 | 🔵 | Social links — Admin → Settings में बदलें |

**करने पर क्या होता है (Cause → Effect):**
- Footer links Admin → Menus (Footer columns) से बदलें — hard-coded नहीं हैं।
- Disclaimer text Admin → Pages → 'Disclaimer' page से edit करें।
- Social links Admin → Settings → Social Links — खाली छोड़ेंगे तो icons गायब।

---

### Live AJAX Search Suggestions

**URL:** `/`  
![Live AJAX Search Suggestions](https://i.imgur.com/zeYrTAx.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Type करें — 300ms बाद API call |
| 2 | 🟢 | AJAX dropdown — API /api.php?action=suggest |
| 3 | 🔵 | Enter दबाएं → full search results page |

**करने पर क्या होता है (Cause → Effect):**
- Type करना शुरू करें → debounce 300ms → API call → dropdown; keyboard ↑↓ + Enter से select (site.js में handling)।
- Enter → `/search?q=…` (jobs + exam items दोनों_search होते हैं)।

---

### All Jobs Listing — Filters + Grid

**URL:** `/jobs`  
![All Jobs Listing — Filters + Grid](https://i.imgur.com/XwRoA6d.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Breadcrumb — SEO + navigation |
| 2 | 🟢 | Filters — keyword, category, state, qualification, org, status |
| 3 | 🟢 | Job card — click पर single job page |
| 4 | 🔵 | Pagination — SEO friendly URLs |

**करने पर क्या होता है (Cause → Effect):**
- Filter भरकर 'Filter' → URL में query params (`?category=ssc&state=bihar`) → link share करने योग्य, SEO friendly।
- Status filter 'closing' → `/jobs/closing-soon` — 7 दिन के अंदर बंद होने वाली jobs।
- Pagination → `?page=2` — server rendered, crawlable।

---

### 4.10 Single Job Page (सबसे important page)

#### Single Job Page — Hero + Quick Facts

**URL:** `/job/ssc-cgl-combined-graduate-level-examination-2026`  
![Single Job Page — Hero + Quick Facts](https://i.imgur.com/OgYF3IZ.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Breadcrumb — Home › Jobs › Job title |
| 2 | 🟢 | Job title (H1) — SEO title admin में override कर सकते हैं |
| 3 | 🟢 | Quick facts — vacancy, age, salary, last date auto-filled |

**करने पर क्या होता है (Cause → Effect):**
- Title बदलें → slug auto-follow करता है (जब तक आप manually edit न करें)।
- Quick facts खाली छोड़ें → 'As per notification' दिखता है (hard-coded fallback, खाली बॉक्स नहीं)।
- Views counter हर page load पर +1 — dashboard chart इसी से बनता है।


#### Single Job Page — Important Dates + Application Fee

**URL:** `/job/ssc-cgl-combined-graduate-level-examination-2026`  
![Single Job Page — Important Dates + Application Fee](https://i.imgur.com/Mk5o7xp.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Important Dates — last date के हिसाब से badge auto |
| 2 | 🟢 | Application Fee — category wise fee table |

**करने पर क्या होता है (Cause → Effect):**
- Last date आज से ≥ today → badge 'N days remaining'; 0 → 'Last day today!'; <0 → 'Application closed'।
- Fee table खाली → section अपने-आप छिप जाता है (खाली heading नहीं दिखती)।


#### Single Job Page — Eligibility + Overview

**URL:** `/job/ssc-cgl-combined-graduate-level-examination-2026`  
![Single Job Page — Eligibility + Overview](https://i.imgur.com/01BoQr4.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Eligibility & Age Limit — editor से rich text |
| 2 | 🟢 | Job Overview — JobPosting schema इसी से बनता है |

**करने पर क्या होता है (Cause → Effect):**
- Editor में list/table/bold → sanitize होकर front पर वैसा ही render (whitelist HTML)।
- Overview text से meta description auto-generate होती है अगर आप manual meta नहीं भरते।


#### Single Job Page — Selection Process, Exam Pattern, Syllabus

**URL:** `/job/ssc-cgl-combined-graduate-level-examination-2026`  
![Single Job Page — Selection Process, Exam Pattern, Syllabus](https://i.imgur.com/HaJkmiG.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Selection Process — stages step by step |
| 2 | 🟢 | Exam Pattern — marks, negative marking |
| 3 | 🟢 | Syllabus — topics list |

**करने पर क्या होता है (Cause → Effect):**
- Exam Pattern / Syllabus खाली → section hide; भरें → table/list जैसा डाला वैसा दिखता है।
- AI 'draft' button → Groq API call → text box भरता है (overwrite mode) — भरने के बाद ज़रूर पढ़ें।


#### Single Job Page — How to Apply + Documents

**URL:** `/job/ssc-cgl-combined-graduate-level-examination-2026`  
![Single Job Page — How to Apply + Documents](https://i.imgur.com/UWR82DN.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | How to Apply — step-by-step guide |
| 2 | 🟢 | Documents Required — checklist |

**करने पर क्या होता है (Cause → Effect):**
- How to Apply में steps डालें → user को step-by-step guide; official link अलग box में रहता है।
- Documents checklist → friction कम होता है, bounce rate घटता है।


#### Single Job Page — Important Links (Official Apply)

**URL:** `/job/ssc-cgl-combined-graduate-level-examination-2026`  
![Single Job Page — Important Links (Official Apply)](https://i.imgur.com/KiA8MW8.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Official Apply Link — /go/job/{id} से redirect + click tracking |
| 2 | 🔴 | Disclaimer line — “site application accept नहीं करती” |

**करने पर क्या होता है (Cause → Effect):**
- Official Apply Link = `/go/job/<id>` → पहले interstitial page (साफ चेतावनी), फिर official site; click `apply_clicks` में +1।
- Notification PDF link = `/go/notification/<id>` → `notification_clicks` +1।
- Direct official URL डालने से tracking नहीं होगा — tracking चाहिए तो `/go` route ही use करें।


#### Single Job Page — Comments & Star Reviews

**URL:** `/job/ssc-cgl-combined-graduate-level-examination-2026`  
![Single Job Page — Comments & Star Reviews](https://i.imgur.com/mgnEacr.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Comment form — admin approval के बाद ही public |
| 2 | 🟡 | Moderation queue Admin → Comments में |

**करने पर क्या होता है (Cause → Effect):**
- Visitor comment देता है → status 'pending' → Admin → Comments में Approve करने के बाद ही public।
- Star rating → job card + listing पर average rating दिखता है।
- Spam mark → front से गायब, admin में रिकॉर्ड रहता है।


---

### 4.11 Exam Sections (Admit Card / Result / Answer Key / Cut Off / Syllabus)

#### Exam Section — Admit Card Listing

**URL:** `/admit-card`  
![Exam Section — Admit Card Listing](https://i.imgur.com/uu3HZcZ.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | List row — click पर detail page |
| 2 | 🟢 | Section heading — 6 exam types में same template |

**करने पर क्या होता है (Cause → Effect):**
- 6 exam sections एक ही template से चलते हैं: /admit-card, /results, /answer-key, /cut-off, /syllabus, /updates।
- Admin → Exam Updates में type चुनें → वही section; slug duplicate नहीं होना चाहिए।


#### Exam Section — Admit Card Detail Page

**URL:** `/admit-card/ssc-cgl-tier-1-admit-card-2026-download`  
![Exam Section — Admit Card Detail Page](https://i.imgur.com/SOI7pDc.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Breadcrumb — SEO + back navigation |
| 2 | 🟢 | Title — SEO title अलग से set कर सकते हैं |

**करने पर क्या होता है (Cause → Effect):**
- Release/Exam date भरें → list + detail page पर date chip दिखता है।
- SEO title/meta अलग से भरें → Google में title वही जाएगा (job title नहीं)।


#### Exam Section — Results Listing

**URL:** `/results`  
![Exam Section — Results Listing](https://i.imgur.com/k5DzAhv.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Result row — PDF/official link detail page पर |

**करने पर क्या होता है (Cause → Effect):**
- Result publish → homepage 'Latest Results' section में auto आता है (date desc)।
- Status 'draft' → सिर्फ admin में दिखता है, site पर नहीं।


#### Exam Section — Syllabus Detail Page

**URL:** `/syllabus/upsc-cse-syllabus-2026-prelims-mains`  
![Exam Section — Syllabus Detail Page](https://i.imgur.com/VOYYEcc.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Syllabus detail — rich content editor से आता है |

**करने पर क्या होता है (Cause → Effect):**
- Syllabus content rich editor से → tables/lists supported।
- Related items sidebar → same type के latest 5 items (bounce कम करता है)।


---

### 4.12 Articles, Pages, Organizations, States

#### Articles & Guides Listing

**URL:** `/posts`  
![Articles & Guides Listing](https://i.imgur.com/awp2zc4.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Posts list — Admin → Posts से manage |

**करने पर क्या होता है (Cause → Effect):**
- Post type 'article' → /posts listing; 'update' → homepage updates strip।
- New Post → title, excerpt, featured image, tags, SEO — publish पर तुरंत live।


#### Article Single — Share Buttons + Related

**URL:** `/post/how-to-fill-ssc-online-form-2026-step-by-step-guide`  
![Article Single — Share Buttons + Related](https://i.imgur.com/WMttcmP.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Share buttons — WhatsApp/Telegram/Facebook/Copy |
| 2 | 🟢 | Article heading |

**करने पर क्या होता है (Cause → Effect):**
- Share buttons → WhatsApp/Telegram/Facebook + Copy Link (JS, कोई tracking नहीं)।
- Featured image → og:image (social share पर वही फोटो)।
- Related articles → same listing के नीचे auto।


#### Static Page — About Us

**URL:** `/page/about-us`  
![Static Page — About Us](https://i.imgur.com/6Z5y58J.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Static page — Admin → Pages से edit |

**करने पर क्या होता है (Cause → Effect):**
- Static pages: About, Disclaimer, Privacy, Terms — Admin → Pages से edit।
- Slug बदलें → URL बदलेगा; पुराना URL 404 देगा (Admin → SEO में 301 डालें)।


#### Organizations Listing

**URL:** `/organizations`  
![Organizations Listing](https://i.imgur.com/eSdY2YB.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Organization cards — job count के साथ |

**करने पर क्या होता है (Cause → Effect):**
- Organization page → jobs + count; org के नाम से brand trust बनता है।
- Admin → Organizations में org inactive करें → dropdown से गायब, पुराने jobs पर 'None' नहीं आता (join safe)।


#### State Wise Jobs

**URL:** `/states`  
![State Wise Jobs](https://i.imgur.com/xMFShgR.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | State cards — 33 states & UTs |

**करने पर क्या होता है (Cause → Effect):**
- State card → `/state/<slug>`; count auto (published jobs)।
- किसी state में 0 jobs → card फिर भी दिखता है (count 0) — छिपाना हो तो DB/query में filter करें।


---

### 4.13 Redirect Page, 404, Search, Theme, Mobile

#### /go Redirect Interstitial — Click Tracking

**URL:** `/go/job/1`  
![/go Redirect Interstitial — Click Tracking](https://i.imgur.com/itX4epZ.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Redirect notice — user को बताता है official site पर जा रहे हैं |
| 2 | 🟢 | Destination URL — साफ-साफ दिखाया जाता है |
| 3 | 🟢 | Continue button — click count DB में +1 |

**करने पर क्या होता है (Cause → Effect):**
- यह page user को बताता है कि वह official site पर जा रहा है (trust + compliance)।
- 1.8s बाद auto-redirect + Continue button → click count DB में (Admin → Ads/Jobs stats)।
- अगर आप `/go` हटा दें → tracking बंद, लेकिन user को चेतावनी भी नहीं मिलेगी (recommended: रखें)।


#### 404 Not Found Page

**URL:** `/yeh-page-exist-nahi-karta`  
![404 Not Found Page](https://i.imgur.com/GKzqArM.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟡 | Custom 404 — SEO friendly, 3 quick links |

**करने पर क्या होता है (Cause → Effect):**
- गलत URL → custom 404 + 3 quick links (bounce कम)।
- Slug बदलने से पुराने links 404 → Admin → SEO → 301 Redirects में map करें।


#### Search Results Page

**URL:** `/search?q=ssc`  
![Search Results Page](https://i.imgur.com/LSzvcp7.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Search results — jobs + exam items दोनों |

**करने पर क्या होता है (Cause → Effect):**
- Search jobs + exam items दोनों खोजता है; keyword match title/tags/organization पर।
- Empty result → 'Browse All Jobs' CTA (user flow नहीं टूटता)।


#### Dark Mode — pure black + #C6FF33 accent

**URL:** `/`  
![Dark Mode — pure black + #C6FF33 accent](https://i.imgur.com/02GwyWd.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Dark theme — visitor की choice localStorage में save |
| 2 | 🟢 | Same content, dark palette |

**करने पर क्या होता है (Cause → Effect):**
- Dark mode pure black + #C6FF33 accent; preference localStorage में।
- CSS variables (`--bg`, `--primary`…) बदलें → पूरी site का रंग बदलता है (assets/style.css)।


#### Mobile View — 390px

**URL:** `/`  
![Mobile View — 390px](https://i.imgur.com/lcDrUVm.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Mobile — CTA buttons stack हो जाते हैं |
| 2 | 🟢 | Categories — mobile पर 2 columns |

**करने पर क्या होता है (Cause → Effect):**
- Mobile पर grid 1-2 column, sticky mobile apply bar नीचे (job page पर)।
- CSS responsive breakpoints assets/style.css में; JS vanilla, कोई framework नहीं।


---

## 5. Admin Panel — पेज दर पेज

**Login:** `/admin/login.php` — email + password, CSRF token, 5 गलत attempts पर throttle.


### Admin — Login (throttling + CSRF)

**URL:** `/admin/login.php`  
![Admin — Login (throttling + CSRF)](https://i.imgur.com/vGntKMx.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Login form — 5 गलत try पर temporary lock |
| 2 | 🟢 | Email — DB users table से match |
| 3 | 🔴 | Password — password_hash / bcrypt |

**करने पर क्या होता है (Cause → Effect):**
- 5 बार गलत password → temporary throttle (IP + email) — brute-force रुकता है।
- Session: httponly cookie, SameSite, login के समय regenerate id।
- Password भूल जाएं → DB में `password_hash` reset (PHP: password_hash) या install.php re-run।

---

### 5.2 Dashboard

#### Admin Dashboard — Stats + 30-Day Chart

**URL:** `/admin/index.php`  
![Admin Dashboard — Stats + 30-Day Chart](https://i.imgur.com/T2URdGV.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | 5 stat cards — jobs, views, apply clicks, subscribers, pending |
| 2 | 🟢 | 30-day views chart — pure SVG, कोई library नहीं |
| 3 | 🟢 | Sidebar menu — 20+ admin pages |

**करने पर क्या होता है (Cause → Effect):**
- Stats live DB queries से — कोई cron/cache नहीं, हमेशा fresh numbers।
- Chart pure SVG (30 दिन) — `page_views` table से; सिर्फ logged days ही count होते हैं।
- Sidebar में 20+ pages — role 'editor' को वही दिखते हैं (admin-only pages अलग)।


#### Admin Dashboard — Top Jobs, Searches, Status

**URL:** `/admin/index.php`  
![Admin Dashboard — Top Jobs, Searches, Status](https://i.imgur.com/HfNaFaa.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Top Jobs by Views + Apply clicks |
| 2 | 🟢 | Top Searches — लोग क्या ढूंढ रहे हैं |
| 3 | 🟢 | Jobs by Status — published / draft / closed |
| 4 | 🔵 | System info — PHP, DB, version |

**करने पर क्या होता है (Cause → Effect):**
- Top Searches → आपको बताता है कौन-सा exam/job demand में है → वही content पहले लिखें।
- Jobs by Status → draft/published/closed ratio; draft ज्यादा हैं तो publish pipeline देखें।


---

### 5.3 Jobs (सबसे ज़्यादा use होने वाला हिस्सा)

#### Admin → Jobs — List, Filters, Bulk Actions

**URL:** `/admin/jobs.php`  
![Admin → Jobs — List, Filters, Bulk Actions](https://i.imgur.com/6KRU8fE.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Search + filters (status, category, org) |
| 2 | 🟢 | Jobs table — row पर click = edit |
| 3 | 🟡 | Bulk action — publish/draft/feature/delete एक साथ |
| 4 | 🟢 | Add New Job button |

**करने पर क्या होता है (Cause → Effect):**
- Search + filters → बड़ी listing में भी job ढूंढना आसान।
- Bulk action (publish/draft/feature/delete) → एक साथ कई jobs; delete permanent है, undo नहीं।
- Row पर click → edit form; 'Add New Job' ऊपर।


#### Admin → Jobs → Edit — Basic Fields

**URL:** `/admin/jobs.php?view=edit&id=1`  
![Admin → Jobs → Edit — Basic Fields](https://i.imgur.com/9TeHqvd.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Title → slug auto-generate होता है |
| 2 | 🔴 | Status: Draft = site पर नहीं दिखेगा, Published = live |
| 3 | 🟢 | Organization / Category / State / Qualification |

**करने पर क्या होता है (Cause → Effect):**
- Status = Draft → site पर नहीं दिखेगा (listing queries `status='published'` filter करती हैं)।
- Status = Closed → page खुलता है पर badge 'Closed' + apply CTA छिप सकता है।
- Slug manually बदलें → URL बदलता है; duplicate slug → conflict, unique रखें।


#### Admin → Jobs → Edit — Dates, Vacancy, Fee, Links

**URL:** `/admin/jobs.php?view=edit&id=1`  
![Admin → Jobs → Edit — Dates, Vacancy, Fee, Links](https://i.imgur.com/s8Q69jY.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Important Dates — last date से “closing soon” auto |
| 2 | 🟢 | Application Fee — category wise |
| 3 | 🟢 | Official Links — Apply / Notification PDF / Website |

**करने पर क्या होता है (Cause → Effect):**
- Last date = आज से आगे → 'days remaining' badge; आज → 'Last day'; बीत गया → 'Application closed'।
- Notification start date आने तक badge 'Notification coming soon' (अगर last date future में है)।
- Official links में `/go` URLs हैं — apply clicks yahin track होते हैं।


#### Admin → Jobs → Edit — Rich Editor + AI Buttons

**URL:** `/admin/jobs.php?view=edit&id=1`  
![Admin → Jobs → Edit — Rich Editor + AI Buttons](https://i.imgur.com/0I2Xr9h.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Editor toolbar — bold, list, table, link |
| 2 | 🟢 | AI buttons — Groq API से summary/SEO/tags |

**करने पर क्या होता है (Cause → Effect):**
- Editor toolbar → bold/italic/list/table/link; paste पर sanitize (script tags हट जाते हैं)।
- AI buttons → Groq key चाहिए; key न होने पर button click पर error toast।
- AI से generate होने के बाद ज़रूर proof-read करें — AI vacancy/date नहीं बनाता (prompt rule) पर भाषा गलत हो सकती है।


#### Admin → Jobs → Edit — Publish / Featured Image / SEO

**URL:** `/admin/jobs.php?view=edit&id=1`  
![Admin → Jobs → Edit — Publish / Featured Image / SEO](https://i.imgur.com/XCTGKPN.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Publish box — status + date |
| 2 | 🟢 | Featured Image — media picker |
| 3 | 🟢 | SEO — title, meta description, og:image |

**करने पर क्या होता है (Cause → Effect):**
- Featured checkbox ON → homepage 'Featured' panel + job card पर 'Featured' badge।
- Featured image → job card + og:image; media picker से choose (upload नहीं करना पड़ता)।
- SEO title/meta खाली → job title + auto excerpt use होता है (OG tags में भी)।


---

### 5.4 Exam Updates, Posts, Pages, Media, Menus

#### Admin → Exam Updates — 6 Types, One Page

**URL:** `/admin/exam.php`  
![Admin → Exam Updates — 6 Types, One Page](https://i.imgur.com/xKVMtjq.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Admit Card / Result / Answer Key / Cut Off / Syllabus / Update |

**करने पर क्या होता है (Cause → Effect):**
- Type tabs: Admit Card / Result / Answer Key / Cut Off / Syllabus / Update — एक ही CRUD।
- Bulk publish/draft/delete → एक साथ कई rows।
- Published item → site के उस section + homepage (Result/Admit card strips) में auto।


#### Admin → Exam → Edit Admit Card

**URL:** `/admin/exam.php?view=edit&type=admit_card&id=1`  
![Admin → Exam → Edit Admit Card](https://i.imgur.com/98DKo0P.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Basic fields — title, org, dates |
| 2 | 🟢 | Content editor |
| 3 | 🟢 | Publish box |

**करने पर क्या होता है (Cause → Effect):**
- Fields: title, org, release date, exam date, content, SEO; status publish/draft।
- Slug auto from title; duplicate → error/unique constraint।


#### Admin → Posts & Articles

**URL:** `/admin/posts.php`  
![Admin → Posts & Articles](https://i.imgur.com/VCMk27a.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Articles list — write new post बटन ऊपर |

**करने पर क्या होता है (Cause → Effect):**
- Post = article (blog) या update (homepage strip) — type dropdown से चुनें।
- Filters: status + search; bulk actions same as jobs।


#### Admin → Posts → Edit (Featured Image + SEO)

**URL:** `/admin/posts.php?view=edit&id=1`  
![Admin → Posts → Edit (Featured Image + SEO)](https://i.imgur.com/wlUX2YG.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Title + slug + excerpt |
| 2 | 🟢 | Featured Image — og:image भी यही |

**करने पर क्या होता है (Cause → Effect):**
- Excerpt → listing card + meta description fallback।
- Featured image → listing thumbnail + og:image; pages में भी यही feature available।


#### Admin → Pages (About, Disclaimer, Privacy…)

**URL:** `/admin/pages.php`  
![Admin → Pages (About, Disclaimer, Privacy…)](https://i.imgur.com/uFtrBol.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Static pages — footer links इन्हीं से जुड़ते हैं |

**करने पर क्या होता है (Cause → Effect):**
- Static pages (About/Privacy/Disclaimer/Terms) — footer links इन्हीं से जुड़े हैं।
- Slug change → footer link auto update (menu slug से match करता है)।


#### Admin → Media Library

**URL:** `/admin/media.php`  
![Admin → Media Library](https://i.imgur.com/oSjKjh9.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Upload — MIME check, random filename, WebP thumb |
| 2 | 🟢 | Library — alt/title edit, search, bulk delete |

**करने पर क्या होता है (Cause → Effect):**
- Upload → fileinfo MIME check, random filename, WebP thumbnail (GD होने पर) → `/uploads`।
- Alt text → SEO + accessibility; search box → title/alt/original name से filter।
- Delete → file भी delete; uploads folder में PHP execution `.htaccess` से blocked।


#### Admin → Menus (Header + Footer)

**URL:** `/admin/menus.php`  
![Admin → Menus (Header + Footer)](https://i.imgur.com/YDFo8Ma.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Menu items drag नहीं, sort order + URL से control |

**करने पर क्या होता है (Cause → Effect):**
- Header menu → site header nav; footer columns → footer links।
- Sort order बदलें → क्रम बदलता है; URL manually (`/jobs`, `/results`) या बाहरी link।
- Menu item delete → footer/header से link हटता है (page itself safe)।


---

### 5.5 Taxonomy + Engagement

#### Admin → Organizations

**URL:** `/admin/organizations.php`  
![Admin → Organizations](https://i.imgur.com/n0ZBxIv.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Organizations — short name, website, logo |

**करने पर क्या होता है (Cause → Effect):**
- Organization add → jobs form के dropdown में तुरंत available।
- Website field → organization page पर official link (`/go` tracked नहीं, direct)।


#### Admin → Categories (icon + color)

**URL:** `/admin/categories.php`  
![Admin → Categories (icon + color)](https://i.imgur.com/dZcRme1.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Category — icon + color + sort order |

**करने पर क्या होता है (Cause → Effect):**
- Category icon + color → homepage cards + job page badge का रंग।
- Sort order → homepage grid में position।
- Category delete → उसके jobs 'Uncategorized' नहीं होते, category_id null (safe)।


#### Admin → Tags

**URL:** `/admin/tags.php`  
![Admin → Tags](https://iili.io/n1pap44.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Tags — comma separated add |

**करने पर क्या होता है (Cause → Effect):**
- Tags comma separated → job/post tags; tag page `/tag/<slug>` auto।
- Tags SEO title + AI suggest (jobs form में) से जुड़ते हैं।


#### Admin → Comments & Reviews Moderation

**URL:** `/admin/comments.php`  
![Admin → Comments & Reviews Moderation](https://iili.io/n1pcHa2.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Approve / Pending / Spam / Delete |

**करने पर क्या होता है (Cause → Effect):**
- Approve → public; Pending → सिर्फ admin; Spam → hidden + record; Delete → permanent।
- Reply field → job page पर threaded reply (admin badge के साथ)।


#### Admin → Newsletter Subscribers

**URL:** `/admin/newsletter.php`  
![Admin → Newsletter Subscribers](https://iili.io/n1pcJvS.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Subscribers + CSV export |

**करने पर क्या होता है (Cause → Effect):**
- Subscribers list + CSV export → अपने ESP (Mailchimp/Brevo) में import करें।
- Delete → unsubscribe permanent; status inactive = unsubscribed।


#### Admin → Job Alerts

**URL:** `/admin/alerts.php`  
![Admin → Job Alerts](https://iili.io/n1pcFje.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Job alerts — email + filters + CSV export |

**करने पर क्या होता है (Cause → Effect):**
- Job alerts (email + filters) → CSV export; SMTP set होने पर आप mail भेज सकते हैं।
- Filters (category/state/qualification) → targeted alert भेजने में काम आते हैं।


#### Admin → Ads Manager (5 positions + CTR)

**URL:** `/admin/ads.php`  
![Admin → Ads Manager (5 positions + CTR)](https://iili.io/n1pcqCb.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Ad slots — impressions, clicks, CTR |

**करने पर क्या होता है (Cause → Effect):**
- 5 positions: header, hero, in_content, sidebar, footer — HTML code या image upload।
- Impressions/clicks/CTR auto-track (`/go/ad/<id>`) — कौन-सा slot चल रहा है, पता चलता है।
- Image ad → click पर `/go/ad/` → official sponsor site (nofollow sponsored)।


---

### 5.6 System: SEO, Settings, SMTP, AI, Backup, Users, Profile

#### Admin → SEO & 301 Redirects

**URL:** `/admin/seo.php`  
![Admin → SEO & 301 Redirects](https://iili.io/n1pcBGj.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Default SEO — meta title/description, OG image |
| 2 | 🟢 | SEO Status — kya on hai, kya missing hai |
| 3 | 🟢 | 301 Redirects — old URL → new URL (bulk delete) |

**करने पर क्या होता है (Cause → Effect):**
- Default SEO → meta title pattern, description, OG image → सारे pages पर fallback।
- 301 Redirects → purana URL → naya URL; bulk delete supported।
- robots.txt editor → `/robots.txt` उसी content से serve होता है।


#### Admin → Settings (Identity, Homepage, Social)

**URL:** `/admin/settings.php`  
![Admin → Settings (Identity, Homepage, Social)](https://iili.io/n1pco3Q.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Site Identity — name, logo, tagline |
| 2 | 🟢 | Homepage texts — hero title/subtitle यहीं बदलें |

**करने पर क्या होता है (Cause → Effect):**
- Site identity (name, logo, tagline) → header/footer/title/emails।
- Homepage hero title/subtitle → homepage H1 + tagline (यहीं से Hindi text बदलें)।
- Social links → footer icons; analytics code → custom code box में डालें।


#### Admin → Settings — Groq API Key + Custom Code

**URL:** `/admin/settings.php`  
![Admin → Settings — Groq API Key + Custom Code](https://iili.io/n1pcuu1.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Custom head/footer code — Analytics, GTM, Adsense |
| 2 | 🟢 | Groq API key — AI Writer इसी से चलता है |

**करने पर क्या होता है (Cause → Effect):**
- Custom head code → Google Analytics/GTM/Search Console tag; footer code → chat widgets।
- Groq API key → AI Writer + jobs form के AI buttons चालू; key server-side रहती है (browser में expose नहीं)।
- Key खाली → AI features बिना error के disabled रहते हैं (site नहीं टूटती)।


#### Admin → SMTP Email (Test Mail)

**URL:** `/admin/smtp.php`  
![Admin → SMTP Email (Test Mail)](https://iili.io/n1pca6v.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | SMTP settings — Gmail/Outlook/Zoho presets |
| 2 | 🟢 | Test email — save से पहले verify करें |

**करने पर क्या होता है (Cause → Effect):**
- SMTP save → credentials DB में (server-side); Test email भेजकर verify करें।
- Gmail → App Password ज़रूरी (normal password block); port 465/SSL या 587/TLS।
- SMTP बिना set किए newsletter/alerts mail नहीं जाएँगे (data DB में सुरक्षित रहता है)।


#### Admin → Backup & Reset

**URL:** `/admin/backup.php`  
![Admin → Backup & Reset](https://iili.io/n1pcG9I.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Backup download — .sqlite / .sql, 1 click |
| 2 | 🔴 | Warning — reset undo नहीं होता, pehle backup lein |

**करने पर क्या होता है (Cause → Effect):**
- Download backup → SQLite `.sqlite` file या MySQL `.sql` dump — restore = file replace/import।
- Delete demo content → jobs/exam/posts/comments हटते हैं, settings + admin safe।
- Factory reset → सारा content clear (admin + settings + uploads folder safe), undo नहीं — pehle backup लें।


#### Admin → AI Writer (Groq)

**URL:** `/admin/ai-writer.php`  
![Admin → AI Writer (Groq)](https://iili.io/n1pcwPf.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | AI tools — summary, SEO title, article, FAQ, translate |

**करने पर क्या होता है (Cause → Effect):**
- Tools: summary, SEO title, meta description, tags, article, FAQ, rewrite, Hindi↔English translate।
- Prompt rule: AI vacancy/date/fee/link कभी invent नहीं करता — facts आप भरें।
- Output copy करें → jobs/posts editor में paste + proof-read (AI Hindi/Hinglish मिला सकता है)।


#### Admin → Users (admin / editor roles)

**URL:** `/admin/users.php`  
![Admin → Users (admin / editor roles)](https://iili.io/n1pc89S.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🟢 | Users — Admin = full, Editor = content only |

**करने पर क्या होता है (Cause → Effect):**
- Admin = full access (settings, users, backup, smtp); Editor = सिर्फ content (jobs/exam/posts/pages/media)।
- Editor login → admin-only pages पर 403; password reset यहीं से करें।


#### Admin → Profile (name/email/password)

**URL:** `/admin/profile.php`  
![Admin → Profile (name/email/password)](https://iili.io/n1pcgte.png)

| # | मार्क | क्या है |
|---|-------|---------|
| 1 | 🔴 | Password change — install के तुरंत बाद बदलें |

**करने पर क्या होता है (Cause → Effect):**
- Name/email/password change → अगले login से नई credentials।
- Install के तुरंत बाद default password बदलें (security) — .env/db में कोई default न छोड़ें।


---

## 6. क्या करने से क्या होता है — Master Table

| अगर आप ये करेंगे… | तो site पर ये होगा… | कहाँ |
|---|---|---|
| Job **Status = Draft** | Job site पर कहीं नहीं दिखेगा (listing सिर्फ `published` लाती है) | Admin → Jobs → Edit |
| Job **Status = Closed** | Page खुलता है, badge "Closed", last-date badge "Application closed" | Admin → Jobs |
| **Featured = ON** | Homepage "Featured" panel + job card पर star badge | Admin → Jobs → Edit (sidebar) |
| **Last date ≤ 7 दिन** | अपने-आप "Closing Soon" section + "N days left" badge | Auto |
| **Last date बीत गई** | "Application closed" badge, closing list से बाहर | Auto |
| **Slug बदला** | URL बदला → पुराना URL **404** → Admin → SEO → 301 Redirect डालें | Admin → SEO |
| **Category का color/icon बदला** | Homepage card + job page badge का रंग/आइकन बदला | Admin → Categories |
| **Organization inactive** | Jobs form dropdown से गायब, पुराने jobs प्रभावित नहीं | Admin → Organizations |
| **Comment Approve** | Job page पर public; Pending = सिर्फ admin | Admin → Comments |
| **Newsletter/Alert email submit** | DB में save (CSV export); mail tabhi जाएगा जब SMTP set हो | Front form / Admin |
| **SMTP set + Test** | Newsletter/alerts mail भेज सकते हैं; Gmail में **App Password** ज़रूरी | Admin → SMTP |
| **Groq API key डाली** | AI Writer + jobs form के AI buttons चालू (server-side call) | Admin → Settings |
| **Groq key खाली** | AI features बिना error के बंद रहते हैं — site नहीं टूटती | — |
| **Media delete** | File भी `/uploads` से delete (undo नहीं) | Admin → Media |
| **Menu item delete** | Header/footer से link हटता है (page safe) | Admin → Menus |
| **Ads slot active** | 5 positions (header/hero/in_content/sidebar/footer) पर ad + CTR tracking | Admin → Ads |
| **Factory Reset** | सारा content clear; **admin + settings + uploads safe**; undo नहीं | Admin → Backup |
| **Delete Demo Content** | jobs/exam/posts/comments हटते हैं, settings वैसी रहती हैं | Admin → Backup |
| **APP_URL गलत** | canonical, sitemap, OG tags में गलत domain → SEO खराब | `.env` |
| **Theme toggle** | Visitor की choice `localStorage` में save → next visit वही theme | Front header |
| **Job delete** | **Permanent** — कोई trash नहीं | Admin → Jobs |

---

## 7. Customization — कहाँ क्या बदलें

| बदलना है | जाएँ | फाइल (अगर code से करना हो) |
|---|---|---|
| Site name / logo / tagline | Admin → Settings → Site Identity | `assets/img/favicon.svg`, `views/header.php` |
| Hero heading + subtitle (Hindi) | Admin → Settings → Homepage | `views/home.php` |
| रंग / theme (light + dark) | — | `assets/style.css` → `:root` CSS variables |
| Homepage sections का क्रम | — | `views/home.php` (section order) |
| Header / Footer menu | Admin → Menus | `views/header.php`, `views/footer.php` |
| Job card का layout | — | `views/partials/job-card.php` |
| Listing row (exam sections) | — | `views/partials/list-row.php` |
| Single job page के sections | — | `views/job.php` |
| Email / SMTP | Admin → SMTP | `functions.php` → `smtp_send()` |
| AI prompts | — | `api.php` / `functions.php` (Groq calls) |
| URL structure / routes | — | `index.php` |
| Admin nav (कौन से page दिखें) | — | `admin/inc/header.php` → `$nav` array |
| Upload limit | `.env` → `UPLOAD_MAX_KB` | `config.php` |
| Timezone | `.env` → `APP_TIMEZONE` | `config.php` |
| robots.txt | Admin → SEO → robots editor | `seo.php` → `seo_robots()` |
| Sitemap | Auto | `seo.php` → `seo_sitemap()` |

### Theme colors (assets/style.css)

```css
:root{
  --primary:#2563eb;      /* main blue — links, buttons */
  --accent:#C6FF33;       /* dark-mode accent */
  --green:#16a34a; --amber:#d97706; --red:#dc2626; --violet:#7c3aed;
  --bg:#ffffff; --text:#0f172a; --border:#e2e8f0;
}
[data-theme="dark"]{ --bg:#000000; --text:#e5e7eb; --primary:#C6FF33; }
```

### कस्टम section जोड़ना (example)

```php
<!-- views/home.php में नया section -->
<section class="gl-section">
  <div class="gl-container">
    <div class="section-head"><h2>My New Section</h2></div>
    <!-- content -->
  </div>
</section>
```

CSS classes reusable हैं: `.gl-container`, `.gl-section`, `.section-head`, `.jobs-grid`,
`.cat-grid`, `.chip`, `.gl-btn`, `.gl-panel`, `.detail-card`, `.side-panel`।

---

## 8. File Structure — कौन सी फाइल क्या करती है

```
govlinks/
├── index.php        ← front controller + सारे public routes (यहीं URL map है)
├── config.php       ← .env loader, constants, error handling
├── db.php           ← PDO (SQLite + MySQL), schema, demo seed
├── functions.php    ← सारे helpers (auth, csrf, icons, media, mail, AI…)
├── seo.php          ← meta, OG, schema (JobPosting/Breadcrumb), sitemap, robots, 301
├── api.php          ← AJAX endpoints (suggest, load-more, forms, AI)
├── install.php      ← one-time web installer
├── server.php       ← `php -S` router (local preview)
├── .htaccess        ← Apache: pretty URLs, gzip, cache, security
│
├── assets/          ← style.css, site.js (vanilla), admin.css, admin.js, img/
├── uploads/         ← media library (PHP execution blocked)
├── storage/         ← SQLite DB, sessions, logs
│
├── views/           ← सारे front templates (flat, max depth 2)
│   ├── header.php / footer.php
│   ├── home.php            ← homepage (सारे sections)
│   ├── jobs.php / job.php  ← listing + single
│   ├── exam-list.php / exam-single.php
│   ├── posts.php / post.php / page.php
│   ├── organizations.php / states.php
│   ├── go.php / 404.php
│   └── partials/           ← job-card.php, list-row.php
│
└── admin/           ← सारे admin pages (flat)
    ├── inc/     ← auth.php, header.php, footer.php, editor.php
    ├── index.php   ← dashboard
    ├── jobs.php / exam.php / posts.php / pages.php
    ├── media.php / menus.php / categories.php / tags.php / organizations.php
    ├── comments.php / newsletter.php / alerts.php / ads.php
    ├── seo.php / settings.php / smtp.php / backup.php / ai-writer.php
    └── users.php / profile.php / login.php / logout.php
```

**Rules of the codebase:** कोई framework नहीं, composer नहीं, nested folders नहीं (max depth 2),
कोई external CDN नहीं (icons inline SVG)।

---

## 9. `.env` Configuration

| Key | मतलब | Default / Example |
|---|---|---|
| `APP_ENV` | `local` (errors दिखें) / `production` (छिपें) | `production` |
| `APP_URL` | पूरा site URL (canonical/OG/sitemap) | `https://your-domain.com` |
| `APP_TIMEZONE` | PHP timezone | `Asia/Kolkata` |
| `DB_DRIVER` | `sqlite` या `mysql` | `sqlite` |
| `DB_DATABASE` | SQLite path या MySQL DB name | `storage/govlinks.sqlite` |
| `DB_HOST` / `DB_PORT` / `DB_USERNAME` / `DB_PASSWORD` | सिर्फ MySQL | `127.0.0.1` / `3306` |
| `GROQ_API_KEY` | AI Writer के लिए (optional) | `gsk_...` |
| `UPLOAD_MAX_KB` | max upload size | `5120` |
| `IMAGE_WEBP` | WebP thumbnails on/off | `true` |

> Settings page (Admin → Settings) में Groq key + robots.txt भी edit होते हैं — बार-बार `.env` छूने की ज़रूरत नहीं।

---

## 10. SEO — क्या-क्या पहले से चालू है


### SEO — Auto Generated sitemap.xml

**URL:** `/sitemap.xml`  
![SEO — Auto Generated sitemap.xml](https://i.imgur.com/BNR2bOd.png)

**करने पर क्या होता है (Cause → Effect):**
- `/sitemap.xml` auto-generate — jobs, posts, pages, exam items; कोई manual update नहीं।
- `/robots.txt` Admin → SEO → robots.txt editor से control।
- APP_URL गलत → sitemap में गलत domain → Google indexing खराब।


| Feature | Status | कहाँ से control |
|---|---|---|
| SEO-friendly URLs (`/job/<slug>`) | ✅ | auto (slug field) |
| Meta title + description | ✅ | हर post/job/exam page के SEO box में |
| Open Graph + Twitter cards | ✅ | SEO box + featured image |
| `JobPosting` schema | ✅ | `seo.php` — job fields से auto |
| `BreadcrumbList` schema | ✅ | हर page पर breadcrumbs |
| `sitemap.xml` (+ sub-sitemaps) | ✅ Auto | `/sitemap.xml` |
| `robots.txt` | ✅ Editable | Admin → SEO |
| 301 Redirects | ✅ | Admin → SEO |
| Canonical URLs | ✅ | `APP_URL` + current path |
| Lazy images, gzip, cache headers | ✅ | `.htaccess` / views |

---

## 11. Security — क्या सुरक्षा है

| सुरक्षा | कैसे |
|---|---|
| SQL injection | PDO prepared statements (सारी queries) |
| CSRF | हर POST form में token (`csrf_field()` + `csrf_check()`) |
| XSS | Output escaping `e()` + whitelist HTML sanitizer |
| Password | `password_hash()` / `password_verify()` (bcrypt) |
| Session | httponly cookie, SameSite, login पर `session_regenerate_id()` |
| Brute force | Admin login throttling |
| Roles | Admin = full, Editor = content only (admin-only pages पर 403) |
| Uploads | `finfo` MIME check (extension नहीं), random filename, SVG sanitise, `/uploads` में PHP execution blocked |
| Headers | `X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy` |
| Sensitive files | `.env` / `.htaccess` deny (`.htaccess` rule) |

**Live जाने से पहले:** admin password बदलें, `APP_ENV=production` रखें, `install.php` हटा दें,
`storage/` को publicly accessible न रखें।

---

## 12. Live कराने से पहले Checklist + Troubleshooting

### ✅ Checklist

- [ ] Admin password बदला (default `Admin@123` नहीं)
- [ ] `APP_URL` सही domain
- [ ] Demo content हटाया / असली notifications डालीं
- [ ] Site name, logo, hero text, social links अपने
- [ ] Analytics / Search Console code डाला
- [ ] SMTP set + test email भेजा
- [ ] `/sitemap.xml` Search Console में submit
- [ ] `install.php` delete
- [ ] Backup download करके सुरक्षित रखा

### 🛠️ Troubleshooting

| समस्या | कारण | हल |
|---|---|---|
| Blank page / 500 | `APP_ENV=local` में error दिखता है | `storage/php-error.log` देखें |
| CSS/JS नहीं लोड | `APP_URL` गलत या base path | `.env` में सही `APP_URL` |
| Images upload नहीं हो रहे | `uploads/` writable नहीं | `chmod 775 uploads` |
| DB write error | `storage/` writable नहीं | `chmod 775 storage` |
| Pretty URLs काम नहीं | mod_rewrite off / nginx rule missing | `.htaccess` या nginx `try_files` |
| Admin login नहीं हो रहा | throttle लग गया | 5-10 मिनट रुकें, या DB में password reset |
| AI buttons काम नहीं | Groq key missing/invalid | Admin → Settings → AI (Groq) |
| Mail नहीं जा रहा | SMTP गलत / Gmail normal password | App Password use करें, port 465 SSL |
| Sitemap खाली | कोई published content नहीं | Jobs/Posts publish करें |
| Page 404 | slug बदला | Admin → SEO → 301 Redirect |

### 🔁 Restore / Reset

```bash
# SQLite restore: backup .sqlite file को storage/govlinks.sqlite से replace करें
# MySQL restore: phpMyAdmin → Import → backup.sql
# Fresh start: storage/installed.lock + .env हटाएँ → /install.php
```

---

## 📌 नोट्स

- यह portal **सिर्फ जानकारी** देता है — कभी भी application accept नहीं करता; हर apply button
  `/go/...` से official website पर ले जाता है।
- Demo content की dates install-day के relative हैं — **live से पहले ज़रूर बदलें**।
- Preview sandbox में admin password demo के लिए `Admin@123` सेट किया गया है — अपने असली
  server पर इसे तुरंत बदलें।
- सारे स्क्रीनशॉट hosted links हैं (Imgur) — offline/local copy `shots/annotated/` फोल्डर में भी मौजूद हैं।

---

*Documentation generated for the live preview build — PHP 8.4 · SQLite · Hindi (हिंदी)।*
