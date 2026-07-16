# Catetin v2

Pencatat pengeluaran harian, PWA, offline-first, semua data tersimpan lokal di `localStorage` HP kamu (gak ada server, gak ada tracking).

## Yang baru di v2
- **Kategori lebih lengkap**: Makan, Transport, Ngopi/Jajan, **Rokok**, Belanja, Tagihan, Hiburan, **Kesehatan**, Lainnya — plus kamu bisa **bikin kategori sendiri** (emoji + warna custom).
- **Kalkulator rokok**: masukin harga sebungkus + isi + jumlah batang, otomatis dihitung totalnya.
- **Program berhenti merokok**: hitung hari bebas rokok otomatis + estimasi uang yang kehemat, berdasarkan rata-rata histori pengeluaran rokok kamu. Ada deteksi "relapse" kalau ternyata masih ada catatan beli rokok setelah tanggal mulai.
- **Insight cerdas**: kategori terboros, perbandingan vs bulan lalu, proyeksi akhir bulan, streak nyatet, semua dihitung otomatis dari data kamu (bukan dari server, 100% lokal & instan).
- **Kalender kebiasaan**: heatmap 30 hari terakhir kayak grafik kontribusi GitHub, biar keliatan pola boros/hematnya.
- **Tagihan berulang**: tandain pengeluaran sebagai tagihan bulanan (misal Netflix, kos, cicilan), nanti diingetin kalau belum dicatat bulan ini.
- **Statistik lebih dalam**: donut per kategori dengan naik/turun vs bulan lalu, grafik harian, dan tren 6 bulan.
- **Riwayat dengan pencarian & filter**: cari catatan, filter per kategori/hari ini/minggu ini/bulan ini.
- **Undo hapus** (snackbar 4.5 detik), **export ke JSON/CSV**, **import backup**, **mode terang/gelap**.
- Navigasi tab (Beranda / Statistik / Riwayat / Atur) biar gak numpuk di satu halaman kayak versi awal.

## Update terbaru (perbaikan)
- **Chart.js & jsPDF pakai CDN lagi** (bukan file lokal) — biar proses upload ke GitHub jauh lebih simpel dari HP (cuma 7 file, gak ada folder `vendor/` yang isinya file gede-gede). Konsekuensinya: pas pertama kali buka Catetin, HP harus online sebentar buat ambil library-nya. Setelah itu ke-cache otomatis, jadi kunjungan berikutnya tetep bisa offline.
- **Kalkulator rokok inget harga terakhir** — gak perlu ngetik ulang tiap kali nyatet.
- **Insight nambah pola hari-dalam-seminggu** (misal "Sabtu biasanya paling boros").
- **Pengingat harian lokal** (Atur → Pengingat): minta izin notifikasi browser, muncul kalau jam 7 malem lu belum nyatet apa-apa hari itu. Ini notifikasi lokal pas app dibuka/di-resume, bukan push background — keterbatasan PWA tanpa server, terutama di iOS.
- **Reminder backup**: kalau udah 14+ hari gak export data & catatan udah lumayan banyak, muncul banner ajakan backup di tab Atur.
- **Onboarding singkat** buat user baru (4 langkah, cuma muncul sekali & cuma kalau belum ada data sama sekali).
- **Cetak laporan PDF** (Atur → Cetak laporan PDF): pilih rentang (bulan ini / 30 hari / semua), hasilnya PDF rapi dengan header brand, ringkasan total, tabel sebaran kategori, dan rincian transaksi — siap dicetak atau dibagi.
- Perbaikan kecil: sudut rounded di baris pertama daftar pengaturan sekarang kepakai bener (sebelumnya ada bug CSS selector).

## Keterbatasan yang jujur diakui
- **Gak ada sync antar-device / akun bersama** — semua data di `localStorage` HP itu doang. Kalau mau ini, butuh backend (server + login), itu proyek terpisah dari PWA offline-first ini.
- **Notifikasi**: sekarang bisa pakai push asli (OneSignal) yang tetep muncul walau app ketutup, khusus Android/Chrome — tapi butuh App ID sendiri (gratis, langkah setup di atas) dan sedikit konfigurasi sekali di dashboard OneSignal buat jadwal harian. Tanpa itu, balik ke mode lokal (nyala pas app dibuka doang).
- **Belum ditest di HP fisik** — sudah dicek ketat dari sisi kode (sintaks, referensi elemen, logika), tapi belum ada sesi testing di device asli. Kalau nemu bug pas dipakai, laporin ya.

## Setup push notification asli (OneSignal, gratis)
Reminder harian yang lama cuma nyala pas app dibuka. Ini upgrade-nya biar notifnya beneran muncul walau app ketutup — pakai OneSignal (gratis buat pemakaian personal, jauh di bawah limit free tier-nya).

1. Daftar gratis di [onesignal.com](https://onesignal.com), bikin app baru → pilih platform **Web Push**.
2. Pas ditanya site URL, isi alamat tempat Catetin di-hosting (harus HTTPS — bisa pake Netlify/Vercel/GitHub Pages gratisan, atau `localhost` buat testing).
3. Setelah app dibuat, copy **App ID**-nya (ada di Settings → Keys & IDs).
4. Buka `index.html`, cari baris ini deket atas `<script>`:
   ```js
   const ONESIGNAL_APP_ID = "PASTE-ONESIGNAL-APP-ID-DI-SINI";
   ```
   Ganti dengan App ID lu.
5. Buka Catetin di HP Android → tab Atur → nyalain toggle **Pengingat nyatet harian** → izinin notifikasi.
6. Biar notifnya beneran kekirim otomatis tiap jam tertentu (misal jam 7 malem), di dashboard OneSignal:
   - Buka **Messages → Automated → New Automated Message** (atau menu **Journeys**, namanya kadang beda tergantung versi dashboard).
   - Pilih trigger "berdasarkan waktu" / *Automated Message* yang dikirim tiap hari jam sekian ke segmen **Subscribed Users**.
   - Isi judul & isi pesan, misal "Belum nyatet hari ini? 👀" / "Yuk catet pengeluaran di Catetin sebelum lupa."
   - Aktifkan. Ini yang jadi "otak" pengirim notifikasinya — app di HP lu gak perlu nyala buat ini jalan.

Tanpa App ID diisi, fitur reminder otomatis balik ke mode lokal (nyala pas app dibuka) — masih jalan, cuma gak sekuat push asli.

## Cara pakai
Buka `index.html` langsung di browser HP, atau hosting foldernya (Netlify/Vercel/GitHub Pages) lalu "Add to Home Screen" biar jadi app beneran (PWA, bisa offline).

Data lama dari v1 (kalau ada) otomatis kebaca/migrasi pas pertama kali dibuka.
