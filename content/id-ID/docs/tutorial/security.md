# Keamanan, kemampuan asli, dan tanggung jawab Anda

Sebagai web developer, kita biasanya menikmati keamanan yang kuat bersih browser - risiko yang terkait dengan kode kita menulis relatif kecil. website kami diberikan kekuasaan terbatas di bak pasir, dan kami percaya bahwa pengguna kami menikmati browser dibangun oleh tim besar insinyur yang mampu cepat merespon ancaman keamanan yang baru ditemukan.

Ketika bekerja dengan elektron, penting untuk memahami bahwa elektron tidak web browser. Hal ini memungkinkan Anda untuk membangun aplikasi desktop kaya fitur dengan teknologi web yang akrab, tapi kode wields banyak kekuatan yang lebih besar. JavaScript dapat mengakses filesystem, user shell, dan banyak lagi. Hal ini memungkinkan Anda untuk membangun aplikasi asli berkualitas tinggi, tetapi risiko keamanan yang melekat skala dengan kekuatan tambahan yang diberikan kepada kode Anda.

Dengan itu dalam pikiran, menyadari bahwa menampilkan konten yang sewenang-wenang dari sumber untrusted pose risiko keamanan parah elektron yang tidak dimaksudkan untuk menangani. Pada kenyataannya, aplikasi elektron yang paling populer (Atom, kendur, Visual Studio kode, dll) menampilkan terutama konten lokal (atau konten terpencil yang terpercaya, aman tanpa integrasi Node) – jika aplikasi Anda mengeksekusi kode dari sumber online, itu adalah tanggung jawab Anda untuk Pastikan bahwa kode ini tidak berbahaya.

## Melaporkan Issue Baru

Untuk informasi tentang cara untuk benar mengungkapkan kerentanan elektron, lihat [SECURITY.md](https://github.com/electron/electron/tree/master/SECURITY.md)

## Masalah keamanan Kromium dan upgrade

Sementara elektron berusaha untuk mendukung versi baru dari kromium sesegera mungkin, pengembang harus menyadari bahwa upgrade adalah usaha yang serius - melibatkan pengeditan lusinan atau bahkan ratusan file. Mengingat sumber daya dan kontribusi yang tersedia saat ini, elektron akan sering tidak pada versi yang sangat terbaru dari kromium, tertinggal oleh hari atau minggu.

Kami merasa bahwa sistem kami saat memperbarui Chromium komponen menyerang keseimbangan yang tepat antara sumber daya yang kita miliki dan kebutuhan mayoritas aplikasi yang dibangun di atas kerangka. Kami benar-benar tertarik untuk mendengar lebih lanjut tentang kasus-kasus penggunaan tertentu dari orang-orang yang membangun hal-hal di atas elektron. Permintaan tarik dan kontribusi yang mendukung upaya ini yang selalu sangat welcome.

## Mengabaikan di atas saran

Masalah keamanan ada setiap kali Anda menerima kode dari remote tujuan dan menjalankannya secara lokal. Sebagai contoh, mempertimbangkan sebuah situs terpencil yang ditampilkan di dalam [`BrowserWindow`](../api/browser-window.md). Jika penyerang entah bagaimana berhasil mengubah konten kata (baik dengan menyerang sumber langsung, atau dengan duduk di antara aplikasi dan tujuan yang sebenarnya), mereka akan dapat mengeksekusi kode asli pada mesin pengguna.

> : peringatan: Dalam situasi yang harus Anda memuat dan mengeksekusi kode jauh dengan Node integrasi diaktifkan. Sebaliknya, gunakan hanya lokal file (dikemas bersama-sama dengan aplikasi Anda) untuk mengeksekusi Node kode. Untuk menampilkan konten jauh, gunakan [tampilan web` tag dan pastikan untuk menonaktifkan `nodeIntegrasi](../api/web-view).

## Peringatan Keamanan Elektronika

Dari Electron 2.0, pengembang akan melihat peringatan dan rekomendasi yang dicetak ke konsol pengembang. Mereka hanya menunjukkan op ketika nama binernya adalah Electron, menunjukkan bahwa pengembang saat ini melihat konsol.

Anda dapat memaksa atau menonaktifkan peringatan ini dengan menyetel ` ELECTRON_ENABLE_SECURITY_WARNINGS </ code> atau <code> ELECTRON_DISABLE_SECURITY_WARNINGS </ code> pada
objek <code> process.env </ code> atau <code>> </ code>.</p>

<h2>Daftar Periksa: Rekomendasi keamanan</h2>

<p>Ini bukan antipeluru, tapi paling tidak, Anda harus mengikuti langkah-langkah ini
tingkatkan keamanan aplikasi Anda.</p>

<p>1) <a href="#only-load-secure-content"> Isi muatan aman saja </a>
2) <a href="#disable-node.js-integration-for-remote-content"> Nonaktifkan integrasi Node.js di semua penyaji yang menampilkan konten jauh </a>
3) <a href="#enable-context-isolation-for-remote-content"> Aktifkan isolasi konteks di semua penyaji yang menampilkan konten jauh </a>
4) <a href="#handle-session-permission-requests-from-remote-content"> Gunakan <code> ses.setPermissionRequestHandler () </ code> di semua sesi yang memuat konten jarak jauh </a>
5) <a href="#do-not-disable-websecurity"> Jangan menonaktifkan <code> webSecurity </ code> </a>
6) <a href="#define-a-content-security-policy"> Tentukan <code> Content-Security-Policy </ code> </a>
  dan gunakan aturan pembatasan (yaitu <code> script-src 'self' </ code>)
7) <a href="#override-and-disable-eval"> Mengabaikan dan menonaktifkan <code> eval </ code> </a>
, yang memungkinkan string untuk dieksekusi sebagai kode.
8) <a href="#do-not-set-allowRunningInsecureContent-to-true"> Jangan set <code> allowRunningInsecureContent </ code> ke <code> true </ code> </a>
9) <a href="#do-not-enable-experimental-features"> Jangan aktifkan fitur eksperimental </a>
10) <a href="#do-not-use-blinkfeatures"> Jangan gunakan <code> blinkFeatures </ code> </a>
11) <a href="#do-not-use-allowpopups"> WebViews: Jangan gunakan <code> allowpopups </ code> </a>
12) <a href="#verify-webview-options-before-creation"> WebViews: Verifikasi opsi dan params dari semua tag <code> & lt; webview & gt; </ code> </a></p>

<h2>1) Hanya memuat konten aman</h2>

<p>Setiap sumber daya yang tidak disertakan dengan aplikasi anda harus dimuat dengan menggunakan protokol yang aman seperti <code>HTTPS`. Dengan kata lain, jangan gunakan tidak aman protokol seperti `HTTP`. Similarly, we recommend the use of `WSS` over `WS`, `FTPS` over `FTP`, and so on.

### Mengapa?

`HTTPS` memiliki tiga manfaat utama:

1) Mengotentikasi server terpencil, membuat aplikasi anda terhubung dengan benar Ke host bukan peniru. 2) Memastikan integritas data, menyatakan bahwa data tidak diubah saat di transit antara aplikasi anda dan host. 3) Mengenkripsi lalu lintas antara pengguna dan tujuan host, sehingga lebih sulit untuk menyadap informasi yang dikirim antara ponsel anda dengan aplikasi dan host.

