# SELESAI — TitipKu: Laporan Penyelesaian (L149)

Proyek orisinal L148 (TitipKu) dibawa ke kualitas "berani dipamerkan".

## Suite test & cakupan
- `pytest tests/ -q`: **5 passed** (skenario utama, pagar &
  kepemilikan, jalur galat & hardening, rate limit login, admin kedua
  & pendapatan).
- Cakupan logika inti (`coverage report --include="*/backend/app.py"`):
  **98%** (147 pernyataan, 3 terlewat) — melampaui syarat ≥ 80%.
- E2E browser L148 tetap menjadi bukti alur UI ujung-ke-ujung.

## Audit keamanan
Checklist 24 butir L135 diterapkan penuh di `AUDIT_KEAMANAN.md`:
**0 temuan kritis** (22 LULUS, 1 TIDAK-ADA untuk fitur unggahan yang
memang tidak ada). Hardening yang ditambahkan di L149: rate limit
login (429), klaim `exp` JWT 8 jam, batas badan 1 MB (413), header
keamanan (nosniff/X-Frame-Options/CSP), jejak audit mutasi, dan
penaikan pytest menutup PYSEC-2026-1845 — `pip-audit` akhir bersih.

## Lighthouse (halaman ter-deploy, Chrome headless)
- Performance: **100**
- Accessibility: **97**
- Best Practices: **100**
- SEO: **90**
(Laporan mentah: `laporan-lighthouse.json` di folder L149.)

## Deploy
- Alamat: **http://127.0.0.1:18149/** — service systemd `titipku`
  (unit di folder ini; aplikasi berjalan dari repo workspace agar
  tahan reset sistem berkas root VM), diperiksa merespons
  **HTTP 200** oleh `verifikasi.sh`.
- Catatan kejujuran: "publik" di dalam sandbox ini berarti alamat
  lokal mesin; penerbitan ke internet sungguhan mengikuti jalur
  L140/L141 (VPS + domain) yang tidak tersedia di sini.
