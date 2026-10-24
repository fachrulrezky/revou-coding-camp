# Implementation Plan: Cat Clicker App

## Overview

Implementasi aplikasi web satu halaman menggunakan HTML5, Tailwind CSS (CDN), dan Vanilla JavaScript murni. Aplikasi dibangun secara inkremental: struktur HTML → modul JS inti (AppState, ClickCounter, ReactionSystem) → animasi dan efek visual (AnimationEngine, FloatingText, ParticleSystem) → milestone dan perayaan → aksesibilitas dan responsivitas → pengujian.

## Tasks

- [-] 1. Set up struktur proyek dan file HTML dasar
  - Buat `index.html` dengan boilerplate HTML5
  - Tambahkan Tailwind CSS via CDN `<script src="https://cdn.tailwindcss.com">`
  - Buat `style.css` untuk CSS `@keyframes` animasi reaksi dan efek visual
  - Buat `app.js` sebagai entry point JavaScript
  - Susun struktur DOM: container utama, Cat_Sprite (elemen `<div>` atau `<button>`), area counter, layer floating text, dan layer overlay milestone
  - Tambahkan atribut `aria-label`, `tabindex="0"`, dan `role="button"` pada Cat_Sprite
  - _Requirements: 1.1, 1.2, 1.3, 6.3, 7.4_

- [ ] 2. Implementasi AppState dan ClickCounter
  - [~] 2.1 Implementasi objek AppState
    - Definisikan objek `AppState` dengan properti `totalClicks`, `currentReaction`, `isAnimating`, dan `milestonesReached` (Set)
    - Implementasikan fungsi `loadMilestonesFromStorage()` untuk memuat data dari `sessionStorage` saat halaman dimuat
    - Implementasikan fungsi `saveMilestones(milestones)` dengan try/catch untuk menulis ke `sessionStorage`
    - _Requirements: 2.4, 5.5_

  - [~] 2.2 Implementasi ClickCounter
    - Implementasikan `ClickCounter.increment()` yang menambah `AppState.totalClicks` sebesar 1 dan mengembalikan nilai baru
    - Implementasikan `ClickCounter.getValue()` yang mengembalikan `AppState.totalClicks`
    - Implementasikan `ClickCounter.updateDisplay(value)` yang memperbarui elemen DOM counter
    - _Requirements: 2.1, 2.2_

  - [ ]* 2.3 Tulis property test untuk ClickCounter
    - **Property 1: Counter increment invariant**
    - **Validates: Requirements 2.1**

  - [ ]* 2.4 Tulis unit test untuk AppState dan ClickCounter
    - Test: `increment()` dari 0 → 1
    - Test: `increment()` dari 99 → 100
    - Test: `loadMilestonesFromStorage()` mengembalikan Set kosong jika sessionStorage kosong
    - _Requirements: 2.1, 2.4, 5.5_

- [ ] 3. Implementasi ReactionSystem
  - [~] 3.1 Definisikan Reaction Pool
    - Buat array `reactionPool` berisi minimal 6 objek Reaction (surprised, happy, sleepy, angry, in-love, playful) sesuai tabel di design
    - Setiap objek memiliki properti: `id`, `label`, `cssClass`, `emoji`, `duration`
    - _Requirements: 3.1, 3.2_

  - [~] 3.2 Implementasi `ReactionSystem.pick()`
    - Implementasikan logika pemilihan acak yang mengecualikan `lastReaction` sebelum memilih
    - Update `ReactionSystem.lastReaction` setelah setiap pemilihan
    - _Requirements: 3.1, 3.6_

  - [ ]* 3.3 Tulis property test untuk ReactionSystem
    - **Property 4: Reaksi tidak berulang dua kali berturut-turut**
    - **Validates: Requirements 3.6**

  - [ ]* 3.4 Tulis property test untuk keanggotaan pool
    - **Property 5: Reaksi yang dipilih selalu berasal dari Reaction_Pool**
    - **Validates: Requirements 3.1**

  - [ ]* 3.5 Tulis unit test untuk ReactionSystem
    - Test: `pick()` tidak mengembalikan `lastReaction`
    - Test: pool memiliki minimal 5 item
    - _Requirements: 3.1, 3.2, 3.6_

- [~] 4. Checkpoint — Pastikan semua test pada fase sebelumnya lulus
  - Pastikan semua test lulus, tanyakan kepada pengguna jika ada pertanyaan.