### Bagaimana?

```js
Buruk browserWindow.loadURL ('http://my-website.com') / / baik browserWindow.loadURL ('https://my-website.com')
```

```html
<!--buruk--> <script crossorigin src="http://cdn.com/react.js"></script> <link rel="stylesheet" href="http://cdn.com/style.css"><!--baik--> <script crossorigin src="https://cdn.com/react.js"></script> <link rel="stylesheet" href="https://cdn.com/style.css">
```

## 2) Nonaktifkan Integrasi Node.js untuk Konten Jarak Jauh

Yang terpenting adalah Anda menonaktifkan integrasi Node.js di renderer manapun ( `` BrowserWindow </ code> </a>, <a href="../api/browser-view.md"> <code > BrowserView </ code> </a>, atau
<a href="../api/web-view"> <code> WebView </ code> </a>) yang memuat konten jarak jauh. Tujuannya adalah untuk membatasi
kekuatan yang anda berikan untuk konten terpencil, sehingga membuatnya jauh lebih sulit bagi penyerang membahayakan pengguna harus memperoleh kemampuan mereka untuk menjalankan JavaScript pada situs web anda.</p>

<p>Setelah ini, anda dapat memberikan izin tambahan untuk host tertentu. Misalnya, jika anda membuka BrowserWindow menunjuk pada`https://my-website.com/", anda dapat memberikan situs web yang tepat dengan kemampuan yang dibutuhkan, tapi tidak lebih.</p>

<h3>Mengapa?</h3>

<p>Cross-site scripting (XSS) serangan yang lebih berbahaya jika seorang penyerang dapat melompat keluar dari proses penyaji dan mengeksekusi kode pada komputer pengguna.
Cross-site scripting serangan yang cukup umum - dan masalah sementara, kekuatan mereka biasanya terbatas untuk bermain-main dengan situs web yang mereka jalankan.
Menonaktifkan integrasi Node.js membantu mencegah XSS dari yang meningkat menjadi serangan "Eksekusi Kode Jarak Jauh "(RCE).</p>

<h3>Bagaimana?</h3>

<pre><code class="js">// Buruk
const mainWindow = baru BrowserWindow()
mainWindow.loadURL('https://my-website.com')
``</pre> 

```js
// Bagus
const mainWindow = baru BrowserWindow({
  webPreferences: {
    nodeIntegration: false,
    preload: './preload.js'
  }
})

