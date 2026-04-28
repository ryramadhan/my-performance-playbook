# Web Performance Best Practices
> kebiasaan baik dari awal, berlaku untuk project statis maupun dinamis.

---

## 1. Core Web Vitals — standar Google
metrik utama yang dipakai industri untuk mengukur performa:

| Metrik | Artinya | Target |
|--------|---------|--------|
| **LCP** (Largest Contentful Paint) | konten utama muncul | < 2.5 detik |
| **FID** (First Input Delay) | respon pertama klik | < 100ms |
| **CLS** (Cumulative Layout Shift) | layout tidak loncat | < 0.1 |
| **TTFB** (Time to First Byte) | server mulai response | < 800ms |

> cek skor ini pakai **Lighthouse** di Chrome DevTools atau pagespeed.web.dev sebelum deploy.

---

## 2. Frontend

### Gambar
- selalu pakai format **WebP**
- `loading="lazy"` untuk gambar di bawah fold
- `loading="eager"` untuk gambar yang langsung kelihatan (hero)
- ukuran gambar sesuai tampilan, jangan upload 4K untuk thumbnail
- selalu isi atribut `width` dan `height` — cegah **CLS** (layout loncat)

### JavaScript
- jangan load JS yang tidak dipakai
- pakai **code splitting** — JS dimuat per halaman, bukan sekaligus
- untuk interaktivitas simpel, **vanilla JS** lebih baik dari library besar
- hindari library besar kalau fungsinya bisa diganti beberapa baris JS
- defer script yang tidak kritis:
```html
<script src="analytics.js" defer></script>
```

### CSS
- pakai **Tailwind** — otomatis buang CSS yang tidak dipakai saat build
- hindari **render-blocking CSS** — load critical CSS inline, sisanya async

### Font
- pakai font **lokal** (`@fontsource`) bukan Google Fonts CDN
- tambahkan `font-display: swap` agar teks tetap muncul saat font belum dimuat
- preload font yang dipakai di atas fold:
```html
<link rel="preload" href="/fonts/inter.woff2" as="font" crossorigin />
```

### Routing & Transisi
- pakai **client-side routing** agar pindah halaman tanpa reload (Next.js built-in)
- aktifkan **prefetch** — halaman berikutnya diload sebelum diklik
- di Astro pakai **View Transitions** untuk efek transisi mulus

### UX — Loading & Error State
sering diabaikan developer junior, wajib ada di industri:
```
✅ skeleton loading    → bukan spinner doang
✅ error boundary      → kalau fetch gagal, tampilkan pesan yang proper
✅ empty state         → kalau data kosong, tampilkan sesuatu
✅ optimistic update   → UI update duluan, baru sync ke server
```

---

## 3. Backend

### Database
- selalu pakai **indexing** pada kolom yang sering di-query
- hindari **query N+1** — pakai JOIN atau eager loading
- pakai **pagination** — jangan kirim semua data sekaligus
- pilih kolom yang dibutuhkan saja, hindari `SELECT *`
- gunakan **connection pooling** — jangan buka koneksi baru setiap request

### Caching — ini yang paling ngaruh
```
Browser cache   → gambar, font, CSS (otomatis via CDN)
CDN cache       → Vercel / Cloudflare (otomatis)
Server cache    → Redis untuk data yang sering diakses
Database cache  → simpan hasil query berat
```
> rule of thumb: kalau data yang sama diminta lebih dari sekali
> dan tidak sering berubah — cache.

### API
- response time idealnya **< 200ms**
- aktifkan **compression** (gzip / brotli) untuk response besar
- pakai **pagination** untuk list data
- return hanya data yang dibutuhkan, jangan kirim semua field
- pakai **HTTP/2** — request paralel lebih efisien
- versioning API dari awal: `/api/v1/...`

---

## 4. SEO Technical
```
✅ meta title & description unik per halaman
✅ Open Graph — tampilan link saat dishare WhatsApp / Twitter
✅ sitemap.xml — bantu Google index semua halaman
✅ robots.txt — kontrol halaman mana yang boleh diindex
✅ canonical URL — hindari duplicate content
✅ structured data (JSON-LD) — rich result di Google
```

---

## 5. Security — yang ngaruh ke performa & profesionalisme
```
✅ environment variables   → jangan hardcode API key di kode
✅ rate limiting di API    → cegah spam request
✅ input validation        → frontend dan backend, keduanya wajib
✅ CORS yang proper        → jangan pakai wildcard * di production
✅ HTTPS                   → wajib, ngaruh ke SEO juga
✅ dependency audit        → npm audit secara berkala
```

---

## 6. Monitoring — wajib di production
sering dilupakan, padahal ini yang bikin developer terlihat profesional:

```
Sentry           → track error yang terjadi di production
Vercel Analytics → monitor performa real user
Uptime Robot     → notifikasi kalau website down
Lighthouse CI    → cek performa otomatis setiap deploy
```

> prinsipnya: kalau tidak dimonitor, kamu tidak akan tau ada masalah
> sampai user komplain.

---

## 7. Deploy & Infrastruktur

```
Frontend (Next.js / Astro)  → Vercel atau Netlify
Backend (Node.js / Express) → Railway atau Render
CDN & proteksi              → Cloudflare
Database                    → Supabase / Railway / PlanetScale
Cache server                → Upstash (Redis serverless)
```

> deploy di CDN = otomatis lebih cepat karena server lebih dekat ke user.

---

## 8. Urutan Prioritas

kalau mau web terasa instant, benerin urutan ini dulu:

```
1. API & database cepat     → query efisien + caching
2. Gambar dioptimasi        → paling sering diabaikan
3. JS seminimal mungkin     → jangan load yang tidak perlu
4. Deploy di CDN            → Vercel / Netlify / Cloudflare
5. Prefetch & code split    → halaman terasa instant
6. Monitor di production    → tau masalah sebelum user komplain
```

---

## 9. Checklist Sebelum Deploy

- [ ] Lighthouse score > 90 di semua kategori
- [ ] semua gambar pakai WebP + ukuran sesuai
- [ ] tidak ada `console.log` tertinggal di production
- [ ] environment variables sudah di-set di hosting
- [ ] error handling sudah ada di semua fetch / API call
- [ ] loading state sudah ada di semua data yang di-fetch
- [ ] meta title & description sudah diisi per halaman
- [ ] test di mobile — bukan cuma desktop
- [ ] cek network tab — tidak ada request yang tidak perlu
- [ ] database sudah pakai indexing di kolom yang sering di-query

---

> **intinya:** performa web bukan soal framework yang digunakan,
> tetapi bagaimana kita menulis kode yang efisien sejak awal.
> fokusnya bukan menghilangkan loading sepenuhnya,
> melainkan membuatnya cepat, konsisten, dan nyaman bagi pengguna di berbagai kondisi.