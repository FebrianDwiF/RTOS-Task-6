# Proyek Kontrol LED Multi-Thread Menggunakan STM32 dan FreeRTOS

## Deskripsi
Proyek ini adalah implementasi kontrol LED multi-thread pada STM32 menggunakan FreeRTOS. Dua LED utama (LED hijau dan merah) dikendalikan dengan pola kedipan yang berbeda. Sistem ini menggunakan mekanisme critical section dengan `taskENTER_CRITICAL` dan `taskEXIT_CRITICAL` untuk mencegah interferensi dalam akses data bersama (`startFlag`), dengan LED kuning sebagai indikator interferensi.

## Fitur Utama
- **Task Hijau (GreenLEDTask)**: Mengontrol LED hijau dengan pola kedipan setiap 0,5 detik.
- **Task Merah (RedLEDTask)**: Mengontrol LED merah dengan pola kedipan setiap 0,1 detik.
- **Penggunaan Critical Section**: Menjamin akses eksklusif ke data bersama tanpa interferensi.

## Struktur Task

### 1. `defaultTask`
- **Prioritas:** Normal (`osPriorityNormal`)
- **Deskripsi:** Task default yang menjalankan loop kosong dengan delay minimal.

### 2. `GreenLEDTask`
- **Prioritas:** Idle (`osPriorityIdle`)
- **Deskripsi:** Task ini mengontrol LED hijau, menghidupkannya setiap 0,5 detik.
- **Akses Data Bersama**: Mengakses `accessSharedData` dalam critical section menggunakan `taskENTER_CRITICAL()` dan `taskEXIT_CRITICAL()`.

### 3. `RedLEDTask`
- **Prioritas:** Normal (`osPriorityNormal`)
- **Deskripsi:** Task ini mengontrol LED merah, menghidupkannya setiap 0,1 detik.
- **Akses Data Bersama**: Mengakses `accessSharedData` dalam critical section menggunakan `taskENTER_CRITICAL()` dan `taskEXIT_CRITICAL()`.

## Pengelolaan Data Bersama Menggunakan Critical Section

Fungsi `accessSharedData` digunakan untuk mengelola akses ke data bersama antara `GreenLEDTask` dan `RedLEDTask`. Modifikasi telah dilakukan dengan menggunakan critical section untuk mencegah konflik saat akses data bersama. 

Untuk menangani critical section, FreeRTOS menyediakan dua fungsi:
- **Disable interrupts:** `taskENTER_CRITICAL();`
- **Enable interrupts:** `taskEXIT_CRITICAL();`
  
Dengan langkah berikut:
1. **Disable interrupts:** Panggil `taskENTER_CRITICAL()` sebelum mengakses data bersama.
2. **Access shared resource**: Akses `startFlag` atau data lain yang dibagi.
3. **Enable interrupts:** Panggil `taskEXIT_CRITICAL()` setelah akses selesai.

Kode di setiap task telah dimodifikasi agar mengikuti langkah ini untuk memastikan bahwa hanya satu task yang mengakses data bersama pada satu waktu. Akibatnya, LED biru yang berfungsi sebagai indikator interferensi tidak akan menyala, karena tidak akan ada lagi konflik.


## Konfigurasi GPIO
- LED hijau, merah, dan biru dihubungkan pada `GPIOA`:
  - **GPIO_PIN_0** untuk LED hijau
  - **GPIO_PIN_1** untuk LED merah
  - **GPIO_PIN_2** untuk LED kuning (indikator interferensi)
- Konfigurasi pin sebagai output push-pull tanpa pull-up/pull-down resistor.

## Fungsi Utama

### `accessSharedData`
- **Deskripsi:** Mengelola akses ke data bersama antara `GreenLEDTask` dan `RedLEDTask` dalam critical section.
  - Jika `startFlag` bernilai 1, task diizinkan mengakses data dan flag diatur menjadi 0.
  - Jika task lain mencoba mengakses data ketika `startFlag` bernilai 0, LED biru akan menyala untuk menandai interferensi.
  - Setelah selesai, `startFlag` diatur kembali menjadi 1, dan LED kuning dimatikan.

### `SystemClock_Config`
- Menggunakan HSI (High Speed Internal) clock dengan frekuensi rendah tanpa PLL untuk kesederhanaan.

### `MX_GPIO_Init`
- Menginisialisasi GPIO yang digunakan untuk LED.

## Langkah-Langkah Penggunaan
1. Clone repository ini.
2. Buka proyek di STM32CubeIDE.
3. Build proyek.
4. Flash ke board STM32.
5. LED akan mulai berkedip sesuai pola yang telah diprogram tanpa menyalakan LED biru.

## Persyaratan Sistem
- **Hardware**: STM32 microcontroller
- **Software**: STM32CubeIDE atau IDE serupa, FreeRTOS, STM32 HAL library.

## Catatan Tambahan
- Critical section menggunakan `taskENTER_CRITICAL` dan `taskEXIT_CRITICAL` memastikan bahwa task memiliki akses eksklusif ke data bersama tanpa interferensi.
- Karena tidak ada lagi konflik akses data, LED kuning tidak akan menyala.

https://github.com/user-attachments/assets/b73f0831-d189-440d-bf02-6f8510b37d58
