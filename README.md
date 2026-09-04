# WhatsApp Group Guard Bot

Bot WhatsApp untuk jaga group, dibangun memakai [Baileys](https://github.com/WhiskeySockets/Baileys) resmi (`@whiskeysockets/baileys`). Support jalan di **Termux** atau **Pterodactyl Panel**.

> **Kenapa harus Baileys resmi?**
> Banyak fork Baileys di internet yang disusupi backdoor (mencuri session, auto-forward pesan ke server lain, dsb). Project ini sengaja cuma memakai package resmi dari npm registry: `@whiskeysockets/baileys`.

---

## Quick Start (Termux, dari Nol)

Step By Step Run via TERMUX **satu per satu**. 1

```bash
pkg update -y && pkg upgrade -y
pkg install nodejs -y
```

Cek hasil `node -v` 2.
```bash 
node -v
```

Kalau di bawah 20 Jalankan Command ini 3.
```bash
pkg install nodejs-lts -y
```

4 Lanjut:

```bash
pkg install git unzip -y
```
Step 5
```
git clone https://github.com/orythor/Xio-MD.git
cd Xio-MD
```
Step 6
```bash
npm install
```
Step 7 [Jalankan]
```bash
node index.js
```

Metode B Via File local Step 1
```bash
termux-setup-storage
cp /sdcard/Download/Xio-MD.zip ~/
cd ~
unzip Xio-MD.zip
cd Xio-MD
```

Step 2 [setelah berada di dalam folder `Xio-MD`]

```bash
npm install
```

Step 3 Edit `config.js`.

```bash
nano config.js
```
Save dengan `Ctrl+X`, lalu `Y`, lalu `Enter`.

Step 4 Jalankan. 
```bash
node index.js
```
Ikuti instruksi QR code / pairing code yang muncul di layar.

---

## Instalasi di Pterodactyl

Di tab **Startup**, pastikan **Startup Command**:
```
node index.js
```
```bash
node ^20 (node 20 saran) 
```

---

## Daftar Isi

1. [Quick Start (Termux, dari Nol)](#quick-start-termux-dari-nol)
2. [Fitur](#fitur)
3. [Struktur Folder](#struktur-folder)
4. [Persiapan (Semua Platform)](#persiapan-semua-platform)
5. [Instalasi di Termux](#instalasi-di-termux)
6. [Instalasi di Pterodactyl](#instalasi-di-pterodactyl)
7. [Menjalankan Bot](#menjalankan-bot)
8. [Setting Owner Pertama Kali](#setting-owner-pertama-kali)
9. [Daftar Command](#daftar-command)
10. [Penjelasan Fitur Jadibot](#penjelasan-fitur-jadibot)
11. [Troubleshooting](#troubleshooting)
12. [Catatan Keamanan](#catatan-keamanan)

---

## Fitur

| Command | Fungsi | Akses |
|---|---|---|
| `.jadibot [nomor]` | Jadikan sebuah nomor sebagai sub-bot (via pairing code) | Owner |
| `.listjadibot` | Lihat daftar sub-bot yang sedang aktif
| `.deletjadibot <nomor>` | Hapus/putuskan sub-bot tertentu
| `.kick @tag` | Kick member dari group (bisa juga reply pesan)
| `.delete` | Hapus pesan (reply ke pesan yang mau dihapus)
| `.addown @tag` | Tambah owner baru
| `.offgb` | Tutup grup (hanya admin yang bisa chat)
| `.ongb` | Buka grup lagi (semua member bisa chat) |
| `.setname <nama>` | Ganti nama grup (maks 25 karakter)
| `.setdeskripsi <teks>` | Ganti deskripsi grup
| `.setizin all/admin` | Atur siapa boleh edit info grup (nama/deskripsi/foto)|
| `.setpp` | Ganti foto profil grup (kirim gambar dengan caption `.setpp`, atau reply gambar lalu ketik `.setpp`)
| `.antilinkphishing on/off` | Auto-hapus pesan yang mengandung link phishing
| `.antilinkGC on/off` | Auto-hapus link invite group/channel WhatsApp lain
| `.antimedia on/off` | Blokir pengiriman media (gambar/video/dokumen/stiker)
| `.antispam on/off` | Auto-hapus pesan dari member yang mengirim terlalu cepat/beruntun
| `.menu` / `.help` | Tampilkan daftar command


## Struktur Folder

```
wabot/
├── index.js                  # Entry point, koneksi utama ke WhatsApp
├── handler.js                 # Router pesan masuk & command
├── config.js                  # Semua setting (owner, prefix, limit jadibot, dst)
├── package.json
├── database.json              # Auto-generate saat pertama kali run (JANGAN dihapus manual)
├── commands/
│   ├── jadibot.js
│   ├── listjadibot.js
│   ├── deletjadibot.js
│   ├── kick.js
│   ├── delete.js
│   ├── addown.js
│   ├── toggleGroupLock.js       # Logic .offgb dan .ongb (satu file, dua command)
│   ├── setnama.js
│   ├── setdeskripsi.js
│   ├── setizin.js
│   ├── setpp.js
│   ├── antilinkPhishing.js     # Logic guard (jalan otomatis)
│   ├── antilinkGC.js
│   ├── antimedia.js
│   ├── antispam.js
│   ├── toggleAntilinkPhishing.js  # Command .antilinkphishing on/off
│   ├── toggleAntilinkGC.js
│   ├── toggleAntimedia.js
│   ├── toggleAntispam.js
│   └── menu.js
├── lib/
│   ├── database.js             # Baca/tulis database.json
│   ├── helpers.js               # Fungsi bantu (cek admin, extract mention, dst)
│   ├── ui.js                    # Box border & reaction emoji untuk semua balasan bot
│   ├── jadibotManager.js        # Core logic spawn/hapus sub-bot
│   └── groupEvents.js
└── sessions/
    ├── main/                    # Session bot utama (auto-generate)
    └── sub_<nomor>/              # Session tiap sub-bot (auto-generate)
```

---

## Persiapan (Semua Platform)

Yang perlu disiapkan sebelum install:

1. **Nomor WhatsApp khusus untuk bot** (disarankan bukan nomor pribadi utama, karena ada risiko banned dari WhatsApp kalau dipakai). 
2. **Node.js versi 20 ke atas**. Cek dengan:
   ```bash
   node -v
   ```
   Kalau di bawah v20, update dulu (caranya beda-beda tergantung platform, lihat bagian masing-masing di bawah).
3. File bot ini (folder `wabot`) sudah di-extract di device/server kamu.
