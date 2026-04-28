# Web Performance Best Practices
> kebiasaan baik dari awal, berlaku untuk project statis maupun dinamis.
> standar industri — frontend, backend, fullstack.

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
- pakai **client-side routing** agar pindah halaman tanpa reload
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

## 5. Security
```
✅ environment variables   → jangan hardcode API key di kode
✅ rate limiting di API    → cegah spam request
✅ input validation        → frontend dan backend, keduanya wajib
✅ CORS yang proper        → jangan pakai wildcard * di production
✅ HTTPS                   → wajib, ngaruh ke SEO juga
✅ dependency audit        → npm audit secara berkala
```

---

## 6. Accessibility (a11y)
sering diabaikan, padahal ngaruh ke SEO dan profesionalisme:
```
✅ alt text di semua gambar
✅ semantic HTML — pakai <nav>, <main>, <article>, <section>
✅ keyboard navigable — semua fungsi bisa dipakai tanpa mouse
✅ contrast ratio cukup — teks harus terbaca jelas
✅ focus indicator — elemen yang difokus harus kelihatan
```
> test pakai: Lighthouse accessibility score atau axe DevTools extension.

---

## 7. Mobile First
```
✅ design dari mobile dulu, baru desktop
✅ touch target minimal 44x44px — tombol tidak boleh terlalu kecil
✅ test di berbagai ukuran layar sebelum deploy
✅ tidak ada horizontal scroll di mobile
✅ font size minimal 16px untuk body text
```
> di Tailwind: mulai dari class default (mobile), baru tambah md: lg: untuk desktop.

---

## 8. Testing
wajib di industri, sering dilupakan developer junior:

```
unit test        → test fungsi / komponen satu per satu (Jest, Vitest)
integration test → test alur dari frontend ke backend
e2e test         → simulasi user buka browser beneran (Playwright, Cypress)
```

> rule of thumb: tidak perlu 100% coverage —
> fokus test di bagian yang paling kritis dan paling sering dipakai.

---

## 9. CI/CD — otomasi deploy
```
setiap push ke GitHub:
  → otomatis jalankan test
  → kalau test pass → otomatis deploy
  → kalau test fail → deploy dibatalkan

tools:
  GitHub Actions  → untuk custom pipeline
  Vercel          → otomatis deploy setiap push (tanpa setup)
```
> dengan CI/CD, tidak ada lagi "works on my machine" — semua terverifikasi otomatis.

---

## 10. Monitoring — wajib di production
```
Sentry           → track error yang terjadi di production
Vercel Analytics → monitor performa real user
Uptime Robot     → notifikasi kalau website down
Lighthouse CI    → cek performa otomatis setiap deploy
```
> kalau tidak dimonitor, kamu tidak akan tau ada masalah sampai user komplain.

---

## 11. Deploy & Infrastruktur
```
Frontend (Next.js / Astro)  → Vercel atau Netlify
Backend (Node.js / Express) → Railway atau Render
CDN & proteksi              → Cloudflare
Database                    → Supabase / Railway / PlanetScale
Cache server                → Upstash (Redis serverless)
```
> deploy di CDN = otomatis lebih cepat karena server lebih dekat ke user.

---

## 12. Urutan Prioritas

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

## 13. Checklist Sebelum Deploy

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
- [ ] accessibility score > 90 di Lighthouse
- [ ] semua gambar punya alt text
- [ ] tidak ada horizontal scroll di mobile
- [ ] test basic e2e — alur utama user bisa jalan

---

## 14. Fondasi Kebiasaan — berlaku di semua project

✅ gambar → selalu WebP + lazy load
✅ HTML → selalu semantic (nav, main, article)
✅ API → selalu pagination, jangan kirim semua data
✅ database → selalu index kolom yang di-query
✅ variabel sensitif → selalu di .env, tidak pernah hardcode
✅ error handling → selalu ada, tidak pernah dibiarkan kosong
✅ loading state → selalu ada kalau ada fetch data
✅ mobile → selalu test di mobile sebelum selesai
✅ console.log → selalu dihapus sebelum push ke repo
✅ Lighthouse → selalu cek score sebelum deploy

> **intinya:** web cepat bukan soal framework — tapi kebiasaan nulis kode yang benar dari awal. saya masih terus belajar dari dua sisi, frontend dan backend, karena semakin dalam dipelajari semakin banyak yang ingin saya tau. dan itu yang membuat saya terus penasaran.