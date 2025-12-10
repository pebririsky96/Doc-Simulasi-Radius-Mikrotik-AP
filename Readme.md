
## Doc-Simulasi-RADIUS-Mikrotik-Hotspot (FreeRADIUS & daloRADIUS)

Repositori ini berisi dokumentasi lengkap terkait proses simulasi dan implementasi autentikasi RADIUS pada perangkat MikroTik RouterOS (Hotspot) menggunakan FreeRADIUS dan daloRADIUS sebagai server manajemen pengguna. Proyek ini dirancang sebagai panduan teknis untuk pembelajaran, pengujian, maupun persiapan deployment pada lingkungan jaringan kampus, sekolah, maupun enterprise.

🎯 Tujuan Proyek

Dokumentasi ini dibuat untuk memberikan panduan menyeluruh mengenai:

- Instalasi dan konfigurasi FreeRADIUS pada server Linux.
- Integrasi daloRADIUS sebagai antarmuka web untuk manajemen user.
- Konfigurasi MikroTik Hotspot / WPA2-Enterprise / MAC-Auth agar terhubung ke RADIUS.
- Simulasi autentikasi user melalui berbagai skenario (CHAP, PAP, MAC-based).
- Alur login, struktur database RADIUS, dan mekanisme validasi di backend.
- Troubleshooting umum saat integrasi RADIUS dengan MikroTik dan Access Point.

🧩 Lingkup Dokumentasi

Repositori ini meliputi beberapa komponen utama:

1. Topologi & Arsitektur Simulasi

Menjelaskan desain jaringan uji coba, termasuk:

Router MikroTik (CHR atau perangkat fisik)

Access Point (Unifi / Omada / MikroTik)

Server FreeRADIUS + daloRADIUS

Alur autentikasi antara client → AP → RADIUS

2. Instalasi FreeRADIUS

Panduan langkah demi langkah:

Instalasi FreeRADIUS di Ubuntu

Konfigurasi clients.conf, mods-enabled, dan sites-enabled

Aktivasi modul SQL untuk integrasi database

3. Instalasi & Konfigurasi daloRADIUS

Mencakup:

Dependency PHP & webserver (Apache/Nginx)

Integrasi dengan database MariaDB/MySQL

Pengelolaan user, group, dan profile RADIUS

4. Konfigurasi MikroTik

Berbagai metode autentikasi:

Hotspot + CHAP

Hotspot + PAP

MAC authentication (MAC-as-username/password)

RADIUS untuk Wireless Security (WPA2-EAP)

Konfigurasi shared-secret, realm, dan debug log

5. Integrasi Access Point

Cara menghubungkan AP ke server RADIUS:

Unifi Controller (WPA2-Enterprise / External Portal / PPSK)

TP-Link Omada Controller

MikroTik CAPsMAN

6. Troubleshooting & Logging

Cara membaca log:

FreeRADIUS debug (freeradius -X)

MikroTik RADIUS log

daloRADIUS session & accounting

📂 Struktur Direktori

Contoh jika repositori Anda memiliki folder seperti berikut:

/topologi
/config-mikrotik
/freeradius
/daloradius
/hotspot-login
/screenshots


Sesuaikan dengan struktur aktual repositori.

📸 Screenshot & Output Pengujian

Dokumentasi mencakup:

Hasil login

Dashboard daloRADIUS

Log autentikasi MikroTik

Alur login CHAP/PAP/MAC-auth

Response dari FreeRADIUS debug mode

🚀 Manfaat & Use Case

Proyek ini cocok digunakan untuk:

Praktikum jaringan (SMK, Kampus, Pelatihan)

Simulasi sebelum implementasi produksi

Pembelajaran integrasi RADIUS lintas vendor

Penyusunan SOP atau standar jaringan enterprise

🤝 Kontribusi

Kontribusi terbuka untuk:

Perbaikan dokumentasi

Penambahan contoh konfigurasi AP lain

Penambahan skenario autentikasi lebih lanjut

Silakan lakukan fork, pull request, atau open issue untuk diskusi.
