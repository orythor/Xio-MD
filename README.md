# Xio-MD

Bot WhatsApp untuk jaga grup, dibangun pakai [Baileys](https://github.com/WhiskeySockets/Baileys) resmi (WhiskeySockets). Support Termux & Pterodactyl, login pakai Pairing Code (diutamakan) atau QR Code.

---

## ⚠️ Baca Dulu Sebelum Mulai

1. **Nomor bot sebaiknya BUKAN nomor utama kamu.** Pakai nomor WA kedua khusus buat bot. Kalau nomor bot ke-banned WhatsApp (karena spam/report), nomor utama kamu aman.
2. **Jangan pernah share isi file `.env`** ke siapapun atau upload ke repo publik. File ini berisi kunci akses ke database Supabase kamu.
3. Bot ini pakai Baileys versi `7.0.0-rc14` — ini masih **release candidate**, bukan versi stabil final. Sudah dipakai ratusan project lain, tapi tetap ada kemungkinan bug dari sisi library itu sendiri di luar kendali kode bot ini.

---

## 📋 Yang Kamu Butuhkan

- Node.js versi 18 ke atas
- Akun [Supabase](https://supabase.com) (gratis) — dipakai buat fitur `.antidelete` dan `.rvo`
- Nomor WhatsApp aktif buat jadi bot
- Termux (kalau run di HP Android) atau panel Pterodactyl (kalau run di VPS/hosting)

---

## 🚀 Setup — Langkah demi Langkah

### Langkah 1: Siapkan Database Supabase

1. Buka [supabase.com](https://supabase.com), login/daftar, lalu klik **New Project**.
2. Tunggu sampai project selesai dibuat (biasanya 1-2 menit).
3. Di sidebar kiri, klik **SQL Editor** → **New query**.
4. Buka file `supabase_schema.sql` yang ada di folder project ini, **copy semua isinya**, lalu tempel ke SQL Editor tadi.
5. Klik **Run** (atau tekan `Ctrl+Enter`). Kalau berhasil akan muncul "Success. No rows returned".
6. Masih di Supabase, buka **Project Settings** (ikon gerigi di sidebar) → **API**.
7. Catat 2 nilai ini, nanti dibutuhkan di Langkah 3:
   - **Project URL** (contoh: `https://xxxxx.supabase.co`)
   - **anon public** key (di bagian "Project API keys")

### Langkah 2: Download & Install Project

**Kalau di Termux (Android):**

```bash
pkg update && pkg upgrade -y
pkg install nodejs git -y
```

Lalu masuk ke folder project ini (kalau kamu dapat dalam bentuk zip/folder, extract dulu) dan:

```bash
cd xio-md
npm install
```

**Kalau di Pterodactyl:**

1. Buat server baru dengan Egg **Node.js** (versi 18+).
2. Upload seluruh isi folder project ini ke File Manager Pterodactyl (atau lewat SFTP).
3. Buka tab **Console**, jalankan:
   ```bash
   npm install
   ```

### Langkah 3: Isi File `.env`

1. Cari file bernama `.env.example` di folder project.
2. Duplikat file itu, ganti namanya jadi `.env` (buang `.example`-nya).
3. Buka `.env`, isi setiap baris:

```env
OWNER_NUMBER=628xxxxxxxxxx      # nomor WA kamu sendiri, buat akses command owner
LOGIN_METHOD=pairing            # "pairing" atau "qr"
BOT_NUMBER=628xxxxxxxxxx        # nomor WA yang mau dijadiin bot (isi kalau LOGIN_METHOD=pairing)
PREFIX=.                        # awalan command, defaultnya titik
SUPABASE_URL=https://xxxxx.supabase.co       # dari Langkah 1
SUPABASE_ANON_KEY=isi_key_kamu_disini        # dari Langkah 1
BOT_NAME=Xio-MD
```

> Semua nomor format-nya: kode negara + nomor, tanpa `+`, tanpa spasi, tanpa strip. Contoh nomor Indonesia: `6281234567890`.

### Langkah 4: Jalankan Bot

```bash
node index.js
```

- **Kalau `LOGIN_METHOD=pairing`** (disarankan): bot bakal nampilin kode 8 digit di terminal. Buka WhatsApp di HP nomor bot → **Perangkat Tertaut** → **Tautkan Perangkat** → **Tautkan dengan nomor telepon** → masukkan kode itu.
- **Kalau `LOGIN_METHOD=qr`**: bot nampilin QR code di terminal. Scan pakai WhatsApp di HP nomor bot lewat **Perangkat Tertaut** → **Tautkan Perangkat**.

Kalau berhasil, muncul tulisan `[XIO-MD] Berhasil terhubung ke WhatsApp`.

Sesi login tersimpan di folder `session/` — jadi lain kali kamu jalanin `node index.js` lagi, **nggak perlu pairing/scan ulang** selama folder itu nggak dihapus.

### Langkah 5 (Opsional): Jalankan di Background dengan PM2

Biar bot tetap jalan walau terminal ditutup:

```bash
npm install -g pm2
pm2 start index.js --name xio-md
pm2 logs xio-md      # buat lihat log
pm2 stop xio-md      # buat berhenti
```

---

## 📜 Daftar Command

`∆` = owner bot / admin grup saja. `©` = semua orang boleh pakai.

| Command | Fungsi | Akses |
|---|---|---|
| `.menu` | Nampilin daftar fitur | © |
| `.antimedia on/off` | Auto-hapus media dari member biasa | ∆ |
| `.antilinkphishing on/off` | Auto-hapus link mencurigakan (shortlink dll) | ∆ |
| `.antilinkgc on/off` | Auto-hapus link undangan grup WA lain | ∆ |
| `.antilinkch on/off` | Auto-hapus link channel WA | ∆ |
| `.antidelete on/off` | Tampilkan lagi pesan yang dihapus pengirimnya | ∆ |
| `.antispam on/off` | Aktifkan filter antispam | ∆ |
| `.addownerbot @user` | Jadikan seseorang owner bot (akses semua grup) | ∆ (owner only) |
| `.addadmingb @user` | Jadikan seseorang admin bot (akses grup ini saja) | ∆ |
| `.delete` (reply pesan) | Hapus pesan yang di-reply | ∆ |
| `.setppgb` (reply gambar) | Ganti foto profil grup | ∆ |
| `.setizingb admin/semua` | Atur siapa yang boleh kirim pesan di grup | ∆ |
| `.setdeskripsi <teks>` | Ganti deskripsi grup | ∆ |
| `.setnamagb <teks>` | Ganti nama grup | ∆ |
| `.kick @user` | Keluarkan member dari grup | ∆ |
| `.open` | Buka grup, semua bisa kirim pesan | ∆ |
| `.close` | Tutup grup, cuma admin bisa kirim pesan | ∆ |
| `.rvo` (reply pesan view-once) | Buka ulang foto/video sekali lihat | © |

**Catatan soal `.setizingb`:** nama command ini agak ambigu di spesifikasi awal. Gua interpretasiin sebagai "atur izin siapa yang boleh kirim pesan di grup" (`.setizingb admin` = cuma admin yang bisa chat, `.setizingb semua` = semua member bisa chat). Kalau maksudnya beda, kasih tau — command-nya gampang disesuaikan.

---

## ⚙️ Cara Kerja Fitur Jaga Grup

- Semua command `∆` bisa dipakai oleh: **owner bot** (nomor di `OWNER_NUMBER`, atau yang ditambahkan lewat `.addownerbot`), **admin grup WhatsApp asli**, atau **admin bot khusus grup itu** (yang ditambahkan lewat `.addadmingb`).
- Beberapa command (`.setppgb`, `.setizingb`, `.setdeskripsi`, `.setnamagb`, `.kick`, `.open`, `.close`) butuh **bot sendiri juga jadi admin** di grup itu, karena aksinya butuh izin admin dari sisi WhatsApp. Kalau bot belum admin, command bakal nolak jalan dan kasih tau.
- Setting on/off (antimedia dkk) disimpan per-grup di Supabase, jadi nggak hilang walau bot di-restart.

---

## 🗑️ Tentang Fitur `.antidelete`

Fitur ini nyimpen histori pesan **teks** ke Supabase, dan begitu pesan dihapus pengirimnya, bot kirim ulang isinya ke grup.

**Batasan yang perlu kamu tau:** versi ini **belum** bisa nampilin ulang foto/video yang dihapus — cuma teks. Ini keputusan sengaja, bukan bug: nyimpen setiap foto/video yang lewat di grup ke database bakal bikin storage Supabase kamu (terutama yang free tier) penuh dengan sangat cepat, karena grup aktif bisa kirim ratusan MB media per hari.

Kalau kamu tetap mau fitur itu, ini yang perlu diubah (dan konsekuensinya):
- Di `lib/events.js`, fungsi `handleGroupGuard`, ubah bagian `antidelete` supaya ikut nge-download & simpan `mediaBase64` untuk pesan gambar/video (bukan cuma teks).
- Konsekuensi: tabel `messages` di Supabase bakal tumbuh jauh lebih cepat. Kamu perlu monitor kuota storage Supabase kamu, dan pertimbangkan upgrade tier kalau grup-nya aktif.

Data pesan otomatis dibersihkan tiap hari untuk yang lebih tua dari 3 hari (lihat `lib/cleanup.js`), jadi tabel nggak akan tumbuh tanpa batas — tapi tetap perlu diperhatikan kalau kamu ubah supaya nyimpen media juga.

---

## 🔁 Fitur `.rvo` (Reveal View Once)

Command ini bisa buka ulang pesan foto/video "sekali lihat" (view once), dengan 2 cara:

1. **Kalau pesan masih "hidup"** di dalam pesan yang di-reply (misal orang lain forward/reply pesan view-once itu dalam waktu dekat) → bot langsung bisa buka isinya.
2. **Kalau sudah "terpakai"** (pesan asli sudah pernah dibuka/expired) → bot coba cari di cache Supabase (`viewonce_cache`), yang otomatis kesimpan tiap kali ada pesan view-once lewat di grup.

---

## 🐛 Troubleshooting

**Pairing code muncul di terminal, tapi HP nggak dapat notifikasi apa-apa.**
Ini kadang terjadi karena `browser` config di Baileys. Kode ini sudah pakai `Browsers.ubuntu('Chrome')` yang formatnya sesuai rekomendasi terbaru — kalau masih terjadi, coba hapus folder `session/`, lalu jalankan ulang.

**Koneksi putus terus / reconnect loop.**
Cek koneksi internet server/HP kamu. Kalau masih terjadi terus, coba hapus folder `session/` dan login ulang dari awal.

**Bot nggak bisa jalanin `.kick`, `.setnamagb`, dll.**
Pastikan bot sudah dijadikan **admin** di grup itu dulu lewat menu WhatsApp biasa.

**Error soal `.env` waktu start.**
Pastikan semua baris di `.env` sudah terisi, terutama `OWNER_NUMBER`, `SUPABASE_URL`, dan `SUPABASE_ANON_KEY`.

**Mau ganti gambar header `.menu`.**
Ganti aja file `assets/menu.jpg` dengan gambar lain (nama file harus tetap sama).

---

## 📁 Struktur Folder

```
xio-md/
├── index.js              # entry point utama
├── package.json
├── .env                  # konfigurasi kamu (JANGAN di-share)
├── .env.example           # template konfigurasi
├── supabase_schema.sql   # SQL buat setup database
├── assets/
│   └── menu.jpg           # gambar header .menu
├── session/               # sesi login WhatsApp (auto-generate)
├── lib/                   # semua kode inti (koneksi, permission, dll)
└── commands/               # satu file per command
```

---

## 🔒 Keamanan Kunci Supabase

Bot ini pakai **anon key** (bukan `service_role` key) buat konek ke Supabase, dan tabel-tabelnya dikonfigurasi dengan Row Level Security (RLS) yang mengizinkan baca/tulis penuh ke 4 tabel khusus bot ini (`messages`, `viewonce_cache`, `group_settings`, `bot_roles`) — bukan ke tabel lain yang mungkin kamu buat di project Supabase yang sama.

Karena itu, **anon key kamu setara kunci penuh ke 4 tabel tersebut**. Jangan taruh di kode yang di-expose ke client/browser, dan jangan commit file `.env` ke Git manapun.
