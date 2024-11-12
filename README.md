Berikut adalah template README yang dapat digunakan untuk kode di atas:

---

# Proyek Kontrol LED Multi-Thread Menggunakan STM32 dan FreeRTOS

## Deskripsi
Proyek ini adalah implementasi kontrol LED multi-thread pada STM32 menggunakan FreeRTOS. Terdapat dua LED utama (LED hijau dan merah) yang berkedip dalam pola tertentu. Sistem ini menggunakan mekanisme critical section untuk mengakses data bersama (`startFlag`) secara aman, dengan indikator LED biru sebagai penanda terjadinya interferensi dalam akses data.

## Fitur Utama
- **Task Hijau (GreenLEDTask)**: Mengontrol LED hijau dengan pola kedipan setiap 0,5 detik.
- **Task Merah (RedLEDTask)**: Mengontrol LED merah dengan pola kedipan setiap 0,1 detik.
- **Deteksi Interferensi**: LED kuning menyala ketika ada konflik dalam akses data bersama.

## Struktur Task

### 1. `defaultTask`
- **Prioritas:** Normal (`osPriorityNormal`)
- **Deskripsi:** Task default yang menjalankan loop kosong dengan delay minimal.

### 2. `GreenLEDTask`
- **Prioritas:** Idle (`osPriorityIdle`)
- **Deskripsi:** Task ini mengontrol LED hijau, menghidupkannya setiap 0,5 detik.
- **Akses Data Bersama:** Mengakses `accessSharedData` dalam critical section untuk menghindari interferensi.

### 3. `RedLEDTask`
- **Prioritas:** Normal (`osPriorityNormal`)
- **Deskripsi:** Task ini mengontrol LED merah, menghidupkannya setiap 0,1 detik.
- **Akses Data Bersama:** Mengakses `accessSharedData` dalam critical section.

## Konfigurasi GPIO
- LED hijau, merah, dan biru dihubungkan pada `GPIOA`:
  - **GPIO_PIN_0** untuk LED hijau
  - **GPIO_PIN_1** untuk LED merah
  - **GPIO_PIN_2** untuk LED kuning (indikator interferensi)
- Konfigurasi pin sebagai output push-pull tanpa pull-up/pull-down resistor.

## Fungsi Utama

### `accessSharedData`
- **Deskripsi:** Mengelola akses ke data bersama antara `GreenLEDTask` dan `RedLEDTask`.
  - Jika `startFlag` bernilai 1, task diizinkan mengakses data dan flag diatur menjadi 0.
  - Jika task lain mencoba mengakses data ketika `startFlag` bernilai 0, LED kuning akan menyala untuk menandai interferensi.
  - Setelah selesai, `startFlag` diatur kembali menjadi 1, dan LED biru dimatikan.
  
### `SystemClock_Config`
- Menggunakan HSI (High Speed Internal) clock dengan frekuensi rendah tanpa PLL untuk kesederhanaan.

### `MX_GPIO_Init`
- Menginisialisasi GPIO yang digunakan untuk LED.

## Langkah-Langkah Penggunaan
1. Clone repository ini.
2. Buka proyek di STM32CubeIDE.
3. Build proyek.
4. Flash ke board STM32.
5. LED akan mulai berkedip sesuai pola yang telah diprogram.

## Persyaratan Sistem
- **Hardware**: STM32 microcontroller
- **Software**: STM32CubeIDE atau IDE serupa, FreeRTOS, STM32 HAL library.

## Catatan Tambahan
- Program ini menggunakan mekanisme FreeRTOS untuk mengelola multitasking dan critical section untuk akses data bersama.
- LED biru akan menyala ketika terjadi interferensi akses data, menandakan konflik dalam pengelolaan data antar-task.



https://github.com/user-attachments/assets/f2f78c50-ea71-4901-939e-91c4684fe9e9