mainWindow.loadURL('https://my-website.com')
```

```html
<!--buruk--> <webview nodeIntegration src="page.html"></webview> <!--baik--> <webview src="page.html"></webview>
```

Ketika menonaktifkan integrasi Node.js,anda masih dapat mengekspos Api untuk situs web anda yang mengkonsumsi modul atau fitur Node.js. Script pramuat terus memiliki akses untuk `meminta` dan fitur Node.js lainnya, yang memungkinkan pengembang untuk mengekspos custom API untuk memuat konten yang terpencil.

Dalam contoh berikut script preload, kemudian memuat situs web akan memiliki akses untuk `jendela.readConfig()` metode, tetapi tidak ada fitur Node.js.

```js
const { bacaFileSync } = wajib('fs')

window.bacaConfig = fungsi () {
  const data = bacaFileSync('./config.json')
  kembali data
}
```

## 3) Aktifkan Isolasi Konteks untuk Konten Jarak Jauh

Konteks isolasi adalah fitur Electron yang memungkinkan pengembang untuk menjalankan kode di script preload dan API Electron dalam konteks JavaScript yang berdedikasi. Di praktek itu berarti itu global benda seperti `Array.prototype.push` atau`JSON.parse` tidak dapat dimodifikasi oleh skrip berjalan dalam renderer proses.

Elektron menggunakan sama teknologi seperti Chromium [Content Scripts](https://developer.chrome.com/extensions/content_scripts#execution-environment) untuk mengaktifkan perilaku ini.

### Mengapa?

Konteks isolasi memungkinkan setiap script yang berjalan di penyaji untuk membuat perubahan lingkungan JavaScript tanpa khawatir tentang yang bertentangan dengan script API Electron atau script preload.

Sementara masih eksperimental fitur Elektron, konteks isolasi menambahkan lapisan kemanan tambahan. Ini menciptakan dunia JavaScript baru untuk API Electron dan script preload.

Pada waktu yang sama, script preload masih memiliki akses ke `dokumen` dan `jendela` objek. Dengan kata lain, anda mendapatkan pengembalian yang layak atas kemungkinan investasi yang sangat kecil.

### Bagaimana?

```js
// Main proses
const mainWindow = baru BrowserWindow({
  webPreferensi: {
    kontextIsolation: benar,
    pramuat: 'pramuat.js'
  }
})
```

```js
// Pramuat script

// Tetapkan variabel di halaman sebelum dimuat
webFrame.executeJavaScript('window.foo = "foo";')

// Yang dimuat halaman tidak akan bisa untuk mengakses ini, itu hanya tersedia
// di dalam konteks
window.bar = 'bar'

document.addEventListener('DOMContentLoaded', () => {
  // Akan log out 'bar' sejak window.bar tersedia dalam konteks ini
  console.log(window.foo)


  // 
  console.log(window.bar)
})
```

## 4) Menangani permintaan izin sesi dari konten jauh

Anda mungkin telah melihat permintaan izin saat menggunakan Chrome: Mereka muncul setiap kali situs web mencoba untuk menggunakan fitur yang harus disetujui pengguna secara manual (seperti pemberitahuan).

API didasarkan pada [perizinan API Chromium](https://developer.chrome.com/extensions/permissions) dan menerapkan jenis yang sama dari perizinan.

### Mengapa?

Secara default, Electron akan secara otomatis menyetujui semua permintaan izin kecuali pengembang telah secara manual mengkonfigurasi penangan kustom. Sementara padat default, pengembang yang sadar akan keamanan mungkin ingin menganggap sangat berlawanan.

### Bagaimana?

```js
const { session } = require('electron')

session
  .fromPartition('some-partition')
  .setPermissionRequestHandler((webContents, permission, callback) => {
    const url = webContents.getURL()

    if (permission === 'notifications') {
      // Approves the permissions request
      callback(true)
    }

    if (!url.startsWith('https://my-website.com')) {
      // Denies the permissions request
      return callback(false)
    }
  })
