- 2025-12-10 13:37:35 by RouterOS 7.20.2
- software id = P22F-XEMU
- model = RB931-2nD
- serial number = HD3084Y7BKJ

```bash
/interface bridge
add name=Bridge1
```
- Bridge1 ditambahkan → berfungsi untuk menggabungkan interface Layer-2.
- Pilih bridge jika ingin menyatukan traffic beberapa interface dalam satu broadcast domain.
- Interface yang dimasukkan ke Bridge1 akan berbagi IP, DHCP, dan broadcast secara langsung.
```bash
/interface wireless
set [ find default-name=wlan1 ] band=2ghz-b/g/n disabled=no frequency=auto mode=\
    ap-bridge ssid=TEST-RADIUS
```
- Atur band ke 2.4GHz → agar kompatibel dengan banyak perangkat.
- Mode ap-bridge dipilih karena memungkinkan lebih dari satu client dan bisa di-bridge.
- Set frequency=auto untuk memilih channel terbaik otomatis.
- Masukkan SSID TEST-RADIUS → nama WiFi yang dipancarkan router.
```bash
/interface ethernet
set [ find default-name=ether1 ] name=ether1-WAN
set [ find default-name=ether3 ] name=ether3-LAN
```
- Mengubah nama interface agar mudah dipahami saat konfigurasi.
- Tidak mengubah fungsi; hanya mempermudah administrasi.

```bash
/ip hotspot profile
add dns-name=test.hotspot.radius hotspot-address=192.168.100.1 login-by=\
    mac,cookie,http-chap,http-pap,mac-cookie name=hsprof1 use-radius=yes
```

- dns-name=test.hotspot.radius
    - Menetapkan domain hotspot yang digunakan untuk redirect   login page.

- hotspot-address=192.168.100.1
    - Digunakan sebagai gateway + web login server.

- login-by=mac,cookie,http-chap,http-pap,mac-cookie
Tambahkan metode autentikasi yang berfungsi untuk:

    - MAC auth otomatis
    - CHAP/PAP untuk login halaman web
    - Cookie untuk login otomatis setelah sukses

- use-radius=yes
    - Atur agar hotspot mengirim autentikasi ke server RADIUS.
```bash
/ip pool
add name=dhcp_pool0 ranges=192.168.222.2-192.168.222.254
add name=hs-pool-1 ranges=192.168.100.2-192.168.100.254
 ```
- Menambahkan pool IP → berfungsi sebagai rentang IP dinamis untuk client hotspot.
- Pool ini akan digunakan DHCP dan Hotspot.
 ```bash
/ip dhcp-server
# Interface not running
add address-pool=hs-pool-1 interface=wlan1 name=dhcp1
```
- Menambahkan DHCP server pada interface WLAN1 untuk memberi IP otomatis ke client.
- Atur address-pool=hs-pool-1 agar DHCP mengambil IP dari pool tersebut.
- Catatan: WLAN1 tidak dalam Bridge—DHCP hanya melayani WLAN1 saja.
```bash
/ip hotspot
add address-pool=hs-pool-1 disabled=no interface=wlan1 name=hotspot1 profile=\
    hsprof1
```
- Menambahkan hotspot pada interface wlan1.
- Pilih profile hsprof1 sebelumnya untuk menggunakan konfigurasi RADIUS.
- Masukkan address-pool agar client hotspot mendapatkan IP dari pool tersebut.

```bash
/interface bridge port
add bridge=Bridge1 disabled=yes interface=ether3-LAN
add bridge=Bridge1 interface=ether1-WAN
add bridge=Bridge1 interface=ether2
```
- Menambahkan interface ke Bridge1.
- ether1-WAN dimasukkan ke bridge → router berubah menjadi switch pada sisi WAN.
- (Tidak direkomendasikan, tetapi secara teknis itulah yang terjadi.)
- ether3-LAN masih di-disable, artinya tidak ikut broadcast domain.
```bash
/ip firewall connection tracking
set udp-timeout=10s
```
- Atur timeout koneksi UDP ke 10 detik → berfungsi agar mapping NAT UDP cepat dibersihkan.
- Cocok untuk jaringan padat agar tabel connection tracking tidak penuh.
```bash
/ip neighbor discovery-settings
set discover-interface-list=!dynamic
```
- Menonaktifkan neighbor discovery di interface dinamis.
- Berfungsi meningkatkan keamanan dengan mencegah penemuan router oleh perangkat asing.
```bash
/ip address
add address=192.168.100.1/24 interface=wlan1 network=192.168.100.0
```
- Menambahkan IP gateway pada WLAN1.
- IP ini digunakan sebagai alamat hotspot, DHCP gateway, dan redirect login.

```bash
/ip dhcp-client
add default-route-tables=main interface=Bridge1
```
- Menambahkan DHCP client agar router mendapatkan IP internet otomatis dari ISP.
- Karena ether1-WAN berada di Bridge1, maka Bridge1 dipilih sebagai interface WAN.
- Set default-route-tables=main agar rute default masuk ke routing table utama.
```bash
/ip dhcp-server network
add address=192.168.100.0/24 comment="hotspot network" gateway=192.168.100.1
```
Atur informasi network DHCP seperti gateway dan subnet mask untuk client.
```bash
/ip dns
set allow-remote-requests=yes servers=8.8.8.8
```
- Mengaktifkan DNS resolver pada router → client dapat menggunakan router sebagai DNS server.
- Masukkan DNS 8.8.8.8 (Google) agar query diteruskan ke internet.

```bash
/ip firewall filter
add action=passthrough chain=unused-hs-chain comment="place hotspot rules here" \
    disabled=yes
```

 ```bash   
/ip firewall nat
add action=passthrough chain=unused-hs-chain comment="place hotspot rules here" \
    disabled=yes
add action=masquerade chain=srcnat
add action=masquerade chain=srcnat comment="masquerade hotspot network" \
src-address=192.168.100.0/24
```
- Tambahkan rule NAT masquerade → berfungsi agar IP private client dapat di-NAT menjadi IP publik.
- Rule kedua memperkuat NAT khusus untuk subnet hotspot.
```bash
/ip ipsec profile
set [ find default=yes ] dpd-interval=2m dpd-maximum-failures=5
```
- Atur Dead Peer Detection → memonitor tunnel IPSec.
- Tidak ada dampak ke konfigurasi hotspot.

```bash
/radius
add address=10.10.0.225 radsec-timeout=10ms require-message-auth=no service=\
    hotspot timeout=10ms
```
- Masukkan IP server RADIUS untuk meng-handle autentikasi hotspot.
- Timeout kecil (10ms) digunakan untuk mempercepat respons.
- Radius akan menerima:
    - Access-Request (MAC/PAP/CHAP)
    - Accounting packets
    - CoA (jika incoming aktif)

```bash
/radius incoming
set accept=yes
```
- Mengaktifkan Change of Authorization (CoA).
- Berfungsi untuk:
    - Disconnect user
    - Update bandwidth
    - Apply policy tanpa user harus login ulang

```bash
/routing bfd configuration
add disabled=no interfaces=all min-rx=200ms min-tx=200ms multiplier=5
```
- Digunakan untuk deteksi link failure pada routing dynamic.
- Bekerja jika OSPF/BGP aktif, tetapi konfigurasi ini tinggal sebagai baseline.
```bash
/system clock
set time-zone-name=Asia/Jakarta
```
Konfigurasi waktu
```bash
/system identity
set name=RouterOS
```
Konfigurasi Nama Router