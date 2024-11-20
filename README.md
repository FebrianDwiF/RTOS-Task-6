# Proyek Pengendalian LED dengan FreeRTOS pada STM32
Proyek ini menunjukkan bagaimana menggunakan **FreeRTOS** pada mikrokontroler STM32 untuk mengontrol beberapa LED dengan manajemen tugas dan sinkronisasi yang tepat.

## Fitur
- **Pengendalian LED Berbasis Tugas**:  
  - LED hijau berkedip setiap 500ms.  
  - LED merah berkedip setiap 100ms.  
- **Manajemen Sumber Daya Bersama**: Tugas berbagi akses ke sumber daya kritis menggunakan mekanisme `taskENTER_CRITICAL()` dan `taskEXIT_CRITICAL()` dari FreeRTOS.  
- **Indikator Kesalahan**: LED biru menyala saat terjadi konflik pada akses sumber daya.

## Struktur Proyek
- **`main.c`**: Berisi kode utama, termasuk definisi tugas dan logika manajemen sumber daya bersama.  
- **`FreeRTOS`**: Kernel RTOS untuk penjadwalan dan manajemen tugas.  
- **`HAL`**: Library HAL STM32 untuk inisialisasi dan pengendalian periferal.  

---

## Memulai Proyek

### Prasyarat
- STM32CubeMX (untuk konfigurasi dan generasi proyek).  
- STM32CubeIDE (untuk pengembangan dan debugging).  
- Board STM32 (misalnya STM32F103, STM32F4, dll.) dengan pin GPIO yang diperlukan.

### Konfigurasi Perangkat Keras
| Pin        | Fungsi            | Koneksi                   |
|------------|-------------------|---------------------------|
| GPIOA Pin 0| LED Hijau         | Hubungkan ke LED dan resistor |
| GPIOA Pin 1| LED Merah         | Hubungkan ke LED dan resistor |
| GPIOA Pin 3| LED Biru (Error)  | Hubungkan ke LED dan resistor |

### Cara Kompilasi dan Flash
1. Buka proyek ini di STM32CubeIDE.  
2. Kompilasi proyek.  
3. Flash program ke mikrokontroler STM32.  
4. Perhatikan perilaku LED.

---

## Penjelasan Kode

### Alur Kerja
1. **Inisialisasi**:  
   - HAL dan GPIO diinisialisasi.  
   - Scheduler FreeRTOS dijalankan dengan tiga thread/tugas:  
     - `defaultTask`: Tugas placeholder.  
     - `green_led`: Mengontrol LED hijau dengan interval 500ms.  
     - `red_led`: Mengontrol LED merah dengan interval 100ms.

2. **Logika Tugas**:  
   - Kedua tugas (`green_led` dan `red_led`) mengakses sumber daya bersama dalam critical section.  
   - Sumber daya dilindungi oleh flag (`startFlag`).  
   - Jika terjadi konflik, LED biru akan menyala sebagai indikator kesalahan.  

3. **Manajemen Sumber Daya Bersama**:  
   - Ketika suatu tugas memasuki critical section:  
     - Memeriksa apakah sumber daya bebas menggunakan `startFlag`.  
     - Jika sedang digunakan, LED biru menyala.  
     - Operasi baca/tulis disimulasikan dengan delay.  
   - Setelah keluar dari critical section, flag diatur ulang, dan LED biru dimatikan.

4. **Timing**:  
   - Delay pada tugas diimplementasikan menggunakan `osDelay()`.

---

## Penjelasan Rinci Kode

### Tugas
- **Tugas LED Hijau (`green_led`)**:  
  - Mengaktifkan GPIOA Pin 0 (LED hijau) selama 500ms.  
  - Mengakses sumber daya bersama menggunakan API critical section.  
  - Melepaskan sumber daya dan mengatur ulang flag.  

- **Tugas LED Merah (`red_led`)**:  
  - Mengaktifkan GPIOA Pin 1 (LED merah) selama 100ms.  
  - Berbagi sumber daya yang sama dengan tugas LED hijau, sehingga berpotensi menyebabkan konflik.  

- **Manajemen Sumber Daya Bersama**:  
  - Menggunakan variabel global `startFlag`.  
  - Tugas memasuki critical section untuk mengakses sumber daya dengan aman.  
  - LED biru menyala jika terjadi konflik dalam akses sumber daya.  

### Simulasi Operasi
- Fungsi `SimulateReadWriteOperation()` mensimulasikan penggunaan sumber daya melalui loop delay.

### Penanganan Kesalahan
- Konflik akses sumber daya memicu LED biru (GPIOA Pin 3) sebagai indikator.


### Penjelasan Alur Kerja
Proyek ini menunjukkan bagaimana FreeRTOS menangani penjadwalan tugas dan sinkronisasi. LED merepresentasikan eksekusi tugas secara bersamaan, sementara LED biru menunjukkan penanganan kesalahan selama konflik akses sumber daya.



https://github.com/user-attachments/assets/b6898a4a-2fcc-4665-b4de-1da2afced3a2

