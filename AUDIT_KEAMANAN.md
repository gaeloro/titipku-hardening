# Audit Keamanan TitipKu — Checklist L135 (diterapkan pada L149)

Meninjau `backend/app.py` TitipKu (versi 1.1.0, sesudah hardening
L149) dengan 24 butir checklist L135. Status: LULUS / TIDAK-ADA
(fitur tidak ada). **Temuan kritis: 0.**

## Input
| ID | Pemeriksaan | Status | Bukti |
|---|---|---|---|
| IN-01 | Input divalidasi tipe & rentang | LULUS | Skema pydantic; ukuran masuk whitelist kecil/sedang/besar (422 di luar itu, teruji) |
| IN-02 | Batas panjang string | LULUS | `max_length` email 200, sandi 100, nama_barang 200 (nama 201 char -> 422, teruji) |
| IN-03 | Query berparameter | LULUS | SQLAlchemy ORM di semua akses data |
| IN-04 | Keluaran di-escape (anti XSS) | LULUS | Frontend menulis nama barang via `textContent`; sisanya JSON; CSP `default-src 'self'` |
| IN-05 | Validasi unggahan berkas | TIDAK-ADA | Tidak ada fitur unggahan |
| IN-06 | Batas ukuran badan | LULUS | Middleware 1 MB -> 413 (teruji L149) |

## Autentikasi & Otorisasi
| ID | Pemeriksaan | Status | Bukti |
|---|---|---|---|
| AU-01 | Endpoint tulis wajib autentikasi | LULUS | Semua POST titipan memakai `Depends(pengguna_saat_ini)`; tanpa token 401 (teruji) |
| AU-02 | Otorisasi peran per endpoint | LULUS | Ringkasan hanya admin -> 403 untuk pelanggan (teruji) |
| AU-03 | Peran admin tidak dipilih sendiri | LULUS | Aturan admin-pertama; admin kedua -> 403 (teruji) |
| AU-04 | Hash sandi kuat | LULUS | bcrypt + salt per pengguna |
| AU-05 | Rate limit login | LULUS | 5 gagal/60 dtk per email -> percobaan ke-6 ditolak 429 walau sandi benar (teruji L149) |
| AU-06 | Pemeriksaan kepemilikan (anti-IDOR) | LULUS | Check-out memeriksa pemilik; milik orang lain 404 (teruji) |
| AU-07 | Token kedaluwarsa & palsu ditolak | LULUS | Klaim `exp` 8 jam (ditambah L149); token palsu 401 (teruji) |

## Data
| ID | Pemeriksaan | Status | Bukti |
|---|---|---|---|
| DA-01 | Data pribadi tidak terekspos | LULUS | Tidak ada endpoint daftar pengguna; titipan hanya milik sendiri |
| DA-02 | Error tidak membocorkan internal | LULUS | Pesan `detail` seragam; tidak ada stack/SQL di respons |
| DA-03 | Integritas transaksi | LULUS | Satu commit per mutasi; check-out atomik dalam satu sesi |
| DA-04 | Log tidak mencatat rahasia | LULUS | Jejak audit hanya metode/jalur/status/waktu |

## Konfigurasi
| ID | Pemeriksaan | Status | Bukti |
|---|---|---|---|
| KO-01 | Rahasia dari env | LULUS | `JWT_RAHASIA` wajib dari env; RuntimeError jelas bila kosong |
| KO-02 | CORS dibatasi | LULUS | Tanpa middleware CORS sama sekali — hanya same-origin menurut desain |
| KO-03 | Header keamanan | LULUS | nosniff, X-Frame-Options DENY, CSP (ditambah L149, teruji) |
| KO-04 | Jejak audit mutasi | LULUS | Middleware menulis log JSON-lines tiap POST/PUT/DELETE (ditambah L149) |

## Dependensi
| ID | Pemeriksaan | Status | Bukti |
|---|---|---|---|
| DE-01 | Audit dependensi bersih | LULUS | `pip-audit -r requirements.txt`: "No known vulnerabilities found" (sesudah pytest dinaikkan 8.3.4 -> 9.0.3 karena PYSEC-2026-1845) |
| DE-02 | Versi dibekukan | LULUS | Semua pin persis di requirements.txt |
| DE-03 | Hanya dependensi terpakai | LULUS | 6 runtime terpakai langsung; httpx+pytest khusus test |

**Kesimpulan: 22 LULUS, 1 TIDAK-ADA (fitur unggahan memang tidak
ada), 0 GAGAL, 0 temuan kritis.** Perbaikan yang dilakukan di L149
dari keadaan MVP L148: AU-05, AU-07 (klaim exp), IN-06, KO-03, KO-04,
dan DE-01.
