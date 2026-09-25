<div align="center">

# 📸 HaziBooth

**Photobooth online untuk mengabadikan momen bersama, meskipun terpisah jarak**

[![Node.js](https://img.shields.io/badge/Node.js%2018+-339933?logo=nodedotjs\&logoColor=white)](https://nodejs.org)
[![Socket.IO](https://img.shields.io/badge/Socket.IO-010101?logo=socketdotio\&logoColor=white)](https://socket.io)
[![WebRTC](https://img.shields.io/badge/WebRTC-333333?logo=webrtc\&logoColor=white)](https://webrtc.org)
[![Vanilla JS](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript\&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![Canvas](https://img.shields.io/badge/HTML5%20Canvas-E34F26?logo=html5\&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)

</div>

**HaziBooth** adalah photobooth berbasis web yang memungkinkan dua orang mengambil foto bersama secara online meskipun berada di tempat yang berbeda.

Dua perangkat, satu booth. Pengguna masuk ke ruang privat, dapat melihat satu sama lain secara langsung, lalu menekan tombol shutter bersama-sama. Countdown yang disinkronkan oleh server akan mengambil foto dari kedua kamera pada waktu yang sama.

Hasil foto kemudian dipasangkan dan dirender menjadi **photostrip digital** yang dapat disimpan dan diunduh.

---

## 📑 Daftar Isi

* [Tentang HaziBooth](#tentang-hazibooth)
* [Fitur](#fitur)
* [Teknologi](#teknologi)
* [Struktur Repository](#struktur-repository)
* [Cara Kerja](#cara-kerja)
* [Instalasi Lokal](#instalasi-lokal)
* [Deployment](#deployment)
* [Validasi](#validasi)
* [Catatan & Roadmap](#catatan--roadmap)
* [Pengembang](#pengembang)
* [Lisensi](#lisensi)

---

## 💡 Tentang HaziBooth

Photobooth biasanya membutuhkan dua orang untuk berada di tempat yang sama. Namun, jarak sering kali membuat hal tersebut tidak memungkinkan.

Video call memang memungkinkan dua orang saling melihat, tetapi belum memberikan pengalaman seperti mengambil foto bersama di sebuah photobooth.

**HaziBooth hadir untuk menjembatani hal tersebut.**

| Tantangan                                             | Solusi HaziBooth                                             | Hasil                                                              |
| ----------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------ |
| Dua orang berada di lokasi berbeda                    | Setiap perangkat menjadi bagian dari satu photobooth virtual | Foto bersama meskipun berada di tempat berbeda                     |
| Dua kamera harus mengambil foto secara bersamaan      | Countdown disinkronkan melalui server                        | Foto diambil pada waktu yang hampir bersamaan                      |
| Koneksi WebRTC dapat terhambat oleh jaringan tertentu | Tersedia fallback preview melalui server                     | Preview tetap dapat digunakan ketika koneksi P2P mengalami kendala |
| Tidak ingin menggunakan perangkat photobooth khusus   | Menggunakan kamera dan browser perangkat masing-masing       | Photobooth dapat digunakan tanpa perangkat tambahan                |

HaziBooth dirancang agar pengguna cukup menggunakan:

* 📱 Dua perangkat
* 🌐 Browser
* 📷 Kamera
* 🚀 Server Node.js

Tidak membutuhkan aplikasi khusus atau perangkat photobooth fisik.

---

## ✨ Fitur

* **🔒 Ruang Privat**

  * Setiap room dapat digunakan oleh maksimal dua orang.
  * Pengguna dapat berbagi nama room atau link kepada pasangan.

* **📹 Live Partner Preview**

  * Melihat kamera pengguna lain secara langsung menggunakan WebRTC.
  * Tersedia mekanisme koneksi ulang otomatis.
  * Memiliki fallback preview melalui server ketika koneksi peer-to-peer tidak dapat digunakan.

* **⏱️ Synchronized Capture**

  * Countdown disinkronkan menggunakan timestamp dari server.
  * Setiap perangkat memperkirakan perbedaan waktu antara client dan server.

* **📸 Dual Camera Capture**

  * Mendukung kamera depan maupun belakang.
  * Kamera dapat diganti secara langsung.

* **🖼️ Shared Photo Tray**

  * Kedua pengguna dapat melihat hasil foto dari masing-masing perangkat.
  * Mendukung hingga enam foto untuk setiap pengguna.

* **🎞️ Photostrip Builder**

  * Tersedia berbagai template photostrip:

    * Classic 35mm Film Strip
    * Long 6-Frame Film Strip
    * Instax Instant Print
    * Couple Strip
    * 2 × 2
    * 3 × 2
    * Dark Polaroid
  * Mendukung efek warna bergaya Fuji, Instax, dan DSLR.
  * Dilengkapi grain dan vignette sesuai template.

* **⚡ Screen Flash**

  * Efek flash pada layar yang dapat digunakan ketika mengambil foto.
  * Flash disinkronkan dengan proses capture.

* **🔄 Koneksi Tangguh**

  * Auto-reconnect.
  * Penanganan ketika room sudah penuh.
  * Cleanup otomatis ketika pengguna keluar dari room.

---

## 🛠️ Teknologi

| Bagian     | Teknologi                     | Keterangan                                 |
| ---------- | ----------------------------- | ------------------------------------------ |
| Server     | Node.js ≥ 18 + Socket.IO      | Signaling, room, countdown, dan relay foto |
| Live Video | WebRTC                        | Komunikasi video peer-to-peer              |
| Kamera     | `MediaDevices.getUserMedia()` | API kamera native browser                  |
| Rendering  | HTML5 Canvas                  | Kompresi foto dan pembuatan photostrip     |
| Client     | Vanilla JavaScript            | Tanpa framework dan tanpa build step       |

HaziBooth menggunakan teknologi web native sehingga aplikasi tetap ringan dan tidak membutuhkan framework frontend yang kompleks.

---

## 📁 Struktur Repository

```text
hazibooth/
├── server.js            # HTTP + Socket.IO signaling server
├── public/
│   └── index.html       # UI, WebRTC, kamera, capture, dan photostrip
├── test/
│   ├── screenshot.js    # Pengujian visual menggunakan headless browser
│   └── webrtc-e2e.js    # Pengujian WebRTC menggunakan dua browser
├── package.json
└── .gitignore
```

---

## ⚙️ Cara Kerja

Secara sederhana, alur HaziBooth adalah sebagai berikut:

### 1. Membuat / Memasuki Room

Setiap pengguna masuk ke sebuah room melalui server Socket.IO.

### 2. Menghubungkan Kamera

Kedua perangkat melakukan pertukaran WebRTC offer, answer, dan ICE candidate melalui server.

Setelah koneksi berhasil, video dapat mengalir secara langsung antar perangkat.

### 3. Sinkronisasi Shutter

Ketika pengguna menekan tombol shutter, server menentukan waktu capture.

Setiap perangkat memperkirakan perbedaan waktu antara clock lokal dan server sehingga countdown dapat berjalan secara sinkron.

### 4. Mengambil Foto

Ketika countdown selesai:

* Kamera mengambil frame.
* Frame dikompresi menjadi JPEG.
* Foto dikirim ke server.
* Server meneruskan foto tersebut kepada pengguna lainnya.

### 5. Membuat Photostrip

Foto dari kedua pengguna dipasangkan berdasarkan ID capture.

Selanjutnya foto dirender menggunakan HTML5 Canvas ke dalam template photostrip yang dipilih.

---

## 🚀 Instalasi Lokal

Pastikan sudah menginstal **Node.js versi 18 atau lebih baru**.

Clone repository:

```bash
git clone https://github.com/z1dan820/hazobooth.git
cd hazobooth
```

Install dependency:

```bash
npm install
```

Jalankan aplikasi:

```bash
npm start
```

Aplikasi dapat diakses melalui:

```text
http://localhost:3000
```

Buka alamat tersebut pada dua browser atau dua perangkat, kemudian masukkan nama room yang sama.

Setelah kedua pengguna masuk ke room, photobooth siap digunakan.

### Mode Development

Untuk menjalankan server dan melakukan restart ketika terdapat perubahan file:

```bash
npm run dev
```

Untuk melakukan pengecekan syntax:

```bash
npm run check
```

---

## 🌐 Deployment

Untuk penggunaan di luar `localhost`, **HTTPS diperlukan agar browser dapat mengakses kamera**.

Gunakan reverse proxy atau platform yang menyediakan TLS, seperti:

* Caddy
* Nginx
* Platform hosting dengan HTTPS

### Konfigurasi TURN

Untuk meningkatkan reliabilitas WebRTC pada jaringan yang memiliki NAT atau firewall tertentu, dapat digunakan TURN server.

Environment variable yang tersedia:

| Variable          | Fungsi                                                          |
| ----------------- | --------------------------------------------------------------- |
| `PORT`            | Port HTTP, default `3000`                                       |
| `TURN_URL`        | URL TURN server, dapat berupa beberapa URL yang dipisahkan koma |
| `TURN_USERNAME`   | Username untuk autentikasi TURN                                 |
| `TURN_CREDENTIAL` | Password / credential TURN                                      |
| `ALLOWED_ORIGIN`  | Override origin untuk Socket.IO handshake                       |

Contoh:

```env
PORT=3000
TURN_URL=turn:turn.example.com:3478
TURN_USERNAME=username
TURN_CREDENTIAL=password
ALLOWED_ORIGIN=https://example.com
```

Tanpa TURN server, WebRTC tetap dapat bekerja pada jaringan yang memungkinkan koneksi peer-to-peer.

Jika koneksi peer-to-peer tidak dapat dilakukan, HaziBooth memiliki mekanisme fallback berupa preview dengan frame rate rendah melalui server.

---

## 🧪 Validasi

Beberapa command yang tersedia untuk melakukan pengujian:

### Pengecekan Syntax

```bash
npm run check
```

### WebRTC End-to-End Test

```bash
node test/webrtc-e2e.js
```

Pengujian akan menjalankan dua headless browser yang bergabung ke room dan memeriksa kondisi WebRTC dari kedua sisi.

### Screenshot Test

```bash
node test/screenshot.js
```

Pengujian menjalankan browser dengan kamera simulasi dan menghasilkan screenshot untuk setiap tampilan.

---

## 📝 Catatan & Roadmap

### Penyimpanan Data

Saat ini, data room dan foto disimpan di **memory server**.

Setiap pengguna dapat memiliki hingga enam foto dalam satu room.

Karena data berada di memory, semua room dan foto akan hilang ketika server di-restart.

### 🚧 Roadmap

Beberapa pengembangan yang dapat dilakukan selanjutnya:

* [ ] Penyimpanan room menggunakan Redis
* [ ] Penyimpanan foto menggunakan object storage
* [ ] Sistem expiry otomatis untuk room
* [ ] Shareable photostrip URL
* [ ] Peningkatan kualitas fallback preview
* [ ] Pengembangan template photostrip tambahan
* [ ] Peningkatan pengalaman pengguna pada perangkat mobile

---

## 👨‍💻 Pengembang

**Fahrul Hamzidan Pulungan**

### HaziBooth

Photobooth digital untuk mengabadikan momen bersama, tanpa batas jarak.

Repository:

**https://github.com/z1dan820/hazobooth**

---

## 📄 Lisensi

Project ini menggunakan lisensi **ISC**.

Lihat file `LICENSE` untuk informasi lengkap mengenai ketentuan penggunaan project.
