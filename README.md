# GERLINGSIH STUPA - Bank Sampah

Sistem pengelolaan tabungan bank sampah digital untuk **SD Negeri 7 Pedungan**.

Website ini memungkinkan:
- Nasabah (siswa/umum) mengecek riwayat tabungan dan mencetak buku tabungan
- Guru/Admin menginput setoran sampah
- Administrator melihat rekapitulasi, margin, peringkat, mutasi, dan mengelola data nasabah

---

## 🛠️ Tech Stack

| Komponen       | Teknologi                          |
|----------------|------------------------------------|
| Frontend       | HTML, Tailwind CSS, SweetAlert2    |
| Backend / DB   | Supabase (PostgreSQL)              |
| Hosting        | Vercel + GitHub                    |
| Auth sederhana | PIN (tersimpan di tabel `admin_config`) |

---

## 📁 Struktur File

```
├── index.html      → Halaman utama (TabunganKu + Dashboard Guru)
├── admin.html      → Panel Administrator
└── README.md       → Dokumentasi ini
```

---

## 🚀 Cara Deploy (GitHub + Vercel)

### 1. Siapkan Supabase
1. Buat project di [supabase.com](https://supabase.com)
2. Buka **SQL Editor** → jalankan seluruh script schema (lihat bagian [Database Schema](#-database-schema))
3. Import data dari Google Spreadsheet (export CSV → import ke masing-masing tabel)

### 2. Upload ke GitHub
1. Buat repository baru
2. Upload file:
   - `index.html`
   - `admin.html`
   - `README.md`
3. Commit & push

### 3. Deploy ke Vercel
1. Buka [vercel.com](https://vercel.com) → **Add New Project**
2. Import repository GitHub Anda
3. Klik **Deploy** (tidak perlu setting tambahan)
4. Website langsung hidup

---

## 🔐 PIN Akses

| Fitur                | PIN          | Keterangan                     |
|----------------------|--------------|--------------------------------|
| Dashboard Guru       | `7pedungan`  | Input setoran sampah           |
| Panel Administrator  | `stupa`      | Rekap, mutasi, hapus nasabah   |

PIN disimpan di tabel `admin_config`. Untuk mengubah PIN, jalankan query berikut di SQL Editor:

```sql
UPDATE admin_config SET value = 'PIN_BARU' WHERE key = 'login_pin';   -- PIN Guru
UPDATE admin_config SET value = 'PIN_BARU' WHERE key = 'admin_pin';   -- PIN Admin
```

---

## 🗄️ Database Schema

### Tabel Utama

| Tabel            | Fungsi                                      |
|------------------|---------------------------------------------|
| `nasabah`        | Data nasabah (id, nama, kelas, saldo, dll)  |
| `transaksi`      | Riwayat setoran sampah                      |
| `master_sampah`  | Daftar jenis sampah + harga                 |
| `mutasi`         | Riwayat penarikan saldo                     |
| `admin_config`   | Penyimpanan PIN                             |

### View (untuk laporan)

| View                  | Fungsi                              |
|-----------------------|-------------------------------------|
| `v_rekap_kelas`       | Rekap tabungan per kelas            |
| `v_rekap_margin`      | Rekap margin per tahun              |
| `v_peringkat_murid`   | Peringkat nasabah murid             |
| `v_peringkat_umum`    | Peringkat nasabah umum / guru       |

### Trigger
Setiap kali ada **insert** ke tabel `transaksi`, otomatis:
- `total_berat` nasabah bertambah
- `total_saldo` nasabah bertambah

---

## 📦 Cara Import Data dari Google Spreadsheet

1. Buka Google Spreadsheet lama
2. Export tiap sheet menjadi **CSV**:
   - Nasabah → `nasabah.csv`
   - Transaksi → `transaksi.csv`
   - Master_Sampah → `master_sampah.csv`
   - Mutasi → `mutasi.csv`
3. Di Supabase → **Table Editor** → pilih tabel → **Import data from CSV**
4. Sesuaikan mapping kolom jika nama berbeda

**Contoh mapping kolom Nasabah:**

| Spreadsheet | Supabase       |
|-------------|----------------|
| ID          | id             |
| Nama        | nama           |
| Kelas       | kelas          |
| Berat       | total_berat    |
| Saldo       | total_saldo    |
| Rank        | rank           |

---

## 🔧 Konfigurasi Supabase (sudah terisi)

File HTML sudah berisi konfigurasi berikut:

```js
const SUPABASE_URL = "https://fltppndfmfuckssbvzpk.supabase.co";
const SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...";
```

Jika Anda membuat project Supabase baru, ganti kedua nilai tersebut di `index.html` dan `admin.html`.

---

## ✨ Fitur Utama

### TabunganKu (Nasabah)
- Cari nama → lihat saldo, total berat, peringkat
- Riwayat setoran lengkap
- Cetak Buku Tabungan (print-ready)

### Dashboard Guru
- Pilih kelas → pilih nasabah
- Pilih jenis sampah (harga otomatis terisi)
- Input berat → simpan transaksi

### Panel Administrator
1. **Rekapitulasi** – filter bulan & tahun, bisa sembunyikan kolom uang
2. **Rekap Kelas** – ranking kelas berdasarkan saldo
3. **Rekap Margin / Tahun** – total berat, tabungan, penjualan, profit
4. **Peringkat** – Top nasabah murid & umum/guru
5. **Kwitansi Mutasi** – penarikan saldo
6. **Hapus Nasabah** – soft delete (data tidak hilang permanen)

---

## 🛡️ Keamanan

- Row Level Security (RLS) sudah diaktifkan
- Saat ini menggunakan policy terbuka (`Allow all for anon`) karena proyek sekolah
- Disarankan nanti diperketat (hanya role tertentu yang boleh write)

---

## 📞 Kontak & Kredit

**GERLINGSIH STUPA**  
SD Negeri 7 Pedungan  
*"Mewujudkan Lingkungan Bersih, Menabung Jadi Prestasi"*

---

## 📝 Catatan Pengembangan

- Migrasi dari Google Spreadsheet + Google Apps Script ke Supabase
- Tampilan frontend **tidak diubah**, hanya layer database yang diganti
- Soft delete digunakan agar data historis tetap aman
- Trigger database menggantikan logika update manual yang sebelumnya ada di Apps Script

---

Dibuat dengan ❤️ untuk SD Negeri 7 Pedungan  
© 2026 GERLINGSIH STUPA
