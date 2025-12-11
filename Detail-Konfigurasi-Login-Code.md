Baik, berikut **penjelasan lengkap & teknis** untuk setiap bagian kode HTML + JavaScript login page MikroTik Hotspot (MAC Auth).
Penjelasan dibuat **per bagian, baris demi baris**, sesuai fungsi di sistem Hotspot MikroTik.

---

# 🧱 **1. Struktur HTML Dasar**

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="pragma" content="no-cache" />
    <meta http-equiv="expires" content="-1" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Internet Hotspotz - MAC Auth Login</title>
    <link rel="stylesheet" href="css/style.css">
</head>
```

### Penjelasan:

* **doctype & <html> lang="en"** → mendefinisikan dokumen HTML berbahasa Inggris.
* **<meta charset="utf-8">** → memastikan halaman dapat membaca semua karakter Unicode.
* **pragma + expires = no-cache** → memaksa browser *tidak menyimpan cache*, penting agar halaman login selalu terbaru.
* **viewport** → membuat tampilan responsif di perangkat mobile.
* **title** → judul tab browser.
* **link stylesheet** → memuat file CSS untuk mengatur tampilan login.

---

# 🔐 **2. Bagian CHAP Authentication**

MicroTik Hotspot mendukung CHAP (Challenge-Handshake Authentication Protocol).
Jika router mengirimkan variabel **chap-id**, maka blok ini akan diaktifkan:

```html
$(if chap-id)
```

Artinya:
**Jika CHAP aktif, jalankan kode ini.**
Jika tidak, bagian ini **tidak dimasukkan** ke halaman.

---

# 📝 **3. Hidden Form “sendin”**

```html
<form name="sendin" action="$(link-login-only)" method="post" style="display:none">
    <input type="hidden" name="username" />
    <input type="hidden" name="password" />
    <input type="hidden" name="dst" value="$(link-orig)" />
    <input type="hidden" name="popup" value="true" />
</form>
```

### Fungsi:

* **Form hidden** digunakan untuk mengirim data login terenkripsi (MD5).
* MikroTik CHAP selalu meminta **password dalam bentuk hash**, bukan plaintext.
* Isian:

  * **username** → nanti diisi JS dari input form utama.
  * **password** → nanti diisi hash MD5.
  * **dst** → URL tujuan user sebelum redirect ke login page.
  * **popup=true** → memberi sinyal ke MikroTik bahwa login boleh membuka jendela kecil.

---

# 🔣 **4. Import md5.js**

```html
<script src="/md5.js"></script>
```

* Memasukkan script MD5 bawaan MikroTik.
* Diperlukan agar password bisa di-hash sebelum dikirim.

---

# 🔄 **5. Fungsi JavaScript doLogin()**

```javascript
function doLogin() {
    document.sendin.username.value = document.login.username.value;
```

→ Mengambil username dari form “login” dan memasukkan ke form “sendin”.

```javascript
var pass = "macauth";
```

→ Password **dikunci** ke nilai “macauth”.
(Metode umum untuk MAC Authentication MikroTik.)

```javascript
document.sendin.password.value = hexMD5('$(chap-id)' + pass + '$(chap-challenge)');
```

→ Membuat password hash MD5 sebagai **CHAP response**:

Format hash MikroTik:

```
MD5( chap-id + password + chap-challenge )
```

```javascript
document.sendin.submit();
return false;
```

→ Mengirim form hidden, **bukan** form utama.
→ `return false` mencegah form utama terkirim plaintext.

---

# 🔚 **6. Penutup Kondisi CHAP**

```html
$(endif)
```

→ Menutup blok kondisional CHAP.

---

# 🎨 **7. Struktur Tampilan Login**

```html
<div class="ie-fixMinHeight">
    <div class="main">
        <div class="wrap animated fadeIn">
```

* Wrapper layout → CSS mengatur efek animasi dan posisi.

---

# 🖊️ **8. Form Login Utama**

```html
<form name="login" action="$(link-login-only)" method="post" $(if chap-id) onSubmit="return doLogin()" $(endif)>
```

### Fungsi:

* Form ini hanya *menampilkan input*.
* Jangan submit langsung jika CHAP → maka JS menjalankan **doLogin()**.

---

# 🔍 **9. Hidden Fields**

```html
<input type="hidden" name="dst" value="$(link-orig)" />
<input type="hidden" name="popup" value="true" />
```

* dst → URL tujuan sebelum login
* popup=true → opsional untuk membuka jendela informasi setelah login

---

# 🏫 **10. Bagian Logo**

```html
<img src="" alt="" style="width:120px;">
```

* Tempat meletakkan logo perguruan tinggi / instansi.

---

# 🔔 **11. Pesan Status Login**

```html
<p class="info $(if error)alert$(endif)">
    $(if error == "")
        Silakan masukkan MAC Address kemudian akan diverifikasi otomatis oleh sistem.
    $(endif)
    $(if error)$(error)$(endif)
</p>
```

### Mekanisme:

* Jika **tidak ada error** → tampilkan instruksi.
* Jika ada **error** (contoh: invalid username) → tampilkan pesan error dari MikroTik.

---

# 🧍 **12. Input Username**

```html
<label>
    <img class="ico" src="img/user.svg" />
    <input name="username" type="text" value="$(username)" placeholder="Username / MAC Address" required />
</label>
```

Fungsinya:

* User mengisi username → biasanya **MAC Address** untuk MAC-auth.
* MikroTik variabel `$(username)` mengisi otomatis jika user pernah login sebelumnya.

---

# 🕵️‍♂️ **13. Password Disembunyikan**

```html
<input type="hidden" name="password" value="macauth" />
```

* Password tidak ditampilkan ke user.
* Password default untuk MAC-auth = **"macauth"**.
* Jika CHAP → password ini **tidak dikirim plaintext**, tapi di-hash oleh JS.

---

# ▶️ **14. Tombol Login**

```html
<input type="submit" value="Connect" />
```

* Menjalankan login.
* Jika CHAP → tombol akan memicu **doLogin()**.

---

# 🧱 **15. Penutup Body & HTML**

```html
</form>
</div>
</div>
</div>
</body>
</html>
```

Menyelesaikan struktur halaman.

---

# 🎯 **RINGKASAN ALUR LOGIN**

1. User mengisi MAC Address sebagai username.
2. Jika CHAP:

   * Browser membuat **hash MD5**: chap-id + password + chap-challenge
   * Form hidden “sendin” yang dikirim.
3. MikroTik menerima paket:

   * username = MAC
   * password hash (CHAP)
4. MikroTik meneruskan ke **RADIUS server**.
5. Jika RADIUS Accept → client mendapatkan akses internet.

---

# Mau saya buatkan versi:

✅ Penjelasan yang lebih teknis (RADIUS flow)?
✅ Diagram alur login CHAP Hotspot?
✅ Perbaikan security HTML login page?
✅ Template login page UNS siap pakai?
