# BEMS Monitor — Android App

Aplikasi Android untuk **Building Energy Monitoring System (BEMS)**, dibungkus dari dashboard web (`www/index.html`) menggunakan [Capacitor](https://capacitorjs.com/), sehingga bisa di-install sebagai APK di HP Android.

## Struktur Repository

```
bems-android-app/
├── www/                          # Web app (dashboard) — sumber tampilan
│   ├── index.html                # Dashboard BEMS (90 parameter, chart, export)
│   └── config/
│       └── android-params.json   # Konfigurasi & ambang batas khusus Android
├── android/                      # Project native Android (Capacitor)
│   └── app/src/main/
│       ├── AndroidManifest.xml   # Permission: INTERNET, NOTIFICATIONS, dst.
│       └── res/values/strings.xml# Nama app, package id
├── capacitor.config.json         # Konfigurasi Capacitor (appId, splash, statusbar)
├── package.json
├── .github/workflows/build-android.yml  # Auto-build APK debug via GitHub Actions
└── README.md
```

## Parameter Android (`www/config/android-params.json`)

File ini menerjemahkan 15 kategori & 90 parameter yang ada di dashboard (Voltage, Current, Active/Reactive/Apparent Power, Power Factor, Frequency, Energy, THD V/I, Unbalance, Demand, Min/Max, Ground/Neutral, Tariff) menjadi:

- **Ambang warning/critical** per kategori (contoh: tegangan ±5% warning / ±10% critical dari nominal 230V/400V; THD tegangan mengacu IEEE 519: 5% warning / 8% critical; power factor minimum 0.9 warning / 0.85 critical).
- **Interval refresh** data (default 3 detik) dan sinkronisasi background (15 menit).
- **Notification channel** Android (kritis, peringatan, info) untuk alarm parameter.
- **Permission** yang dipakai: `INTERNET`, `ACCESS_NETWORK_STATE`, `POST_NOTIFICATIONS`, `RECEIVE_BOOT_COMPLETED`.
- **minSdkVersion 23 / targetSdkVersion 34**, tema warna `#080B14` (sama dengan dashboard).

Silakan sesuaikan angka ambang batas ini dengan spesifikasi panel listrik/gedung Anda yang sebenarnya.

## Build APK

### Opsi A — Otomatis lewat GitHub Actions (tanpa install apa pun)
1. Push repo ini ke GitHub (lihat langkah di bawah).
2. Buka tab **Actions** di GitHub → workflow "Build Android APK" akan berjalan otomatis setiap push ke `main`.
3. Setelah selesai, download APK dari bagian **Artifacts** pada hasil run tersebut.
4. Salin APK ke HP, aktifkan "Install dari sumber tidak dikenal", lalu install.

### Opsi B — Build manual di komputer (perlu Android Studio / Android SDK + JDK 17)
```bash
git clone <url-repo-anda>
cd bems-android-app
npm install
npx cap sync android
cd android
./gradlew assembleDebug
# APK ada di: android/app/build/outputs/apk/debug/app-debug.apk
```

Atau buka folder `android/` langsung di **Android Studio** → Run ke HP/emulator.

## Update tampilan dashboard

Kalau dashboard (`www/index.html`) diubah, jalankan `npx cap sync android` supaya perubahan ikut masuk ke project Android sebelum build ulang.

## Cara push ke GitHub

```bash
cd bems-android-app
git init
git add .
git commit -m "Initial commit: BEMS Monitor Android app"
git branch -M main
git remote add origin https://github.com/<username-anda>/bems-android-app.git
git push -u origin main
```