- [ ] 5. Implementasi AnimationEngine
  - [~] 5.1 Definisikan CSS `@keyframes` di `style.css`
    - Tulis keyframe untuk setiap CSS class reaksi: `reaction-surprised`, `reaction-happy`, `reaction-sleepy`, `reaction-angry`, `reaction-in-love`, `reaction-playful`
    - Tambahkan keyframe untuk animasi "bounce" dan "shake" pada Cat_Sprite (Requirement 4.1)
    - Tambahkan CSS transition halus (150ms–500ms) untuk semua perubahan visual Cat_Sprite (Requirement 4.3)
    - _Requirements: 3.3, 4.1, 4.3_

  - [~] 5.2 Implementasi `AnimationEngine.play()` dan `stop()`
    - Implementasikan `AnimationEngine.stop()`: hapus semua CSS class animasi aktif, clear `_timer`, set `AppState.isAnimating = false`
    - Implementasikan `AnimationEngine.play(reaction)`: panggil `stop()` terlebih dahulu, tambahkan `reaction.cssClass`, set `AppState.isAnimating = true`, jadwalkan `reset()` setelah `reaction.duration` ms
    - Implementasikan `AnimationEngine.reset()`: kembalikan Cat_Sprite ke state idle
    - Update tampilan emoji kucing sesuai `reaction.emoji` saat `play()` dipanggil dan kembalikan ke emoji idle saat `reset()`
    - _Requirements: 3.3, 3.4, 3.5, 4.4, 4.5_

  - [ ]* 5.3 Tulis property test untuk siklus hidup CSS class
    - **Property 6: Siklus hidup animasi CSS class berjalan dengan benar**
    - **Validates: Requirements 3.3, 3.4**

  - [ ]* 5.4 Tulis property test untuk interrupt animasi
    - **Property 7: Interrupt animasi berjalan dengan benar**
    - **Validates: Requirements 3.5**

  - [ ]* 5.5 Tulis property test untuk kesesuaian emoji
    - **Property 8: Ekspresi emoji sesuai reaksi yang aktif**
    - **Validates: Requirements 4.5**

  - [ ]* 5.6 Tulis unit test untuk AnimationEngine
    - Test: `play()` menambahkan CSS class yang benar ke Cat_Sprite
    - Test: `stop()` menghapus semua CSS class animasi
    - Test: pemanggilan `play()` kedua selama animasi pertama menghapus class pertama
    - _Requirements: 3.3, 3.4, 3.5_

- [ ] 6. Implementasi FloatingText dan ParticleSystem
  - [~] 6.1 Implementasi `FloatingText.spawn(x, y)`
    - Buat elemen `<div>` dengan teks "+1" dan posisikan secara absolut di koordinat (x, y)
    - Tambahkan CSS class animasi "float-up" yang menggerakkan elemen ke atas lalu memudarkannya (opacity 0)
    - Hapus elemen dari DOM setelah animasi selesai menggunakan `animationend` event
    - _Requirements: 2.3_

  - [~] 6.2 Implementasi `ParticleSystem.emit(x, y, count)`
    - Buat N elemen partikel kecil (default 8) di posisi (x, y)
    - Animasikan setiap partikel menyebar ke arah acak dengan fade-out menggunakan CSS
    - Hapus semua elemen partikel dari DOM setelah animasi selesai
    - _Requirements: 4.2_

  - [ ]* 6.3 Tulis property test untuk visual feedback
    - **Property 3: Visual feedback spawn dekat posisi klik**
    - **Validates: Requirements 2.3, 4.2**

  - [ ]* 6.4 Tulis unit test untuk FloatingText
    - Test: `spawn()` membuat elemen DOM dengan teks "+1"
    - Test: elemen dihapus dari DOM setelah animasi selesai
    - _Requirements: 2.3_

- [ ] 7. Implementasi MilestoneChecker dan CelebrationEngine
  - [~] 7.1 Definisikan konfigurasi milestone
    - Buat array `milestones: [10, 50, 100, 500, 1000]` dan objek `milestoneMessages` dengan pesan unik per milestone sesuai tabel design
    - _Requirements: 5.1, 5.3_

  - [~] 7.2 Implementasi `MilestoneChecker.check(clickCount)`
    - Iterasi daftar milestone, periksa jika `clickCount === milestone` dan milestone belum ada di `AppState.milestonesReached`
    - Jika kondisi terpenuhi: tambahkan milestone ke `AppState.milestonesReached`, panggil `saveMilestones()`, dan panggil `CelebrationEngine.trigger(milestone, message)`
    - _Requirements: 5.1, 5.2, 5.4, 5.5_

  - [~] 7.3 Implementasi `CelebrationEngine.trigger()` dan `dismiss()`
    - Implementasikan `CelebrationEngine.trigger(milestone, message)`: tampilkan overlay DOM dengan animasi confetti dan pesan teks milestone, sembunyikan otomatis setelah minimal 2 detik
    - Implementasikan `CelebrationEngine.dismiss()`: sembunyikan dan hapus overlay dari tampilan
    - Buat animasi confetti menggunakan partikel CSS `@keyframes`
    - _Requirements: 5.2, 5.3_

  - [ ]* 7.4 Tulis property test untuk idempoten milestone
    - **Property 9: Milestone bersifat idempoten dalam satu sesi**
    - **Validates: Requirements 5.1, 5.2, 5.5**

  - [ ]* 7.5 Tulis property test untuk keunikan pesan milestone
    - **Property 10: Pesan milestone unik untuk setiap milestone**
    - **Validates: Requirements 5.3**

  - [ ]* 7.6 Tulis unit test untuk MilestoneChecker
    - Test: `check(10)` pada sesi baru memicu milestone sekali
    - Test: `check(10)` kedua kali tidak memicu milestone lagi
    - _Requirements: 5.1, 5.2, 5.5_

