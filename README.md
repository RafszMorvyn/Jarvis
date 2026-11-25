# Jarvis Android — asisten suara yang mengontrol HP

Alurnya: **suara → Claude (tool use) → aksi di HP → baca layar → ... → jawaban suara**

- `VoiceEngine` — mendengar terus-menerus (SpeechRecognizer, bahasa Indonesia) dan membalas lewat TTS. Mic dijeda saat Jarvis bicara.
- `Agent` + `ClaudeClient` — loop agen ke Anthropic Messages API (model default `claude-sonnet-5-5`, bisa diganti di `Prefs.DEFAULT_MODEL`).
- `JarvisAccessibilityService` — "tangan" Jarvis: baca layar, ketuk, ketik, scroll, swipe, tombol back/home/recents, buka app.
- `JarvisService` — foreground service (mikrofon), wake word "Jarvis", perintah "stop/batal".

## Cara pakai

1. Buka folder ini di **Android Studio** (Ladybug atau lebih baru), tunggu Gradle sync.
   Kalau diminta, biarkan Studio membuat Gradle wrapper (versi 8.9).
2. Run ke HP (USB debugging) atau build APK: *Build > Build APK(s)*.
3. Di app: isi **API key Anthropic** → Simpan → **izinkan mikrofon** → **aktifkan Accessibility Service** → **Mulai Jarvis**.
4. Katakan: *"Jarvis, buka WhatsApp dan cari chat Budi"*.

### Android 13+ (APK di-install manual)
Accessibility bisa tampil abu-abu. Buka *Info aplikasi Jarvis > menu ⋮ > Izinkan pengaturan terbatas*, lalu ulangi langkah aktifkan Accessibility.

## Keamanan
- API key disimpan terenkripsi (EncryptedSharedPreferences), tapi tetap ada di HP. Untuk dibagikan ke orang lain, jangan taruh key di app; pakai server proxy sendiri.
- Tindakan sensitif (kirim pesan, bayar, hapus, posting, dll) diminta konfirmasi suara lewat tool `ask_user`.
- Kolom password tidak dibaca dan tidak diketik. Teks di layar diperlakukan sebagai data, bukan perintah.
- Ucapkan "stop" / "batal" saat Jarvis sedang bekerja untuk membatalkan.
- Isi layar (teks yang terlihat) dikirim ke API Anthropic agar Claude bisa memutuskan langkah berikutnya. Jangan jalankan di layar yang berisi data yang tidak boleh keluar.

## Batasan yang perlu diketahui
- Aplikasi dengan `FLAG_SECURE` (banyak app bank, beberapa app OTP) memblokir pembacaan layar.
- "Live voice" di sini = speech-to-text bergilir + TTS, bukan streaming audio dua arah real-time. Pengenalan suara `id-ID` biasanya butuh internet, dan beberapa HP berbunyi "bip" tiap siklus mendengar.
- Pembacaan layar mengandalkan accessibility tree; elemen yang tidak punya teks/label (game, canvas, beberapa Flutter app) lebih sulit. Ada fallback `tap_xy` / `swipe` berbasis koordinat.
- Distribusi lewat Play Store butuh justifikasi kebijakan Accessibility API. Untuk pemakaian pribadi (sideload) tidak masalah.
- Kode ini belum pernah dikompilasi atau diuji di perangkat (tidak ada Android SDK di lingkungan pembuatnya). Kalau ada error build, kirim pesan error-nya dan langsung diperbaiki.

## Ide pengembangan
Wake word offline (Porcupine), TTS/STT streaming yang lebih natural, tool tambahan (kirim intent langsung untuk telepon/SMS/alarm, baca notifikasi), overlay status mengambang.
