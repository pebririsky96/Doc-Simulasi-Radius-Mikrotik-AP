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

<body>

<!-- Jika menggunakan CHAP -->
$(if chap-id)
<form name="sendin" action="$(link-login-only)" method="post" style="display:none">
    <input type="hidden" name="username" />
    <input type="hidden" name="password" />
    <input type="hidden" name="dst" value="$(link-orig)" />
    <input type="hidden" name="popup" value="true" />
</form>

<script src="/md5.js"></script>
<script>
function doLogin() {
    // ambil username dari form utama
    document.sendin.username.value = document.login.username.value;
    // password dikunci (optional: bisa diisi macauth atau kosong)
    var pass = "macauth";
    document.sendin.password.value = hexMD5('$(chap-id)' + pass + '$(chap-challenge)');
    document.sendin.submit();
    return false;
}
</script>
$(endif)

<div class="ie-fixMinHeight">
    <div class="main">
        <div class="wrap animated fadeIn">
            <form name="login" action="$(link-login-only)" method="post" $(if chap-id) onSubmit="return doLogin()" $(endif)>
                <input type="hidden" name="dst" value="$(link-orig)" />
                <input type="hidden" name="popup" value="true" />

                <!-- Ganti logo UNS -->
                <div style="text-align:center; margin-bottom:20px;">
                    <img src=""
                         alt=""
                         style="width:120px; height:auto;">
                </div>

                <p class="info $(if error)alert$(endif)">
                    $(if error == "")
                        Silakan masukkan MAC Address kemudian akan diverifikasi otomatis oleh sistem.
                    $(endif)
                    $(if error)$(error)$(endif)
                </p>

                <!-- hanya username -->
                <label>
                    <img class="ico" src="img/user.svg" alt="#" />
                    <input name="username" type="text" value="$(username)" placeholder="Username / MAC Address" required />
                </label>

                <!-- sembunyikan password -->
                <input type="hidden" name="password" value="macauth" />

                <input type="submit" value="Connect" />
            </form>
        </div>
    </div>
</div>

</body>
</html>
