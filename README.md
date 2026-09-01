# Kasatria Preliminary Assignment — Panduan Penyelesaian

File `index.html` sudah berisi kode lengkap (modifikasi dari demo three.js
`css3d_periodictable`) yang memenuhi poin 3–9 di instruksi. Yang tersisa untukmu:
isi kredensial Google, sambungkan ke Sheet, lalu deploy.

## 1. Buat Google Sheet dari CSV (poin instruksi #1)
1. Buka [sheets.google.com](https://sheets.google.com) → **Blank spreadsheet**.
2. `File > Import > Upload` → pilih `Data_Template.csv` → *Import location*: **Replace current sheet**.
3. Pastikan urutan kolom tetap: `Name, Photo, Age, Country, Interest, Net Worth` (baris 1 = header, baris 2–201 = data, 200 baris pas untuk grid 20×10 nanti).
4. **Share** → masukkan `lisa@kasatria.com` (akses *Viewer* atau *Editor*, sesuai yang diminta HR).
5. **Penting untuk teknis:** agar halaman web bisa membaca sheet lewat Sheets API dengan API key (bukan OAuth), set juga *General access* ke **"Anyone with the link" → Viewer**. Kalau perusahaan tidak mengizinkan sheet publik, alternatifnya pakai OAuth access token hasil sign-in (lihat catatan di bagian 4).
6. Ambil **Sheet ID** dari URL: `docs.google.com/spreadsheets/d/`**`INI_SHEET_ID`**`/edit`.

## 2. Buat Google Cloud Project (poin instruksi #2)
1. Ke [console.cloud.google.com](https://console.cloud.google.com) → buat project baru, misal `kasatria-assignment`.
2. **APIs & Services > Library** → cari **Google Sheets API** → **Enable**.
3. **APIs & Services > Credentials**:
   - **Create Credentials > API key** → salin, ini untuk `SHEETS_API_KEY`. Klik **Restrict key** → batasi ke *Google Sheets API* dan (setelah deploy) *HTTP referrers* ke domain hosting-mu.
   - **Create Credentials > OAuth client ID** → *Application type*: **Web application**. Di **Authorized JavaScript origins**, tambahkan URL tempat halaman ini akan di-host (contoh `https://username.github.io`, dan `http://localhost:8000` untuk testing lokal). Salin **Client ID**, ini untuk `GOOGLE_CLIENT_ID`.
4. **APIs & Services > OAuth consent screen** → isi nama app, support email, dan tambahkan email test kamu sendiri sebagai *test user* (karena app belum diverifikasi Google).

## 3. Isi konfigurasi di `index.html`
Cari blok `window.APP_CONFIG` di bagian atas file dan isi tiga nilai ini:
```js
window.APP_CONFIG = {
  GOOGLE_CLIENT_ID: 'xxxx.apps.googleusercontent.com',
  SHEETS_API_KEY: 'AIzaSy...',
  SHEET_ID: 'sheet-id-dari-url',
  SHEET_RANGE: 'Sheet1!A2:F201'  // sesuaikan nama tab jika bukan "Sheet1"
};
```

## 4. Cara kerja teknis (untuk persiapan wawancara)
- **Login (poin #2):** memakai *Google Identity Services* (`accounts.google.com/gsi/client`) — tombol "Sign in with Google" standar, gerbang akses ke halaman utama. Token ID di-decode di client hanya untuk menampilkan nama/foto; di aplikasi produksi sebaiknya diverifikasi di backend.
- **Ambil data (poin #1 & #3):** `fetch()` ke Sheets API v4 (`spreadsheets.values.get`) pakai API key, dipetakan ke array objek `{name, photo, age, country, interest, netWorth}`.
- **Tile 3D (poin #4):** setiap elemen periodic table diganti kartu berisi foto, nama, umur/negara/hobi, dan net worth.
- **Warna net worth (poin #5):** merah `< $100K`, oranye `> $100K`, hijau `> $200K` — logika di fungsi `netWorthColor()`.
- **Empat layout (poin #6–9):**
  - `TABLE` — grid tetap **20 kolom × 10 baris** (pas 200 data).
  - `SPHERE` — algoritma bawaan demo (generik untuk jumlah objek berapa pun).
  - `HELIX` — **double helix**: setiap pasangan index genap/ganjil ditaruh di sisi berlawanan (offset 180°) pada ketinggian yang sama, membentuk dua untai.
  - `GRID` — **5 × 4 × 10** (5 kolom, 4 baris, 10 lapis kedalaman).

## 5. Testing lokal
Karena pakai ES modules & fetch, harus dijalankan lewat server lokal, bukan dibuka langsung sebagai file:
```bash
cd folder-project
python3 -m http.server 8000
```
Buka `http://localhost:8000`. Pastikan `http://localhost:8000` sudah ada di *Authorized JavaScript origins* OAuth client.

## 6. Deploy (poin instruksi #10)
Opsi tercepat & gratis:
- **GitHub Pages:** push `index.html` ke repo, aktifkan Pages dari branch `main` (folder root). URL: `https://username.github.io/nama-repo/`.
- **Netlify / Vercel:** drag-and-drop folder ini ke dashboard mereka, langsung dapat URL publik.

Setelah dapat URL, **tambahkan URL tersebut** ke *Authorized JavaScript origins* di OAuth client (langkah 2), baru kirim link ke `lisa@kasatria.com` sesuai poin #10.

## 4b. Opsi lebih aman: Service Account (Sheet tetap privat)
Cara di bagian 4 (API key + sheet publik) paling cepat untuk demo, tapi sheet-nya
harus "Anyone with link". Kalau kamu mau sheet tetap privat, pakai service account:

1. **Buat Service Account** — Cloud Console > IAM & Admin > Service Accounts > Create
   Service Account. Beri nama, lewati bagian role project (tidak perlu), klik Done.
2. **Generate JSON key** — buka service account tsb > tab Keys > Add Key > Create new
   key > JSON. File ini berisi `client_email` dan `private_key` — **jangan pernah**
   commit ke Git atau taruh di `index.html`.
3. **Share Sheet ke service account** — salin `client_email` dari file JSON, lalu
   Share sheet ke email itu dengan akses Viewer. Set kembali *General access* sheet
   ke privat (tidak perlu "Anyone with link" lagi).
4. **Deploy backend** — file `api/sheet-data.js` di folder ini adalah Vercel
   Serverless Function yang memakai service account untuk baca sheet, lalu
   mengembalikan JSON ke webpage. Push folder ini ke GitHub lalu import ke
   [vercel.com](https://vercel.com) (auto-detect folder `api/` sebagai serverless
   function).
5. **Set environment variables** di dashboard Vercel (Project Settings > Environment
   Variables), diambil dari file JSON key:
   - `GOOGLE_SERVICE_ACCOUNT_EMAIL` = nilai `client_email`
   - `GOOGLE_PRIVATE_KEY` = nilai `private_key` (biarkan `\n` apa adanya, kode sudah
     menangani konversinya)
   - `SHEET_ID` = ID sheet
   - `SHEET_RANGE` = misal `Sheet1!A2:F201`
6. **Install dependency** — sebelum deploy, di folder project jalankan
   `npm init -y && npm install googleapis` supaya ada `package.json` yang Vercel
   pakai untuk resolve `import { google } from 'googleapis'`.
7. **Aktifkan mode backend** — di `index.html`, ubah `DATA_SOURCE: 'api-key'` menjadi
   `DATA_SOURCE: 'backend'`. Webpage sekarang memanggil `/api/sheet-data` alih-alih
   Sheets API langsung, dan private key tidak pernah terkirim ke browser.

## Troubleshooting cepat
- **"Sign-in failed" / tombol tidak muncul** → Client ID salah atau origin belum ditambahkan di OAuth settings.
- **"Could not load data from Google Sheet"** → cek: Sheets API sudah *Enable*? API key belum dibatasi terlalu ketat? Sheet sudah *Anyone with link: Viewer*? `SHEET_RANGE` cocok dengan nama tab?
- **Foto tidak muncul** → cek URL kolom `Photo` di CSV bisa diakses publik (bukan link Google Drive privat).
