<div align="center">

# ✦ ManzxyMD — ManzxySelf v8 ✦

**Bot WhatsApp modular berbasis Baileys** — Jadibot pairing code custom, tombol native flow universal,
Carousel, Rich HTML Webview, database user/grup, dan manajer plugin langsung dari chat.

`Node.js ≥ 18` • `CommonJS` • `Low-RAM friendly (±350 MB)` • `Hot-reload plugin`

</div>

---

## 📑 Daftar Isi

1. [Fitur Utama](#-fitur-utama)
2. [Instalasi Cepat](#-instalasi-cepat)
3. [Konfigurasi (`config.js`)](#-konfigurasi-configjs)
4. [Menjalankan Bot](#-menjalankan-bot)
5. [Daftar Command](#-daftar-command)
6. [Jadibot (detail)](#-jadibot)
7. [Sewa Bot](#-sewa-bot-otomatis-per-grup)
7b. [Premium & Limit](#-premium--limit)
8. [Eval & Exec](#-eval--exec-root-owner-saja)
9. [Tombol di Mode Self](#-tombol-di-mode-self)
10. [Plugin Manager dari Chat](#-plugin-manager-dari-chat-svplug--delplug--editplug--getplug)
11. [Menulis Plugin](#-menulis-plugin)
12. [Tombol, Carousel & Rich HTML](#-tombol-carousel--rich-html)
13. [Struktur Database](#-struktur-database)
14. [Struktur Folder](#-struktur-folder)
15. [Console & Log](#-console--log)
16. [Daftar Perbaikan (Changelog)](#-daftar-perbaikan-changelog-v8)
17. [Troubleshooting](#-troubleshooting)
18. [Keamanan](#-keamanan)

---

## 🚀 Fitur Utama

| Fitur | Keterangan |
|---|---|
| 🤖 **Jadibot** | Target nomor lain, pengetik = owner, kode ke nomor target, berbayar KiPay (gratis Owner/Premium), button list pengelola, reconnect benar, auto-restore, masa sewa |
| ⭐ **Premium** | `.buyprem` per jam (Rp1.000) / per hari (Rp2.000) / hingga tahunan, hadiah ke nomor lain; limit harian unlimited |
| 🎟️ **Limit** | User biasa 50/hari (reset 00:00); Owner & Premium unlimited |
| 🏷️ **Sewa bot otomatis** | Per grup: kirim link → pilih paket (button list) → bayar QRIS → bot masuk grup & aktif sendiri → pengingat H-1 → habis: pamit & keluar |
| 💻 **Eval & Exec** | `.eval` / `.exec` + jalan pintas `> kode` dan `$ perintah` (Root Owner) |
| 🔘 **Tombol universal** | Native flow + node `biz` v9 untuk WA biasa, Bisnis, dan clone; otomatis fallback teks bila gagal |
| 🎠 **Carousel** | Kartu geser berisi gambar + tombol (1–10 kartu) |
| 🌐 **Rich HTML** | HTML kustom di-host server bot lalu dibuka di Webview WhatsApp |
| 📚 **Menu** | `.menu` (tombol kategori), `.allmenu` (semua fitur), `.kategori`, otomatis mengikuti plugin |
| 🗄️ **Database** | JSON atomik + backup, struktur user/grup/jadibot lengkap, reset limit harian |
| 🧩 **Plugin manager** | `.svplug` `.delplug` `.editplug` `.getplug` `.listplug` — tanpa bolak-balik ke VPS |
| 🖥️ **Console detail** | Banner ASCII, kotak info sistem, log command lengkap, kode alasan disconnect |
| ⚙️ **Low RAM** | Antrean command, cache grup, monitor memori, pembersih sesi aman |

---

## ⚡ Instalasi Cepat

```bash
# 1. Ekstrak proyek, masuk foldernya
cd ManzxySelf

# 2. Pasang dependency
npm install

# 3. Edit config.js  →  WAJIB ganti ownerNumber
nano config.js

# 4. Jalankan (pairing code akan ditanya di terminal)
npm start
```

Untuk VPS dengan **PM2**:

```bash
npm i -g pm2
PAIRING_NUMBER=628xxxxxxxxxx pm2 start ecosystem.config.js
pm2 logs ManzxyMD
pm2 save && pm2 startup
```

> ⚠️ Jalankan **hanya 1 proses** per sesi WhatsApp. Dua proses dengan sesi yang sama saling menendang
> (`440 Connection Replaced`).

---

## 🔧 Konfigurasi (`config.js`)

| Kunci | Default | Fungsi |
|---|---|---|
| `ownerNumber` | `['6281234567890']` | **Root Owner** (tanpa `+`). Hanya mereka yang boleh `.eval`, `.svplug`, dst. |
| `prefix / prefixes / multiPrefix / noPrefix` | `.` + `! # / ~` | Pengaturan prefix |
| `selfMode` | `true` | `true` = hanya owner & akun bot sendiri yang bisa memakai bot |
| `usePairingCode` | `true` | `false` = login pakai QR |
| `pairingNumber` | `env PAIRING_NUMBER` | Nomor bot (agar tidak ditanya di terminal / PM2) |
| `customPairingCode` | `'MANZKENZ'` | Kode pairing custom, **tepat 8 karakter** A–Z/0–9 tanpa `I L O U`. `''` = acak |
| `jadibot.max` | `10` | Maks sesi jadibot bersamaan |
| `jadibot.autoRestore` | `true` | Sesi tersimpan otomatis menyala lagi saat bot restart |
| `jadibot.pairingTimeoutSec` | `180` | Batas waktu user memasukkan kode |
| `jadibot.maxReconnect` | `10` | Batas gagal reconnect berturut-turut |
| `jadibot.maxPerUser` | `2` | Maks jadibot milik satu user (Root Owner tanpa batas) |
| `jadibot.botAccountIsOwner` | `false` | `true` = akun yang dijadikan bot juga ikut jadi owner botnya |
| `jadibot.freeForOwner` / `freeForPremium` | `true` | Gratis untuk Owner / user Premium |
| `jadibot.price` | lihat file | `perDay`, `minDays`, `maxDays`, `packages` (`'7d': 5000`, …) |
| `limit.daily` | `50` | Limit harian user biasa. Owner & Premium unlimited |
| `limit.freeCategories` | main, jadibot, sewa, premium, owner, plugin | Kategori yang tidak memakai limit (plugin juga bisa `limit: 0` / `limit: N`) |
| `premium.price` | `perDay: 2000, perHour: 1000, minHours: 1, maxDays: 3650` | Harga & batas premium |
| `exec.timeoutSec` | `120` | Batas waktu `.exec` |
| `button.selfLinkButtons` | `true` | Mode self: tombol balasan-cepat diganti tombol link wa.me |
| `sewa.enabled` / `enforce` | `true` / `false` | `enforce: true` = bot hanya melayani grup yang sedang menyewa (owner tetap bisa) |
| `sewa.freeForOwner` / `freeForPremium` | `true` / `false` | Gratis untuk Owner / Premium |
| `sewa.leaveOnExpire` / `remindBeforeHours` | `true` / `24` | Keluar grup saat habis · pengingat sebelum habis |
| `sewa.price` | lihat file | `perDay`, `minDays`, `maxDays` (3650 = ±10 tahun), `packages` |
| `button.selfNumberFallback` | `true` | Mode self: pilihan tombol juga dibuat daftar angka (balas "2") |
| `kipay.api_key` | `env KIPAY_API_KEY` | API key KiPay. Kosong = jadibot berbayar nonaktif |
| `kipay.pollIntervalSec` | `5` | Jeda cek status pembayaran |
| `database.path` | `./database/db.json` | Lokasi database |
| `database.defaultLimit` | `30` | Limit harian user baru |
| `button.botNode` | `true` | Node `<bot biz_bot=1>` agar tombol tampil di chat **pribadi**. Matikan bila tombol justru hilang |
| `button.fallbackText` | `true` | Kirim versi teks bila tombol gagal |
| `console.*` | lihat file | `logCommands`, `logAllMessages`, `stack`, `debug`, `clear` |
| `publicUrl` | `env PUBLIC_URL` | URL publik VPS untuk Rich HTML (disarankan **HTTPS**) |
| `ignoreOldSeconds` | `90` | Pesan lebih lama dari ini diabaikan (cegah command lama terulang saat bot baru hidup) |
| `categoryMeta` | — | Ikon, label, dan urutan kategori di menu |

---

## ▶️ Menjalankan Bot

| Perintah | Fungsi |
|---|---|
| `npm start` | Jalan sesuai `usePairingCode` di config |
| `npm run pairing` | Paksa login pakai pairing code |
| `npm run qr` | Paksa login pakai QR |
| `npm run check` | Cek sintaks file inti |

Setelah login pertama, sesi tersimpan di `session/`. Bila akun logout dari HP, folder sesi dihapus
otomatis dan bot meminta pairing baru.

---

## 📜 Daftar Command

Ikon 🔒 = khusus **Root Owner**. Daftar selalu terbaru lewat `.allmenu`.

### 🏠 Main
| Command | Fungsi |
|---|---|
| `.menu [kategori\|all]` | Menu utama dengan tombol kategori |
| `.allmenu` (`.menuall` `.fullmenu`) | **Semua fitur** per kategori |
| `.kategori <nama>` | Isi satu kategori |
| `.owner` | Kontak owner |
| `.profile` | Profil, level, limit, statistik dari database |

### 🤖 Jadibot
| Command | Fungsi |
|---|---|
| `.jadibot <nomor> [durasi]` | Buat jadibot untuk **nomor lain**. Pengetik = owner bot itu; kode dikirim ke nomor target. Berbayar (gratis untuk Owner & Premium) |
| `.listjadibot` | **Button list**: daftar jadibot milikmu + tombol ⏹️ matikan / ▶️ nyalakan / 🗑️ hapus (Root Owner melihat semua) |
| `.stopjadibot <nomor>` | Matikan (sesi tetap tersimpan) — hanya pemilik / Root Owner |
| `.deljadibot <nomor>` | Matikan + hapus sesi permanen |

### ⭐ Premium
| Command | Fungsi |
|---|---|
| `.buyprem` | Daftar harga + button list paket cepat (1 jam … 1 tahun) |
| `.buyprem <durasi>` | Beli untuk diri sendiri: `1h` `12h` `7d` `30d` `1y` `1d12h` `1y6mo` … |
| `.buyprem <nomor> <durasi>` | Hadiahkan premium ke nomor lain |
| `.cekprem` | Status premium & sisa limit |

### 🏷️ Sewa Bot
| Command | Fungsi |
|---|---|
| `.sewabot [link grup] [durasi]` (`.sewa`) | Menu paket sewa (button list). Di grup = perpanjang; di private dengan link = sewa grup itu. Durasi custom hingga tahunan: `.sewabot <link> 1y` |
| `.sewapay <paket> [link]` | Target tombol paket (jarang diketik manual) |
| `.ceksewa` | Status sewa grup ini / daftar sewa milikmu |
| `.listsewa` 🔒 | Daftar grup penyewa + tombol hentikan (bot keluar grup) |
| `.delsewa <jid>` 🔒 | Hentikan sewa & keluar grup |

### 🎛️ Interactive
`.allbutton` • `.carousel` • `.rich` • `.richhtml [url|<html>]`

### 👥 Group / 🛠️ Tools
`.hidetag <teks>` • `.ping`

### 🧩 Plugin 🔒
`.svplug` • `.delplug` • `.editplug` • `.getplug` • `.listplug`

### 👑 Owner
| Command | Fungsi |
|---|---|
| `.self` / `.public` / `.mode` | Mode bot (jadibot: per sesi & tersimpan) |
| `.ban` / `.unban <nomor>` | Ban user (juga lewat reply) |
| `.mute` / `.unmute` | Bot diam / aktif di grup |
| `.addprem <nomor> [durasi]` 🔒 | Jadikan user Premium (jadibot gratis). Juga `.delprem`, `.listprem` |
| `.reload` 🔒 | Muat ulang semua plugin |
| `.eval <kode>` 🔒 | Jalankan JavaScript, output **tanpa batas**. Jalan pintas: `> kode` / `=> kode` |
| `.exec <perintah>` 🔒 | Jalankan perintah shell di VPS, output **tanpa batas**. Jalan pintas: `$ perintah` |
| `.clearsession` 🔒 | Bersihkan sampah aman + cache RAM |
| `.dbinfo [user <no>\|group]` 🔒 | Statistik / isi database |

---

## 🤖 Jadibot

### Konsep
```
.jadibot <nomor> [durasi]      ← nomor = akun yang AKAN dijadikan bot (bukan nomor pengetik)
```
* **Pengetik = owner** jadibot itu (bisa `.mode`, `.listjadibot`, `.stopjadibot`, dst. di botnya).
  Akun yang dijadikan bot **tidak** otomatis owner (ubah lewat `jadibot.botAccountIsOwner`).
* **Kode pairing dikirim ke nomor target**, bukan ke pengetik (tidak pernah ke grup). Bila nomor target
  tidak bisa dihubungi (privasi), kode dikirim ke owner untuk diteruskan.
* Format nomor bebas: `628123456789`, `0812-3456-789`, `+62 812 3456 789`.
* Agar user biasa bisa memakai bot, jalankan bot di mode **public** (`.public`).

### Siapa yang bayar?
| Pengguna | Biaya | Durasi |
|---|---|---|
| Root Owner / Owner | Gratis | opsional (kosong = permanen) |
| User **Premium** (`.addprem`) | Gratis | opsional |
| User biasa | **Berbayar (QRIS KiPay)** | wajib, 1–90 hari |

Harga diatur di `config.js` → `jadibot.price` (paket `7d/30d/90d`, atau `perDay × hari`). `.jadibot` tanpa
argumen menampilkan daftar harga.

### Alur berbayar
```
.jadibot 0812xxx 7d
  └─ bot membuat tagihan KiPay → mengirim gambar QRIS + nominal TEPAT (sudah termasuk kode unik)
  └─ poller mengecek status tiap 5 dtk (status diverifikasi ke API KiPay, bukan webhook mentah)
  └─ LUNAS → jadibot dibuat otomatis → kode pairing dikirim ke nomor target → pembeli diberi tahu
  └─ target memasukkan kode: WhatsApp › Perangkat Tertaut › Tautkan dengan nomor telepon
```
* Order tersimpan di database → tetap diproses walau bot restart.
* Sudah lunas tetapi gagal dibuat (mis. slot penuh)? Dicoba ulang tiap 30 detik (maks 10×); bila tetap gagal,
  pembeli & Root Owner diberi notifikasi beserta ID transaksi.
* Tagihan kedaluwarsa → pembeli diberi tahu. Satu tagihan aktif per (pembeli, nomor).
* Menyalakan kembali sesi tersimpan milik sendiri yang masa sewanya belum habis **tidak** bayar ulang.
* Tanpa `kipay.api_key`: user biasa diarahkan menghubungi owner; Owner & Premium tetap gratis.

### Pesan "jadibot online"
Begitu jadibot tersambung, **nomor yang dijadikan bot menerima pesan** berisi nomor bot, owner, mode, masa aktif,
dan cara pakai (dikirim lewat bot utama; bila gagal, lewat chat ke diri sendiri). Pemilik juga diberi tahu.
Sesi yang dipulihkan otomatis saat restart tidak mengirim pesan ini lagi (agar tidak spam).

### Mengelola dari `.listjadibot`
Pesan berisi button list dengan bagian **⏹️ Matikan**, **▶️ Nyalakan kembali**, **🗑️ Hapus permanen**.
Memilih satu baris sama dengan mengetik `.stopjadibot <nomor>` / `.jadibot <nomor>` / `.deljadibot <nomor>` —
tanpa mengetik manual. Hanya pemilik (atau Root Owner) yang bisa mengelola sebuah jadibot.

### Alur teknis
```
sessions_jadibot/<nomor>  →  pairing code custom (MANZKENZ → MANZ-KENZ)
   └─ WhatsApp memutus koneksi (515 "restart required")  ← NORMAL
   └─ bot membuat socket baru dengan kredensial yang sama → connection "open"
```

### Penanganan kode disconnect
| Kode | Arti | Tindakan bot |
|---|---|---|
| `515` | Restart required | Reconnect 0,8 dtk (tidak dihitung gagal) |
| `401` `403` `411` | Logout / ditolak | Sesi **dihapus**, pemilik diberi tahu, **tanpa reconnect** |
| `440` | Digantikan sesi lain | Berhenti (tidak berebut sesi) |
| `408` `428` `500` dll | Putus sementara | Reconnect backoff 2 → 30 dtk, menyerah setelah `maxReconnect` |
| putus saat belum pairing | Kode kedaluwarsa | Sesi setengah jadi dibuang, minta `.jadibot` ulang |

### Hak akses
* Owner jadibot hanya berkuasa di botnya sendiri. Perintah berbahaya (`eval`, `svplug`, `reload`, …) hanya
  untuk **Root Owner** (`config.ownerNumber`) — pemilik jadibot **tidak** bisa menjalankan kode di server Anda.
* `jadibot` tidak bisa dijalankan dari dalam jadibot lain. Nomor yang sudah jadi milik orang lain tidak bisa direbut.
* Masa sewa habis → jadibot berhenti & sesi dihapus otomatis (dicek tiap 5 menit).

---

---

## 🏷️ Sewa Bot (otomatis, per grup)

```
[private]  .sewabot https://chat.whatsapp.com/xxxx     ← atau ketik .sewabot langsung di grup (perpanjang)
   └─ bot menampilkan nama grup + BUTTON LIST paket (7d • Rp5.000, 30d • …)
   └─ pilih paket → QRIS + nominal tepat
   └─ LUNAS → bot MASUK grup lewat link → sewa aktif → pesan aktivasi di grup → pembayar diberi tahu
   └─ H-1 (atur: remindBeforeHours) → pengingat + tombol "Perpanjang Sewa" di grup
   └─ habis → pamit di grup → bot keluar grup (leaveOnExpire)
```
* Perpanjang: masa baru **ditambahkan** ke sisa masa aktif (tidak hangus).
* Owner gratis (`sewa.freeForOwner`), Premium gratis bila `sewa.freeForPremium: true`.
* **Mode `enforce`**: grup tanpa sewa tidak dilayani (hanya `.sewabot`); petunjuk dikirim maks. 1× per 10 menit.
* Grup memakai persetujuan admin? Bot memasukkan permintaan; setujui, order otomatis dicoba ulang tiap 30 dtk.
* Order disimpan di database → tetap jalan walau bot restart. Pembayaran memakai KiPay yang sama dengan jadibot.

---

## ⭐ Premium & Limit

**Limit harian**: user biasa 50 per hari (reset tengah malam sesuai `timezone`). **Owner & Premium = unlimited.**
Tiap command memakai 1 limit, kecuali kategori gratis (`limit.freeCategories`) atau plugin berisi `limit: 0`
(mis. `.ping`). Limit hanya dipotong bila command **berhasil**. Saat habis, bot menampilkan tombol *Beli Premium*.
Tanda 🎟️ di menu = command memakai limit.

**Harga premium** (`premium.price`): **Rp2.000 per hari, Rp1.000 per jam** — dihitung otomatis dan jam sisa
tidak pernah lebih mahal dari 1 hari:

| Durasi | Harga | | Durasi | Harga |
|---|---|---|---|---|
| 1 jam | Rp1.000 | | 1 hari 1 jam | Rp3.000 |
| 2 jam | Rp2.000 | | 7 hari | Rp14.000 |
| 3–23 jam | Rp2.000 | | 30 hari | Rp60.000 |
| 1 hari | Rp2.000 | | 1 tahun | Rp730.000 |

Format durasi bebas dan bisa digabung: `30m`(tidak dijual, min 1 jam) `1h` `12h` `7d` `2w` `1mo` `1y` `1d12h` `1y6mo`;
maksimal `premium.price.maxDays` (3650 hari ≈ 10 tahun). Beli lagi saat masih aktif = **diperpanjang** (ditambah ke sisa).
Premium otomatis berakhir; Owner dapat memberi manual lewat `.addprem <nomor> [durasi]`.

---

## 💻 Eval & Exec (Root Owner saja)

| Cara | Fungsi |
|---|---|
| `.eval 1+1` · `> 1+1` | Evaluasi JS. Mendukung `await`, `return`, `console.log` |
| `.exec ls -la` · `$ ls -la` | Perintah shell di folder bot (tidak interaktif, timeout 60 dtk, output dipotong) |

Variabel di `eval`: `sock m ctx db config jadibot loader payment fs path util require`.
Contoh: `> db.summary()` · `> await sock.groupMetadata(ctx.from)` · `$ pm2 ls` · `$ free -m && df -h`.

Output **tanpa batas**: dipecah otomatis per ±60.000 karakter (batas satu pesan WhatsApp); bila lebih dari 180.000 karakter
dikirim sebagai file `.txt` utuh. Batas waktu `.exec` = `exec.timeoutSec`. Setiap pemakaian dicatat di console (`[EVAL]` / `[EXEC]`). Ini **eksekusi kode di VPS** — pastikan `ownerNumber`
benar; pemilik jadibot dan user lain tidak bisa memakainya.

---

## 🔘 Tombol di Mode Self

WhatsApp **mengunci tombol pada pesan yang dikirim akunmu sendiri** (terlihat pudar/abu-abu dengan ikon balasan).
Di mode self bot mengirim dari akunmu, jadi tombol *balasan-cepat* dan *list* tidak bisa ditekan — ini batasan aplikasi
WhatsApp, bukan bug kode. Solusi bawaan (aktif otomatis hanya bila **akun bot sendiri** yang mengetik command):

* **Tombol balasan-cepat → tombol link ↗** (`https://wa.me/<nomor bot>?text=<perintah>`): ketuk, chat terbuka dengan perintah
  sudah terisi, tinggal tekan kirim. Matikan lewat `button.selfLinkButtons: false`.
* **Daftar angka**: di bawah pesan ada daftar pilihan; balas `2` untuk memilih pilihan ke-2 (berlaku 3 menit,
  hanya dari pengirim yang sama). Cocok untuk list kategori. Matikan lewat `button.selfNumberFallback: false`.
* User lain (mode public, jadibot, pembeli) tetap memakai tombol asli yang bisa ditekan.
* Console mencatat `[TOMBOL] Klik diterima: ...` setiap klik sampai ke bot.
* Pesan keluar bot sendiri dicatat dan **diabaikan** saat kembali sebagai event (mencegah balasan berawalan prefix
  terbaca sebagai command baru).

---

## 🧩 Plugin Manager dari Chat (`svplug` / `delplug` / `editplug` / `getplug`)

Semua command ini **Root Owner only**, otomatis: validasi sintaks → backup → tulis → muat ulang →
**rollback bila gagal dimuat**. Backup ada di `backup_plugins/` (15 versi terakhir per plugin).

### `.svplug` — simpan / timpa plugin
```text
# A. Reply pesan berisi kode (atau file .js) lalu ketik:
.svplug tools/halo

# B. Tulis langsung (baris pertama nama, sisanya kode):
.svplug tools/halo
module.exports = {
    command: ['halo'],
    category: 'tools',
    description: 'Sapa',
    async execute(sock, m, { reply }) { await reply('Halo!'); }
};

# C. Nama boleh kosong → dibaca dari nama file / isi kode:
.svplug            (reply file cek.js  atau  reply kode)
```
Kategori baru (folder) dibuat otomatis. Bila nama sudah ada, plugin **diperbarui** dan versi lama di-backup.

### `.editplug` — edit tanpa ganti seluruh file
```text
# Ganti sebagian teks
.editplug ping
[CARI]
Cek kecepatan
[GANTI]
Tes kecepatan

# Timpa seluruh isi: reply kode / file .js
.editplug ping

# Lihat isi saat ini
.editplug ping
```
Teks pencarian harus **unik**; tambah `--all` untuk mengganti semua kemunculan.

### `.getplug` — ambil file
| Perintah | Hasil |
|---|---|
| `.getplug` | Daftar plugin |
| `.getplug ping` | Kirim `ping.js` sebagai dokumen |
| `.getplug ping --text` | Kirim sebagai teks |
| `.getplug ping --backup` | Versi backup terbaru (cara memulihkan plugin yang terhapus) |

Alur kerja nyaman: `.getplug x` → edit file di HP → reply file itu dengan `.svplug`.

### `.delplug` & `.listplug`
`.delplug <nama>` memindahkan plugin ke backup (bisa dipulihkan). Plugin manajemen (`svplug`, `delplug`,
`editplug`, `getplug`, `listplug`) dilindungi agar Anda tidak terkunci. `.listplug` menampilkan semua file + command.

Nama bisa berupa `kategori/nama`, nama file, atau nama command (mis. `.getplug hai`).

---

## 🛠️ Menulis Plugin

Taruh file `.js` di `plugins/<kategori>/`. Bot memuat otomatis dan **hot-reload** saat file berubah.

```js
module.exports = {
    command: ['halo', 'hai'],        // wajib (array / string)
    category: 'tools',               // opsional; default nama folder
    description: 'Menyapa user',     // tampil di menu
    usage: '.halo <nama>',           // opsional; tampil di menu
    // ── pembatas akses (semua opsional) ──
    rootOwner: false,                // hanya Root Owner (config.ownerNumber)
    owner: false,                    // owner bot / pemilik jadibot
    group: false,                    // hanya di grup
    private: false,                  // hanya chat pribadi
    admin: false,                    // hanya admin grup
    botAdmin: false,                 // bot harus admin
    order: 50,                       // urutan di menu (kecil = atas)
    limit: 1,                        // biaya limit per pakai (0 = gratis); default 1 kecuali kategori gratis

    async execute(sock, m, ctx) {
        const { from, sender, pushname, args, text, prefix, reply, user, group, db, isOwner, isAdmin } = ctx;
        await reply(`Halo ${pushname}!`);
    },

    // hook opsional: jalan untuk SETIAP pesan; return true = hentikan pemrosesan
    async before(m, ctx) { return false; }
};
```

### Isi `ctx`
`from` `sender` `senderNumber` `pushname` `body` `mtype` `quoted` `args` `text` (**mempertahankan baris baru**)
`command` `prefix` `isGroup` `groupMetadata` `groupName` `isOwner` `isRootOwner` `isAdmin` `isBotAdmin`
`isSelfMode` `user` `group` `db` `config` `reply()` `getPluginsByCategory()` `loadPlugins()`

> Error di dalam plugin **tidak** membuat bot mati/diam: dicatat di console lengkap dengan nama plugin,
> dan pengirim menerima pesan error singkat.

---

## 🔘 Tombol, Carousel & Rich HTML

```js
const { button } = require('../../lib/richHelper');

// Tombol
await sock.sendUniversalButton(from, {
    title: 'Judul', body: 'Isi', footer: 'Footer', image: { url: 'https://…' }
}, [
    button.quick('Menu', '.menu'),
    button.url('Website', 'https://example.com', true),   // true = webview
    button.copy('Salin', 'KODE123'),
    button.list('Pilih', [{ title: 'Bagian', rows: [{ title: 'Ping', description: '…', id: '.ping' }] }])
], m);

// Carousel (1–10 kartu)
await sock.sendCarousel(from, { body: 'Geser ➜' }, [
    { title: 'Kartu 1', body: '…', image: { url: '…' }, buttons: [button.quick('Pilih', '.menu')] }
], m);

// Rich HTML — HTML kustom (butuh publicUrl) atau URL langsung
await sock.sendRichHtml(from, { title: 'Halaman', html: '<h1>Halo</h1>' }, m);
await sock.sendRichHtml(from, { title: 'Web', url: 'https://example.com' }, m);

// Thumbnail besar
await sock.sendRichText(from, 'Teks', { title: '…', body: '…', thumbnailUrl: '…' }, m);
```

* Upload gambar gagal → pesan tetap dikirim tanpa gambar.
* Tombol gagal dikirim → otomatis dikirim versi teks (`button.fallbackText`).
* **Rich HTML**: set `PUBLIC_URL` (mis. `https://bot.domain.com` → reverse proxy ke port bot).
  Halaman disimpan di memori 1 jam (maks 50 halaman, 300 KB/halaman).

> ℹ️ Tampilan tombol/carousel ditentukan server & aplikasi WhatsApp, yang sering berubah. Jika di versi
> WhatsApp tertentu tombol tidak muncul di **chat pribadi**, coba `button.botNode: false`; jika tidak muncul
> di grup, cek log `[BUTTON]` / `[CAROUSEL]` di console.

---

## 🗄️ Struktur Database

File: `database/db.json` (JSON atomik: tulis `.tmp` → rename; salinan `db.json.bak`; bila rusak otomatis
dipulihkan dari `.bak`). Field yang hilang di data lama **dilengkapi otomatis** (migrasi aman) saat data dibaca.

```text
db.json
├── meta        { schemaVersion, createdAt, updatedAt }
├── settings    { bannedWords[], maintenance, totalCommandsRun }
├── users       { "<nomor>": User }
├── groups      { "<jid grup>@g.us": Group }
├── jadibots    { "<nomor>": Jadibot }
├── orders      { "<trx_id>": Order }          # transaksi KiPay
└── stats       { messages, commands, startCount, commandUsage{cmd: n} }
```

### `User` (kunci = nomor tanpa `+`)
| Field | Tipe | Keterangan |
|---|---|---|
| `number` `jid` `lid` | string | Identitas (`lid` = ID format baru WhatsApp) |
| `name` `names[]` | string | Nama terakhir & 5 riwayat nama |
| `registered` `regTime` `age` | bool/num | Registrasi (disiapkan untuk plugin) |
| `role` | string | `user` `premium` `owner` `banned` |
| `premium` | `{active, expire, since}` | Premium; otomatis nonaktif saat `expire` lewat |
| `banned` | `{active, reason, by, time}` | **Ditegakkan**: user di-ban diabaikan |
| `limit` | `{max, used, lastReset}` | Limit harian, reset otomatis tiap hari (zona waktu bot) |
| `level` `exp` | number | Naik otomatis: +5 exp per command, naik level tiap `level×100` exp |
| `money` `bank` | number | Saldo (untuk plugin ekonomi) |
| `warn` | number | Jumlah peringatan |
| `afk` | `{active, since, reason}` | Status AFK |
| `stats` | `{messages, commands, lastCommand, lastCommandAt, firstSeen, lastSeen}` | **Dihitung otomatis** |
| `commandUsage` | `{cmd: n}` | Pemakaian per command |
| `settings` | `{language, notify}` | Preferensi |
| `jadibot` | `{owned[], maxOwned}` | Jadibot milik user |
| `createdAt` `updatedAt` | ms epoch | |

### `Group` (kunci = JID grup)
| Field | Keterangan |
|---|---|
| `id` `name` `owner` `description` `size` | Diisi otomatis dari metadata grup |
| `banned` `{active, reason, time}` | **Ditegakkan**: grup di-ban diabaikan |
| `rent` `{active, since, expire, by, reminded, expiredPending}` | **Ditegakkan** oleh fitur sewa bot (pengingat H-1, keluar otomatis) |
| `settings.mute` | **Ditegakkan** — bot diam (kecuali owner/admin) · `.mute` `.unmute` |
| `settings.adminOnly` | **Ditegakkan** — hanya admin yang bisa command |
| `settings.prefix` | **Ditegakkan** — prefix khusus grup (`''` = ikut config) |
| `settings.welcome` `welcomeText` `bye` `byeText` | Disiapkan untuk plugin sambutan |
| `settings.antilink` `{enabled, mode, whitelist[]}` | Disiapkan untuk plugin (mode `delete`/`warn`/`kick`) |
| `settings.antibadword` `{enabled, words[]}` · `antispam` `{enabled, maxPerMinute}` · `antiviewonce` · `antidelete` · `rules` · `language` | Disiapkan untuk plugin |
| `disabledCommands[]` | **Ditegakkan** — command yang dimatikan di grup |
| `warns` `{nomor: n}` | Peringatan per anggota |
| `stats` `{messages, commands, lastActive, topUsers{nomor: n}}` | **Dihitung otomatis** |

### `Jadibot` (kunci = nomor)
`{ number, owner (pengetik .jadibot), requestedBy, createdAt, lastConnected, expire (0 = permanen), selfMode, status, reconnects }`
dengan `status` ∈ `pairing` `connecting` `open` `closed` `failed` `expired`.

### `Order` (kunci = `trx_id` KiPay)
`type` = `jadibot` (`target` = nomor) atau `rent` (`groupJid`, `inviteCode`, `groupName`, `targetKey`).
`{ trxId, type, target, owner, chat, durationMs, price, amount (nominal wajib bayar), status, attempts, lastError, createdAt, expiresAt, paidAt, doneAt }`
dengan `status` ∈ `pending` `paid` `fulfilling` `done` `expired` `failed`. Order selesai dibersihkan setelah 7 hari.

### Memakai database di plugin
```js
async execute(sock, m, { user, group, db, reply }) {
    user.money += 100;            // ubah langsung
    db.markDirty();               // jadwalkan simpan (debounce 3 dtk)
    const u = db.getUser('628123456789');          // ambil/buat user lain
    const g = db.getGroup('1203xxxx@g.us');       // ambil/buat grup
}
```
> Field bertanda "Disiapkan untuk plugin" sudah ada di struktur & dilengkapi otomatis, tetapi logikanya
> (mis. kirim pesan sambutan) perlu plugin tersendiri — struktur sudah siap dipakai.

---

## 📁 Struktur Folder

```text
ManzxySelf/
├── index.js                 # Boot, koneksi utama, reconnect, shutdown aman
├── handler.js               # Parsing pesan, identitas (LID), hak akses, eksekusi plugin
├── config.js                # Seluruh pengaturan
├── ecosystem.config.js      # PM2
├── lib/
│   ├── jadibotManager.js    # Sesi jadibot: pairing, reconnect, restore, sewa
│   ├── jadibotService.js    # Buat jadibot + kirim kode ke nomor target
│   ├── premium.js           # Harga & pemberian premium
│   ├── configDefaults.js    # Nilai bawaan config (melengkapi config.js lama)
│   ├── payment.js           # Order & poller pembayaran (jadibot + sewa)
│   ├── rental.js            # Sewa bot per grup: join, pengingat, keluar
│   ├── evalHelper.js        # Mesin eval & exec
│   ├── kipay.js             # Client KiPay QRIS
│   ├── richHelper.js        # Tombol, carousel, rich html, rich text
│   ├── database.js          # Database user/grup/jadibot
│   ├── pluginLoader.js      # Muat plugin + hot-reload
│   ├── pluginManager.js     # Logika svplug/delplug/editplug/getplug
│   ├── menuBuilder.js       # Pembuat menu
│   ├── htmlServer.js        # HTTP: keep-alive & hosting Rich HTML
│   ├── lowRamOptimizer.js   # Cache, antrean, monitor memori
│   ├── logger.js            # Banner & console
│   └── utils.js
├── plugins/
│   ├── main/ jadibot/ sewa/ premium/ interactive/ group/ tools/ owner/ plugin/
├── database/                # db.json (+ .bak)
├── backup_plugins/          # backup otomatis plugin
├── session/                 # sesi bot utama   (jangan dibagikan!)
└── sessions_jadibot/        # sesi tiap jadibot (jangan dibagikan!)
```

---

## 🖥️ Console & Log

* **Banner** ASCII, kotak **SYSTEM** (Node, Baileys, RAM bot/VPS, PID) dan **KONFIGURASI**.
* **BOT ONLINE**: akun, jumlah plugin/command, jumlah per kategori, jadibot aktif, isi database.
* Setiap command → kotak: pengirim (+ label OWNER/USER), chat (grup/pribadi), bot (MAIN / JADIBOT +nomor),
  perintah + argumen, waktu proses, RAM, panjang antrean, status `OK` / `ERROR` / `DITOLAK`.
* Disconnect ditulis lengkap dengan kode dan artinya (`428 Connection Closed`, `515 Restart Required`, …).
* Error plugin menampilkan nama plugin + 3 baris stack. Matikan lewat `console.stack: false`.
* `console.logAllMessages: true` untuk satu baris per pesan masuk; `console.debug: true` untuk log teknis.

---

## 🛠️ Daftar Perbaikan (Changelog v8)

### Jadibot — akar masalah "tidak bisa masuk"
1. **Deteksi logout salah**: `(error instanceof Boom)?.output?.statusCode` selalu `undefined`
   → bot menganggap semua putus koneksi boleh reconnect (termasuk logout → loop tanpa henti). Sekarang
   membaca `error.output.statusCode` dengan benar dan setiap kode ditangani sendiri.
2. **Reconnect setelah pairing selalu gagal**: setelah kode dimasukkan, WhatsApp memutus koneksi (515).
   Kode lama memanggil `startJadibot()` lagi, tetapi sesi masih ada di `Map` → ditolak "sudah aktif",
   dan error ditelan `.catch(() => {})`. Sekarang reconnect memakai fungsi internal `connect()`.
3. **Kode pairing palsu**: `MANZKENZ-XXXX-XXXX` hanyalah kode acak yang ditempeli awalan, sehingga tidak
   valid saat diketik. Sekarang memakai pairing code **custom asli** (8 karakter `MANZKENZ`) dengan
   fallback ke kode acak.
4. Permintaan kode hanya `setTimeout(3000)` tanpa retry → sekarang menunggu event `qr`, retry 4×.
5. Sesi setengah jadi tak pernah dibersihkan → ada timeout pairing & pembersihan.
6. Tidak ada pemulihan setelah restart → `autoRestore`.
7. Pemilik jadibot bisa `.eval` (menjalankan kode di server Anda) → sekarang hanya Root Owner.
8. `.listjadibot` selalu menampilkan "Public" (field `selfMode` tidak pernah dikirim) → diperbaiki;
   mode jadibot kini tersimpan di database.

### Jadibot v8.1 — model pemilik & berbayar
* `.jadibot <nomor> [durasi]`: target = nomor di argumen (sebelumnya selalu nomor pengetik).
* Pengetik menjadi owner di jadibot; kode pairing dikirim ke nomor target.
* `.listjadibot` kini button list dengan aksi matikan / nyalakan / hapus; user hanya melihat & mengelola miliknya.
* Berbayar via KiPay QRIS; gratis untuk Owner & Premium (`.addprem`).

### v8.3
* **Perbaikan error `.sewa`** (`Cannot read properties of undefined (reading 'enabled')`): `config.js` lama tidak punya bagian `sewa`.
  Kini `lib/configDefaults.js` otomatis melengkapi pengaturan baru yang belum ada di config.js-mu (bot menampilkan daftarnya di console).
* Menu didesain ulang (frame, progress bar level, status, limit, tips) dan dikirim sebagai **satu pesan utuh**.
* Eval/exec **tanpa batas teks** (dipecah / dikirim sebagai file). `.getplug --text`, `.editplug`, `.dbinfo` juga tidak dipotong.
* `.buyprem` — premium per jam/hari/tahunan (Rp1.000/jam, Rp2.000/hari), hadiah ke nomor lain, perpanjangan akumulatif.
* Limit harian 50 untuk user biasa; Owner & Premium unlimited.
* Sewa bot kini bisa custom hingga tahunan (`.sewabot <link> 1y`).
* Mode self: tombol balasan-cepat diganti tombol link wa.me.

### v8.2
* Jadibot online → pesan dikirim ke nomor yang dijadikan bot (sebelumnya memakai JID ber-`:device` sehingga tidak sampai).
* `.exec` + `.eval` lebih lengkap (await, console.log, timeout) + jalan pintas `>` dan `$`.
* Mode self: daftar angka sebagai pengganti tombol, pesan keluar bot tidak lagi diproses ulang, log klik tombol.
* Sewa bot otomatis per grup (button list → QRIS → bot masuk → pengingat → keluar saat habis).

### Tombol / Carousel / Rich HTML
* Carousel: `messageVersion: 1` tidak ada; kini ditambahkan, plus node `biz` & `bot`, validasi 1–10 kartu,
  tombol default bila kartu tanpa tombol, dan fallback teks.
* Tombol: `messageParamsJson` berisi `limited_time_offer` (dengan `expiration_time` dalam milidetik) dan
  `bottom_sheet` palsu yang mengganggu render → dihapus (opsional lewat `content.params`);
  body tidak lagi diisi ulang dengan daftar tombol; pesan dibungkus `viewOnceMessage` + `messageContextInfo`.
* Rich HTML sebelumnya hanya membuka situs contoh; kini bisa meng-host **HTML kustom** dan membuka URL apa pun.
* Upload media gagal tidak lagi membatalkan seluruh pesan.

### Bug lain
* `index.js`: logout menyebabkan loop reconnect; `console.clear()` tiap reconnect; kini backoff + sesi dibersihkan.
* `handler.js`: error plugin **ditelan tanpa jejak** (sekarang dicatat + dibalas); `text` kehilangan baris
  baru (merusak `.hidetag` & input kode); pesan `append`/offline lama bisa menjalankan ulang command;
  identitas LID (`@lid`) membuat deteksi owner gagal di WhatsApp versi baru; `templateButtonReply` tidak
  terbaca; hook `before()` tidak pernah dipanggil.
* `pluginLoader.js`: folder kategori baru tidak ikut di-watch; plugin rusak menimpa yang berjalan;
  kini ada validasi, indeks command, peringatan command ganda.
* `clearsession`: menghapus `session-*` & `pre-key-*` (kunci enkripsi aktif → pesan gagal terdekripsi);
  kini hanya menghapus `sender-key-*` lama (>24 jam).
* `TaskQueue`: error ditelan diam-diam; pesan dibuang tanpa log saat antrean penuh.

---

## 🩺 Troubleshooting

| Gejala | Solusi |
|---|---|
| Tagihan lunas tapi bot belum jadi | Cek console `[PAYMENT]`; order diulang otomatis tiap 30 dtk. Slot penuh? naikkan `jadibot.max` |
| `Gagal membuat tagihan` | Periksa `kipay.api_key`, dan nominal minimal yang diterima KiPay |
| Tombol "tidak bisa ditekan" di mode self | Batasan WhatsApp pada pesan dari akun sendiri → balas dengan **angka** (lihat bagian Tombol di Mode Self) |
| Sewa lunas tapi bot belum masuk grup | Link kedaluwarsa / grup perlu persetujuan admin. Dicoba ulang tiap 30 dtk (maks 10×), lalu owner diberi tahu |
| Jadibot: kode tidak diterima | Pastikan 8 karakter ditulis persis (`MANZKENZ`). Ketik ulang `.jadibot` untuk kode baru |
| Jadibot: "Gagal meminta kode pairing" | Cek koneksi VPS ke WhatsApp; lihat `[JADIBOT]` di console |
| Kode tampil acak, bukan MANZKENZ | Versi Baileys belum mendukung kode custom → `npm i @whiskeysockets/baileys@latest` |
| Tombol tidak muncul di chat pribadi | `button.botNode: false` (atau `true` bila sebelumnya `false`) |
| Tombol tidak muncul sama sekali | Lihat error `[BUTTON]` di console; pesan teks cadangan tetap terkirim |
| Rich HTML memakai situs contoh | Isi `PUBLIC_URL` (HTTPS) lalu restart |
| `440 Connection Replaced` | Ada proses bot lain memakai sesi yang sama (cek `pm2 ls`, `ps aux`) |
| Bot tidak merespons | Mode `selfMode: true` → hanya owner. Coba `.public`; cek `ownerNumber` |
| Command lama tidak jalan setelah bot hidup | Normal: pesan > `ignoreOldSeconds` diabaikan |
| `.svplug` "Sintaks tidak valid" | Periksa kode; pesan error memuat baris bermasalah. Plugin lama aman |

---

## 🔐 Keamanan

* Ganti `ownerNumber`. Jangan bagikan folder `session/` & `sessions_jadibot/` (sama dengan akses akun).
* `.eval` dan manajer plugin = eksekusi kode di server → hanya Root Owner. Tetap berhati-hati.
* Jika menyewakan jadibot: biarkan `selfMode` jadibot aktif (default) dan batasi `jadibot.max` sesuai RAM.
* Gunakan HTTPS untuk `PUBLIC_URL`.
* Penggunaan bot dapat melanggar ketentuan WhatsApp; risiko pemblokiran nomor ditanggung pengguna.

