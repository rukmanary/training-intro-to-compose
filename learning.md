# Catatan Belajar Jetpack Compose

Dokumentasi ini berisi poin-poin penting yang dipelajari selama pengembangan aplikasi *IntroToCompose*.

## 1. Tata Letak dan Perataan (Layout & Alignment)
*   **Box vs Column:**
    *   `Box` digunakan untuk menumpuk elemen (stack) atau memposisikan satu elemen secara bebas (seperti `Alignment.Center`). Lebih efisien untuk menaruh konten tepat di tengah layar.
    *   `Column` digunakan untuk menyusun elemen secara berurutan dari atas ke bawah.
*   **Masalah Perataan Teks:** Teks di dalam `Box` tidak akan terlihat di tengah jika `Box` tersebut tidak mengisi seluruh ruang yang tersedia. Gunakan `Modifier.fillMaxSize()` pada `Box` di dalam `Card` agar perataan `Alignment.Center` bekerja maksimal.
*   **Constraint Surface:** `Surface` yang menjadi anak langsung dari `Scaffold` cenderung memaksa isinya untuk memenuhi seluruh layar. Gunakan `Box(contentAlignment = Alignment.Center)` sebagai pembungkus agar komponen di dalamnya (seperti `Card`) tetap pada ukuran aslinya (misal: 100dp).

## 2. Satuan Ukuran (dp vs sp)
*   **dp (Density-independent Pixels):** Digunakan untuk dimensi fisik seperti lebar, tinggi, padding, dan margin. Menjamin ukuran yang konsisten di berbagai kepadatan layar.
*   **sp (Scale-independent Pixels):** Digunakan **hanya** untuk ukuran teks. Satuan ini menghormati pengaturan aksesibilitas pengguna jika mereka mengubah ukuran font sistem.

## 3. Efek Visual (Elevation)
*   **Card Elevation:** Memberikan kedalaman visual (bayangan) dan perubahan warna tonal di Material 3. Membuat komponen terlihat terpisah dari latar belakang.
*   Gunakan `CardDefaults.cardElevation(defaultElevation = ...dp)`.

## 4. State Management (by vs =)
*   **Menggunakan `=` (Assignment):**
    *   Variabel menyimpan objek `MutableState`.
    *   Akses nilai harus menggunakan `.value` (contoh: `counter.value`).
*   **Menggunakan `by` (Property Delegation):**
    *   Variabel menyimpan nilai aslinya (misal: `Int`).
    *   Akses nilai langsung tanpa `.value`, membuat kode lebih bersih.
    *   **Penting:** Membutuhkan import `androidx.compose.runtime.getValue` dan `setValue`.

## 5. State Hoisting & Lambda Patterns
*   **Data Down, Events Up:** State ditaruh di Parent, dikirim ke Child sebagai parameter, dan perubahan dikirim balik ke Parent via Lambda (callback).
*   **Pola Lambda (Best Practice):**
    *   **Opsi A: `() -> Unit` (Direkomendasikan untuk Action/Tombol)**
        *   Child hanya lapor: "Saya diklik".
        *   Parent bebas menentukan logikanya (tambah skor, ganti warna, dll).
        *   Contoh: `Circle(onClick = { moneyCounter += 100 })`.
    *   **Opsi B: `(T) -> Unit` (Digunakan untuk Input Data)**
        *   Child mengirimkan nilai baru yang dihasilkannya.
        *   Contoh: `TextField(onValueChange = { newValue -> text = newValue })`.
*   **Analisis Perbandingan:**
    *   Menggunakan `() -> Unit` lebih baik untuk komponen seperti `Circle` karena menjaga komponen tetap **Murni (Pure)** dan **Reusable**. Komponen tidak perlu tahu logika bisnis (seperti harus menambah angka berapa), ia hanya perlu tahu kapan aksi terjadi.
    *   Menggunakan `(Int) -> Unit` pada tombol cenderung redundan karena Parent biasanya sudah memiliki akses ke State tersebut.

## 6. Jetpack Compose vs React Native (Padanan Konsep)
*   **Composable Function** ≈ Functional Component.
*   **Recomposition** ≈ Re-render.
*   **State Management:** `remember { mutableStateOf }` ≈ `useState`.
*   **Memoization (Value):** `remember(key) { ... }` ≈ `useMemo`. Digunakan untuk menyimpan hasil perhitungan berat.
*   **Memoization (Function):** `remember { { ... } }` ≈ `useCallback`. Digunakan untuk menjaga referensi fungsi (lambda) agar tetap sama di antara recomposition.
*   **Layout:** 
    *   `Box` ≈ `<View>` dengan gaya stack/absolute.
    *   `Column/Row` ≈ `<View>` dengan `flexDirection: column/row`.
*   **Smart Recomposition:** Berbeda dengan React Native yang sering butuh `React.memo` secara manual, Jetpack Compose secara otomatis melewati (skip) proses recomposition jika parameter komponen tidak berubah (otomatis memoized).
