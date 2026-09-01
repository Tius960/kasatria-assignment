# 🪐 Kasatria People Table

Sebuah "tabel periodik", tapi isinya bukan unsur kimia — melainkan **200 orang**,
melayang di ruang 3D, bisa disusun ulang jadi empat formasi berbeda hanya dengan
satu klik.

Dibangun di atas [three.js](https://threejs.org) `CSS3DRenderer`, terinspirasi
dari demo klasik *periodic table* milik three.js, tapi setiap "unsur" di sini
diganti kartu profil orang sungguhan — lengkap dengan foto, umur, negara, minat,
dan kekayaan bersih — yang datanya ditarik langsung dari **Google Sheet** secara
real-time.

---

## ✨ Apa yang bisa dilakukan halaman ini

### 🔐 Gerbang masuk dengan akun Google
Sebelum melihat apa pun, pengunjung harus **Sign in with Google**. Tidak ada
form pendaftaran — cukup satu tombol, memakai Google Identity Services resmi.
Setelah masuk, nama dan foto profil pengguna muncul sebagai badge kecil di
pojok kanan atas, lengkap tombol *Sign out*.

### 🃏 200 kartu manusia, bukan unsur kimia
Setiap kartu menampilkan:
- Foto orang
- Nama
- Umur & negara
- Satu hal yang mereka minati
- Kekayaan bersih (net worth), diformat sebagai mata uang

Semua data ini ditarik langsung dari Google Sheet lewat Sheets API — jadi kalau
sheet-nya diubah, tinggal refresh halaman dan datanya ikut berubah.

### 🎨 Warna yang bercerita
Setiap kartu punya warna glow sesuai kekayaan bersihnya:

| Warna | Arti |
|---|---|
| 🔴 Merah | Net worth di bawah $100K |
| 🟠 Oranye | Net worth di atas $100K |
| 🟢 Hijau | Net worth di atas $200K |

Sekali lihat, langsung kelihatan sebarannya tanpa perlu baca satu-satu.

### 🌀 Empat formasi 3D yang bisa dipindah kapan saja
Empat tombol di bagian bawah layar — **TABLE, SPHERE, HELIX, GRID** — membuat
seluruh 200 kartu bertransformasi secara *animated* dari satu susunan ke
susunan lain, dengan gerakan smooth ala tween (exponential ease in-out) yang
membuatnya terasa hidup, bukan sekadar loncat posisi.

- **TABLE** — grid rapi 20 kolom × 10 baris, seperti tabel periodik aslinya.
- **SPHERE** — seluruh orang tersebar merata membentuk bola raksasa yang
  menghadap ke luar, dihitung pakai distribusi koordinat spherical.
- **HELIX** — bukan satu, tapi **dua** untai heliks berlawanan 180°, mirip
  struktur DNA — setiap orang bergiliran masuk ke untai A atau untai B.
- **GRID** — susunan tiga dimensi 5 × 4 × 10, orang-orang tersusun berlapis ke
  arah kedalaman layar, seperti kubus data.

### 🖱️ Navigasi bebas ala kamera film
Klik-tarik untuk memutar kamera mengelilingi seluruh formasi, scroll untuk
zoom in/out — semua lewat `TrackballControls`, jadi pengalamannya terasa
seperti melayang di ruang angkasa dan mengorbit sekumpulan orang.

---

## 🧠 Di balik layar

| Bagian | Teknologi |
|---|---|
| Render 3D | `three.js` + `CSS3DRenderer` (kartu HTML asli, dirender dalam ruang 3D — bukan gambar) |
| Animasi transisi | `TWEEN.js` |
| Kontrol kamera | `TrackballControls` |
| Login | Google Identity Services (Sign in with Google) |
| Sumber data | Google Sheets API v4, atau serverless function dengan service account (opsi sheet tetap privat) |
| Rendering kartu | Elemen DOM biasa (`div`) yang "ditempel" ke ruang 3D lewat `CSS3DObject` — bukan tekstur atau canvas |

Pendekatan `CSS3DRenderer` ini yang membuat setiap kartu tetap bisa
menampilkan foto asli, teks tajam, dan hover effect seperti elemen web biasa,
padahal posisinya dihitung penuh dalam ruang tiga dimensi.

---

## 📁 Isi folder ini

- `index.html` — seluruh aplikasi (UI, login, fetch data, render 3D) dalam satu file.
- `api/sheet-data.js` *(opsional)* — serverless function untuk mode sheet privat via service account.

> Untuk panduan langkah-demi-langkah cara menghubungkan Google Sheet, membuat
> kredensial, dan men-deploy halaman ini, lihat `SETUP.md`.
