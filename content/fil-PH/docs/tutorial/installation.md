# Pag-install

> Mga tip para sa pag-install ng Electron

Upang mag-install ng prebuilt Electron binaries, gamitin ang [` npm `](https://docs.npmjs.com/). Ang ginustong paraan ay ang pag-install ng Electron bilang isang dependency sa pag-unlad sa iyong app:

```sh
npm instol elektron -- isave-dev
```

Tingnan ang [ Electron versioning doc ](electron-versioning.md) para sa impormasyon kung paano pamahalaan ang mga bersyon ng Electron sa iyong mga app.

## Global Installation

Maaari mo ring i-install ang utos ng ` electron ` globally sa iyong ` $PATH `:

```sh
npm install electron -g
```

## Pag-customize

Kung gusto mong baguhin ang arkitektura na na-download (hal., ` ia32 ` sa isang ` x64 ` machine), maaari mong gamitin ang flag ng ` --arko ` sa pag-install ng npm o itakda ang Variable na ` npm_config_arch `:

```shell
npm install --arch=ia32 electron
```

Bilang karagdagan sa pagpapalit ng arkitektura, maaari mo ring tukuyin ang platform (hal., ` win32 `, ` linux `, atbp.) gamit ang flag ng `--platform `:

```shell
npm install --platform=win32 electron
```

## Proxies

Kung kailangan mong gumamit ng HTTP proxy maaari mong [ itakda ang mga variable ng kapaligiran ](https://github.com/request/request/tree/f0c4ec061141051988d1216c24936ad2e7d5c45d#controlling-proxy-behaviour-using-environment-variables).

## Custom Mirrors and Caches

Sa panahon ng pag-install, ang module ng ` electron ` ay tatawag sa [`electron-download`](https://github.com/electron-userland/electron-download) upang i-download ang prebuilt binaries ng Electron para sa iyong platform. Magagawa ito sa pamamagitan ng pakikipag-ugnay sa GitHub release ang pahina ng pag-download (` https://github.com/electron/electron/releases/tag/v$VERSION `, kung saan ` $VERSION ` ay ang eksaktong bersyon ng Electron).

Kung hindi mo ma-access ang GitHub o kailangan mong magbigay ng custom build, ay maaaring gawin mo ito sa alinman sa pagbibigay ng salamin o isang umiiral na direktoryo ng cache.

#### Mirror

Maaari mong gamitin ang mga variable ng kapaligiran upang i-override ang base URL, ang landas kung saan hahanapin ang mga binary ng Electron, at ang binary filename. Ang url na ginamit ng ` electron-download ` ay binubuo ng mga sumusunod:

```txt
url = ELECTRON_MIRROR + ELECTRON_CUSTOM_DIR + '/' + ELECTRON_CUSTOM_FILENAME
```

Halimbawa, gamitin ang mirror ng China:

```txt
ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/"
```

#### Cache

Bilang kahalili, maaari mong i-override ang cache ng lokal. Ang ` electron-download ` ay mag-cache download ng mga binary sa isang lokal na direktoryo upang hindi ma-stress ang iyong network. Pwede mong gamitin ang folder ng cache upang magbigay ng custom builds ng Electron o upang maiwasan ang paggawa ng contact kasama ang network sa lahat.

* Linux: `$XDG_CACHE_HOME` or `~/.cache/electron/`
* MacOS: `~/Library/Caches/electron/`
* Windows: `$LOCALAPPDATA/electron/Cache` or `~/AppData/Local/electron/Cache/`

Sa mga kapaligiran na gumagamit ng mga mas lumang bersyon ng Electron, maaari mong makita ang cache din sa ` ~/.electron `.

Maaari mo ring i-override ang lokal na lokasyon ng cache sa pamamagitan ng pagbibigay ng ` ELECTRON_CACHE `.

Ang cache ay naglalaman ng opisyal na zip file ng bersyon pati na rin ang checksum, na naka-imbak bilang isang file ng teksto. Maaaring ganito ang isang karaniwang cache:

```sh
├── electron-v1.7.9-darwin-x64.zip
├── electron-v1.8.1-darwin-x64.zip
├── electron-v1.8.2-beta.1-darwin-x64.zip
├── electron-v1.8.2-beta.2-darwin-x64.zip
├── electron-v1.8.2-beta.3-darwin-x64.zip
├── SHASUMS256.txt-1.7.9
├── SHASUMS256.txt-1.8.1
├── SHASUMS256.txt-1.8.2-beta.1
├── SHASUMS256.txt-1.8.2-beta.2
├── SHASUMS256.txt-1.8.2-beta.3
```

## Paghahanap ng Problema

Habang pinatatakbo and `npm install electron`, ang ibang user ay kadalasang nakakasalubong ng error sa pag-install.

Sa maraming pagkakataon, ang mga problemang ito ay resulta ng problema sa network at hindit talaga aktwal na isyu ng `electron` npm package. Ang problema gaya ng `ELIFECYCLE`,`EAI_AGAIN`, `ECONNRESET`, and `ETIMEDOUT` ay mga indikasyon na mayroon problema sa iyong network. Ang pinaka mainam na solusyon ay subukang pagpalitin ang mga network o hindi kaya ay maghintay ng kaunti at subukang iinstall muli.

Maari mo ring subukan i download ang Electron ng direkta mula sa [electron/electron/releases](https://github.com/electron/electron/releases)kung ang pag-iinstall sa pamamagitan ng `npm` ay hindi nagtatagumpay.

Kung nabigo ang pag-install sa isang error na ` EACCESS ` maaaring kailanganin mo [fix your npm permissions](https://docs.npmjs.com/getting-started/fixing-npm-permissions).

Kung nagpatuloy ang error sa itaas, ang flag ng [unsafe-perm](https://docs.npmjs.com/misc/config#unsafe-perm) ay maaaring itakda sa totoo:

```sh
sudo npm install electron --unsafe-perm=true
```

Sa mas mabagal na mga network, maaring gamitin ang bandang `--verbose` upang maipakita ang progreso ng pag-download:

```sh
npm install --verbose electron
```

Kung kailangan mo upang pilitin ang muling pag-download ng asset at ang SHASUM na file ay magtakda ng ` force_no_cache ` variable na kapaligiran sa ` true`.