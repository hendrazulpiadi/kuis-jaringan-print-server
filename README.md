# Kuis Interaktif: Konfigurasi Print Server & Jaringan Dasar

Aplikasi kuis daring untuk siswa SMK Teknik Komputer & Jaringan. Materi: pengujian
koneksi (ping), Network ID & subnet mask, default gateway, perhitungan subnetting,
persyaratan print server, analisis pesan error, dan troubleshooting hardware/software.

## Menjalankan

Tidak ada build step dan tidak perlu server. Cukup buka `aplikasi_kuis_jaringan_print_server.html`
di browser (Chrome/Edge/Firefox).

- **Lokal** — klik ganda file HTML-nya.
- **Lewat server lokal** (disarankan agar `requestFullscreen` tidak diblokir kebijakan `file://`):

  ```bash
  npx serve .
  ```

## Fitur

- 7 soal pilihan ganda, masing-masing 4 opsi, satu jawaban benar.
- Timer 15 menit dengan auto-submit.
- Navigasi mundur/maju, tombol "Selanjutnya" terkunci sampai pertanyaan dijawab.
- Layar identitas (nama + kelas) sebelum kuis.
- Ringkasan hasil + tombol cetak (`window.print()`) untuk simpan bukti.
- Auto-lock layar (fullscreen) saat kuis dimulai.
- **Penyimpanan hasil otomatis** ke `localStorage` + tombol *Unduh Hasil (JSON)*
  berisi jawaban, kunci, skor, durasi, pelanggaran, dan alasan berakhirnya kuis.
- **Batas percobaan** (default 3×) per browser. Percobaan dicatat **saat kuis dimulai**,
  jadi menutup halaman di tengah kuis tidak memberi percobaan tambahan.

## Sistem Anti-Cheat

Maksimal 3 pelanggaran sebelum kuis disubmit paksa.

| Peristiwa | Perubahan |
| --- | --- |
| `visibilitychange` → `document.hidden` | pelanggaran + modal peringatan |
| `blur` saat tab masih terlihat | diabaikan (klik address bar/devtools bukan pelanggaran) |
| Keluar dari layar penuh | tidak dipantau |

Pelanggaran di-debounce 1500 ms dan hanya dihitung saat tab benar-benar tidak
terlihat, sehingga satu kali pindah tab = satu pelanggaran.

## Struktur

```
aplikasi_kuis_jaringan_print_server.html   # seluruh aplikasi (HTML + CSS + JS)
```

- **CSS** — Tailwind CSS via CDN, dark theme.
- **Bank soal** — `quizData`, array of object: `title`, `question`, `options`, `correct`.
- **State** — `currentQuestionIndex`, `userAnswers`, `studentInfo`, `violationCount`,
  `timeLeft`/`quizDeadline`.
- **Teks soal** mendukung markdown sederhana (`**tebal**`, `*miring*`, `` `kode` ``)
  lewat `formatInline()`, yang juga melakukan escape HTML.

## Menambah / Mengubah Soal

Edit array `quizData`. `correct` adalah **index** opsi yang benar (dimulai dari 0).

```js
{
    title: "Topik: ...",
    question: "Pertanyaan dengan **penekanan** dan `perintah`",
    options: ["opsi A", "opsi B", "opsi C", "opsi D"],
    correct: 0
}
```

Label "Pertanyaan N dari M" di header ikut menyesuaikan otomatis dari `quizData.length`.

## Catatan

- Skor = `benar / jumlah soal * 100`. KKM 75.
- Tombol *Unduh Hasil (JSON)* mengekspor seluruh rekaman percobaan (dalam satu
  berkas JSON) untuk dikirim ke guru.
- **Penyimpanan lokal bukan keamanan.** Siswa tetap bisa menghapus data browser.
  Untuk integritas penilaian sejati dibutuhkan backend (server + basis data).
- **`file://` berbahaya bagi pemakaian bersama** — semua berkas lokal berbagi satu
  origin `localStorage`, jadi percobaan & hasil antar siswa di satu PC bisa tertukar.
  Jalankan via `npx serve .` saat ujian berlangsung. Peringatan ini juga tampil
  otomatis di halaman saat dibuka lewat `file://`.
- **Reset data (guru):** buka DevTools → tab Console, jalankan `resetQuizData()`.
  Fungsi ini menghapus kunci `tkj_printserver_hasil_v1` dan
  `tkj_printserver_percobaan_v1` di localStorage.

## Lisensi

Dibuat untuk keperluan pembelajaran SMK Teknik Komputer & Jaringan.
