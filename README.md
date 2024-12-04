# RTOS Exercise 6
Proyek ini merupakan implementasi multithreading menggunakan FreeRTOS yang mengontrol LED hijau dan LED merah melalui dua task yaitu GreenLEDTask dan RedLEDTask. FreeRTOS digunakan untuk membuat task-task yang berjalan secara konkuren dengan prioritas berbeda. Proyek ini juga mencakup mekanisme akses data bersama yang dikendalikan menggunakan critical section untuk mencegah race condition.

## Cara Kerja 
1. GreenLEDTask
    Prioritas: Idle (prioritas terendah).
    * Menyalakan LED hijau (GPIO_PIN_0) selama 500 ms, kemudian mematikan.
    * Mengakses data bersama (startFlag) dengan proteksi critical section.
2. RedLEDTask
    Prioritas: Normal (lebih tinggi dari GreenLEDTask).
    * Menyalakan LED merah (GPIO_PIN_1) selama 100 ms, kemudian mematikan.
    * Mengakses data bersama (startFlag) juga dengan proteksi critical section.
3. Akses Data Bersama (accessSharedData())
    Kedua task mengakses variabel global startFlag untuk mensimulasikan operasi I/O bersama:
    Jika startFlag == 1, maka akan mengubah startFlag menjadi 0.
    Jika startFlag == 0, maka akan menyalakan GPIO_PIN_2 (indikasi akses bersamaan).
    Simulasi Penundaan: Dilakukan dengan SimulateReadWriteOperation() menggunakan loop kosong (nop). Setelah operasi, startFlag diubah kembali menjadi 1.
4. Proteksi Critical Section
    FreeRTOS menyediakan fungsi critical section:
    * taskENTER_CRITICAL(): Menghentikan sementara penjadwalan task lain, sehingga hanya satu task yang dapat mengakses data bersama.
    * taskEXIT_CRITICAL(): Mengizinkan kembali penjadwalan task lain.
   Hal ini untuk memastikan akses ke startFlag dilakukan secara eksklusif oleh satu task pada satu waktu untuk menghindari race condition.
5. Eksekusi Task
    Setelah inisialisasi, FreeRTOS scheduler (osKernelStart()) mulai berjalan:
    * RedLEDTask (prioritas lebih tinggi) berjalan lebih sering daripada GreenLEDTask.
    * GreenLEDTask hanya berjalan jika RedLEDTask sedang menunggu (idle).
    * LED hijau menyala setiap 500 ms, sedangkan LED merah menyala setiap 100 ms. Keduanya memanggil accessSharedData() untuk mengakses data bersama secara aman.

Kedua task akan terus berjalan secara infinite loop (for(;;)), dikendalikan oleh scheduler FreeRTOS. Akses data bersama dilakukan secara bergantian dengan proteksi critical section.

## Hardware

## Output Proyek
LED hijau dan merah berkedip dengan pola berbeda
* LED hijau menyala setiap 500 ms.
* LED merah menyala setiap 100 ms.
