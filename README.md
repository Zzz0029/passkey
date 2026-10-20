# QR Scanner in `about:blank` — Security PoC

Security research tool: **QR code → JavaScript executor** di dalam popup `about:blank`.

Tujuannya membuktikan bahwa **permission chooser browser** (Web Bluetooth, WebUSB, Web Serial, WebHID, WebAuthn Passkey, File System Access, MIDI, dll.) yang dipanggil dari popup `about:blank` **tidak menampilkan origin peminta akses** — sebagai gantinya menampilkan `about:blank`.

## Live Demo

`https://<your-project>.vercel.app`

## Cara Pakai

1. Klik **1. Grant Camera Permission** → izinkan kamera.
2. Klik **2. Open QR Scanner** → popup `about:blank` terbuka.
   - Kalau popup diblokir, klik ikon 🚫 di address bar → *Always allow popups* → ulangi.
   - Atau scanner otomatis pindah ke **iframe fallback** di bawah.
3. Scan QR (kamera / upload foto / take photo).
4. Klik **Execute** → kode berjalan di popup.
5. Kalau kode memanggil `navigator.bluetooth.requestDevice(...)` dkk., permission dialog akan menampilkan **`about:blank`** (bukan origin) → bug terbukti.

## Generate QR

```bash
sudo apt install qrencode

# Test verifikasi konteks
qrencode -o test.png "alert(location.href + ' | ' + location.origin)"

# Web Bluetooth
qrencode -o bt.png "navigator.bluetooth.requestDevice({acceptAllDevices:true})"

# WebUSB
qrencode -o usb.png "navigator.usb.requestDevice({filters:[]})"

# Passkey
qrencode -o passkey.png "navigator.credentials.create({publicKey:{challenge:crypto.getRandomValues(new Uint8Array(32)),rp:{name:'PoC'},user:{id:crypto.getRandomValues(new Uint8Array(16)),name:'a@b.c',displayName:'A'},pubKeyCredParams:[{type:'public-key',alg:-7}]}})"