```

## 5) Jangan Nonaktifkan Keamanan Web

*Rekomendasi adalah elektron 's default*

Anda mungkin sudah menduga bahwa menonaktifkan property ` webSecurity </ code> pada a proses renderer (<a href="../api/browser-window.md"> <code> BrowserWindow </ code> </a>,<a href="../api/browser-view.md"> <code> BrowserView </ code> </a>, atau <a href="../api/web-view"> <code> WebView </ code> </a>) menonaktifkan penting
fitur keamanan.</p>

<p>Tidak menonaktifkan <code>webSecurity` dalam aplikasi produksi.

### Mengapa?

Menonaktifkan `webSecurity` akan menonaktifkan kebijakan asal yang sama dan mengatur properti `allowRunningInsecureContent` ke `true`. Dengan kata lain, hal ini memungkinkan eksekusi kode yang tidak aman dari domain yang berbeda.

### Bagaimana?

```js
Buruk const mainWindow = BrowserWindow baru ({webPreferences: {
    webSecurity: false
  }})
```

```js
// Good
const mainWindow = new BrowserWindow()
```

```html
<!--buruk--> <webview disablewebsecurity src="page.html"></webview> <!--baik--> <webview src="page.html"></webview>
```

## 6) Tentukan Kebijakan Keamanan Konten

Kebijakan kemanan konten (CSP) adalah lapisan perlindungan tambahan terhadap serangan cross-site scripting dan serangan injeksi data. Kami merekomendasikan bahwa mereka dapat diaktifkan oleh setiap situs web yang anda muat dalam Electron.

### Mengapa?

CSP memungkinkan server yang menyajikan konten untuk membatasi dan mengontrol sumber daya Electron dapat dimuat untuk halaman web yang diberikan. `https://your-page.com` should be allowed to load scripts from the origins you defined while scripts from `https://evil.attacker.com` should not be allowed to run. Defining a CSP is an easy way to improve your applications security.

### Bagaimana?

Electron respects [the `Content-Security-Policy` HTTP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) and the respective `<meta>` tag.

CSP berikut akan memungkinkan Electron untuk mengeksekusi script dari situs web saat ini dan dari `apis.mydomain.com`.

```txt
// Bad
Content-Security-Policy: '*'

// Good
Content-Security-Policy: script-src 'self' https://apis.mydomain.com
```

## 7) Ganti dan Nonaktifkan `eval`

`eval()` adalah metode JavaScript inti yang memungkinkan eksekusi JavaScript dari sebuah string. Menonaktifkan kemampuan aplikasi anda untuk mengevaluasi JavaScript yang tidak diketahui sebelumnya.

### Mengapa?

`Metode()` eval memiliki satu misi tepat: Untuk mengevaluasi serangkaian karakter seperti JavaScript dan menjalankannya. Ini adalah metode yang diperlukan setiap kali anda perlu mengevaluasi kode yang tidak dikenal sebelumnya. Sementara penggunaan yang sah, sama seperti kasus generator kode yang lain `eval()` adalah sulit untuk mengeras.

Secara umum, lebih mudah untuk benar-benar menonaktifkan `eval()` daripada membuat anti peluru itu. Dengan demikian, jika anda tidak membutuhkannya, itu adalah ide yang baik untuk menonaktifkannya.

### Bagaimana?

```js
// ESLint will warn about any use of eval(), even this one
// eslint-disable-next-line
window.eval = global.eval = function () {
  throw new Error(`Sorry, this app does not support window.eval().`)
}
```

## 8) Do Not Set `allowRunningInsecureContent` to `true`

*Recommendation is Electron's default*

By default, Electron will now allow websites loaded over `HTTPS` to load and execute scripts, CSS, or plugins from insecure sources (`HTTP`). Setting the property `allowRunningInsecureContent` to `true` disables that protection.

Loading the initial HTML of a website over `HTTPS` and attempting to load subsequent resources via `HTTP` is also known as "mixed content".

### Mengapa?

Simply put, loading content over `HTTPS` assures the authenticity and integrity of the loaded resources while encrypting the traffic itself. See the section on [only displaying secure content](#only-display-secure-content) for more details.

### Bagaimana?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    allowRunningInsecureContent: true
  }
})
```

```js
// Good
const mainWindow = new BrowserWindow({})
```

## 9) Tidak Mengaktifkan Fitur Eksperimental

*Recommendation is Electron's default*

Advanced users of Electron can enable experimental Chromium features using the `experimentalFeatures` and `experimentalCanvasFeatures` properties.

### Mengapa?

Experimental features are, as the name suggests, experimental and have not been enabled for all Chromium users. Furthermore, their impact on Electron as a whole has likely not been tested.

Ada kasus penggunaan yang sah, tapi kecuali anda tahu apa yang anda lakukan, sebaiknya anda tidak mengaktifkan properti ini.

### Bagaimana?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    experimentalFeatures: true
  }
})
```