- [~] 8. Checkpoint — Pastikan semua test pada fase sebelumnya lulus
  - Pastikan semua test lulus, tanyakan kepada pengguna jika ada pertanyaan.

- [ ] 9. Implementasi event handler dan wiring semua komponen
  - [~] 9.1 Implementasi `handleCatClick(event)`
    - Tulis fungsi `handleCatClick` yang memanggil: `ClickCounter.increment()`, `FloatingText.spawn()`, `ParticleSystem.emit()`, `ReactionSystem.pick()`, `AnimationEngine.play()`, dan `MilestoneChecker.check()`
    - Bungkus semua pemanggilan dengan `try/catch` global sesuai pola error handling di design
    - Tambahkan global error handler `window.addEventListener('error', ...)` untuk mencatat error tanpa menghentikan aplikasi
    - _Requirements: 7.5_

  - [~] 9.2 Pasang event listener pada Cat_Sprite
    - Tambahkan event listener `click` pada Cat_Sprite yang memanggil `handleCatClick`
    - Tambahkan event listener `keydown` untuk tombol `Enter` dan `Space` agar Cat_Sprite dapat diaktifkan via keyboard
    - Tambahkan event listener `touchend` untuk dukungan perangkat mobile dan tablet
    - _Requirements: 6.4, 6.6_

  - [~] 9.3 Implementasi efek hover pada Cat_Sprite
    - Tambahkan CSS class hover yang mengubah skala atau bayangan Cat_Sprite saat kursor diarahkan ke elemen tersebut
    - Pastikan efek hover juga aktif saat Cat_Sprite dalam kondisi `:focus` untuk mendukung keyboard navigation
    - _Requirements: 1.5_

- [ ] 10. Implementasi layout responsif dan aksesibilitas
  - [~] 10.1 Implementasi layout responsif
    - Terapkan kelas Tailwind CSS untuk memastikan Cat_Sprite terlihat dan dapat diklik pada lebar viewport 320px hingga 1920px tanpa horizontal scroll
    - Pastikan posisi dan ukuran Cat_Sprite menyesuaikan secara responsif saat jendela browser di-resize
    - _Requirements: 6.1, 6.2_

  - [ ]* 10.2 Tulis property test untuk responsivitas layout
    - **Property 11: Layout responsif pada seluruh range viewport**
    - **Validates: Requirements 6.1**

  - [~] 10.3 Verifikasi aksesibilitas
    - Pastikan Cat_Sprite memiliki `aria-label` yang deskriptif (misal: "Klik kucing ini!")
    - Verifikasi kontras warna teks dan latar belakang memenuhi rasio minimal 4.5:1 (WCAG AA)
    - Pastikan semua elemen interaktif dapat dijangkau dengan navigasi keyboard (Tab, Enter, Space)
    - _Requirements: 6.3, 6.5, 6.6_

- [ ] 11. Implementasi error handling dan pengujian ketahanan
  - [~] 11.1 Tambahkan error isolation pada setiap modul
    - Bungkus logika internal AnimationEngine, ParticleSystem, FloatingText, dan MilestoneChecker masing-masing dengan `try/catch`
    - Pastikan error pada modul non-kritis tidak menghentikan `ClickCounter.increment()` dan `updateDisplay()`
    - _Requirements: 7.5_

  - [ ]* 11.2 Tulis property test untuk error isolation
    - **Property 12: Error pada modul non-kritis tidak menghentikan fungsi inti**
    - **Validates: Requirements 7.5**

- [~] 12. Final checkpoint — Pastikan seluruh aplikasi berfungsi dengan benar
  - Pastikan semua test lulus, tanyakan kepada pengguna jika ada pertanyaan.

## Notes

- Task bertanda `*` bersifat opsional dan dapat dilewati untuk penyelesaian MVP yang lebih cepat
- Setiap task merujuk ke requirements spesifik untuk keterlacakan (traceability)
- Checkpoint memastikan validasi inkremental di setiap fase penting
- Property tests memvalidasi properti kebenaran universal dari design
- Unit tests memvalidasi contoh spesifik dan kasus tepi (edge cases)
- Semua animasi menggunakan CSS `@keyframes` — tidak ada `setInterval` atau `requestAnimationFrame` loop manual
- Tidak ada build step; aplikasi dapat dibuka langsung di browser dengan membuka `index.html`

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["2.1", "3.1"] },
    { "id": 1, "tasks": ["2.2", "3.2", "5.1"] },
    { "id": 2, "tasks": ["2.3", "2.4", "3.3", "3.4", "3.5", "5.2", "6.1", "7.1"] },
    { "id": 3, "tasks": ["5.3", "5.4", "5.5", "5.6", "6.2", "7.2"] },
    { "id": 4, "tasks": ["6.3", "6.4", "7.3"] },
    { "id": 5, "tasks": ["7.4", "7.5", "7.6", "9.1"] },
    { "id": 6, "tasks": ["9.2", "9.3", "11.1"] },
    { "id": 7, "tasks": ["10.1", "11.2"] },
    { "id": 8, "tasks": ["10.2", "10.3"] }
  ]
}
```
