
     
     

# TitipKu Hardening — Dari MVP ke Kualitas Pamer

Satu kalimat: Folder ini memuat proses dan bukti pembawaan TitipKu
(L148) dari MVP yang jalan menjadi aplikasi yang berani dipamerkan —
hardening keamanan di kode, audit checklist penuh, cakupan test 98%,
dan deploy sebagai service.

## Cara menjalankan / memeriksa

```sh
# menjalankan ulang seluruh bukti L149
./verifikasi.sh
```

Skrip itu menjalankan ulang suite + cakupan (syarat ≥ 80%),
memeriksa pernyataan 0 temuan kritis pada audit, kelengkapan
`SELESAI.md`, skor dari laporan Lighthouse mentah, dan HTTP 200 dari
URL deploy.

## Isi & fitur pembuktian

- **Hardening di kode sumber** (backend TitipKu v1.1.0): rate limit
  login per email (5 gagal/60 detik → 429), klaim `exp` JWT 8 jam,
  middleware batas badan 1 MB (413) + header keamanan (nosniff,
  X-Frame-Options DENY, CSP), dan jejak audit mutasi JSON-lines.
- **Audit keamanan**: checklist 24 butir diterapkan item demi item
  di `AUDIT_KEAMANAN.md` — **0 temuan kritis**; `pip-audit` bersih
  setelah pytest dinaikkan menutup PYSEC-2026-1845.
- **Cakupan test**: suite diperluas (jalur galat, rate limit, admin
  kedua, pendapatan) → 5 test lulus, cakupan `backend/app.py` **98%**.
- **Lighthouse** atas halaman ter-deploy: performa **100**,
  aksesibilitas 97, best-practices 100, SEO 90
  (`laporan-lighthouse.json`).
- **Deploy**: service systemd `titipku` (`titipku.service` +
  `titipku.env` berizin 600), diperiksa merespons HTTP 200.
  Catatan kejujuran dari `SELESAI.md`: di sandbox ini "publik"
  berarti alamat lokal mesin; jalur internet sungguhan mengikuti
  pola VPS + domain.

## Demo / kode

- Demo: hasil audit dan bukti hardening ada di repo ini (AUDIT_KEAMANAN.md, SELESAI.md)
- Kode aplikasi: lihat repo TitipKu (L148); laporan lengkap di
  `SELESAI.md` folder ini.
- Kode: repo ini https://github.com/gaeloro/titipku-hardening — aplikasi yang di-hardening: https://github.com/gaeloro/titipku