```js
// Good
const mainWindow = new BrowserWindow({})
```

## 10) Jangan Gunakan ` blinkFeatures </ code></h2>

<p><em>Recommendation is Electron's default</em></p>

<p>Blink is the name of the rendering engine behind Chromium. Sebagai properti <code>blinkFeatures` dengan `experimentalFeatures`, memungkinkan pengembang untuk mengaktifkan fitur yang telah dinonaktifkan secara default.</p> 

### Mengapa?

Secara umum, ada kemungkinan alasan yang baik jika fitur ini tidak diaktifkan secara default. Legitimate use cases for enabling specific features exist. Sebagai pengembang, anda harus tahu persis mengapa anda perlu mengaktifkan fitur ini, apa percabangannya, dan bagaimana pengaruhnya terhadap keamanan dari aplikasi anda. Dalam keadaan apa pun harus Anda mengaktifkan fitur speculatively.

### Bagaimana?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    blinkFeatures: ['ExecCommandInJavaScript']
  }
})
```

```js
// Good
const mainWindow = new BrowserWindow()
```

## 11) Jangan Gunakan ` allowpopups </ code></h2>

<p><em>Recommendation is Electron's default</em></p>

<p>Jika Anda menggunakan <a href="web-view"><code>WebView`</a>, Anda mungkin perlu halaman dan script dimuat dalam tag `<webview>` untuk membuka jendela baru. Atribut `allowpopups` memungkinkan mereka untuk menciptakan baru [`BrowserWindows`](browser-window) menggunakan metode `window.open()`. `WebViews` sebaliknya tidak diperbolehkan untuk membuat jendela baru.</p> 

### Mengapa?

Jika Anda tidak perlu popup, Anda akan lebih baik tidak memungkinkan penciptaan baru [`BrowserWindows`](browser-window) secara default. Ini mengikuti prinsip dari jumlah minimal akses yang diperlukan: Jangan biarkan situs web membuat popup baru kecuali anda tahu kebutuhan fitur ini.

### Bagaimana?

```html
<webview allowpopups src="page.html"></webview> <webview src="page.html"></webview>
```

## 12) Verifikasi Pilihan WebView Sebelum Penciptaan

Tampilan Web yang dibuat dalam sebuah proses penyaji yang tidak memiliki integrasi Node.js diaktifkan tidak akan dapat mengaktifkan integrasi itu sendiri. Namun, WebView akan selalu membuat proses renderer independen dengan sendiri `webPreferences`.

Itu adalah ide yang baik untuk mengendalikan pembuatan baru [`TampilanWeb`](web-view) dari proses utama dan untuk memverifikasi bahwa web Preferensi mereka tidak menonaktifkan fitur keamanan.

### Mengapa?

Karena WebViews tinggal di DOM, mereka dapat dibuat oleh sebuah script yang menjalankan website Anda bahkan jika Node.js integrasi sebaliknya dinonaktifkan.

Elektron memungkinkan pengembang untuk menonaktifkan berbagai fitur keamanan kontrol proses renderer. Dalam kebanyakan kasus, pengembang tidak perlu menonaktifkan salah satu fitur tersebut - dan Anda harus karena itu tidak memungkinkan konfigurasi yang berbeda untuk [`<WebView>`](web-view)tag yang baru dibuat.

### Bagaimana?

Sebelum [`<WebView>`](web-view)tag terpasang, elektron akan api acara `akan-melampirkan-webview` pada hosting `webContents`. Menggunakan acara untuk mencegah pembentukan WebViews dengan pilihan mungkin tidak aman.

```js
app.on ( 'web-isi-dibuat', ( acara , isi) = & gt; {
   contents.on ( 'akan melampirkan tampilan web', ( acara , webPreferences, params) = & gt; {
     // Strip pergi script preload jika tidak digunakan atau memverifikasi lokasi mereka adalah sah
     webPreferences.preload delete
     hapus webPreferences.preloadURL

     // Disable simpul integrasi
     webPreferences.nodeIntegration = false

     // Verifikasi URL yang dimuat
     if (! params.src.startsWith ( 'https://yourapp.com/ ')) {
 event .preventDefault ()
 }
 })})            
```

Sekali lagi, daftar ini hanya meminimalkan risiko, tidak menghapusnya. Jika tujuan Anda adalah untuk menampilkan sebuah situs web, browser akan menjadi lebih aman pilihan .