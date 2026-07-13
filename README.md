# Sistem Pengingat Istirahat Otomatis K3 Menggunakan YOLOv8 dan ByteTrack: Analisis Komparatif POV


Repositori ini berisi implementasi kode program untuk proyek sistem pengawasan Keselamatan dan Kesehatan Kerja (K3) Digital. Sistem ini dirancang untuk memantau durasi duduk pengguna komputer di ruang laboratorium secara *real-time* guna mencegah kelelahan fisik (*fatigue*) dan gangguan muskuloskeletal akibat kerja (*work-related musculoskeletal disorders*). 

Proyek penelitian ini disusun untuk memenuhi syarat publikasi ilmiah dengan format **Seminar Nasional Sains dan Teknologi (SNST)**.

---

## 📌 Deskripsi Proyek & Nilai Kebaruan

Berbeda dengan pendekatan sistem penghitung konvensional yang mengandalkan batasan wilayah statis (*Static Region of Interest / ROI*) yang kaku, sistem ini mengintegrasikan **YOLOv8n** dan **ByteTrack** secara dinamis untuk melacak pergerakan objek tanpa batas ruang. 

Sistem ini melakukan **Analisis Komparatif POV** untuk mengevaluasi performa pelacakan identitas (*tracking ID*) pada dua skenario sudut pandang kamera di laboratorium komputer:
1. **Webcam POV (Jarak Dekat):** Fokus pada pengamatan pengguna tunggal (*single-user*) yang aktif di depan monitor.
2. **CCTV POV (Jarak Jauh / Wide Angle):** Fokus pada pemantauan banyak orang sekaligus (*multi-person*) di dalam ruangan laboratorium.

---

## 🛠️ Arsitektur & Logika Sistem

Sistem bekerja secara sekuensial mengekstraksi video *frame per frame* dengan arsitektur sebagai berikut:
* **Object Detection:** Model pralatih `yolov8n.pt` yang difilter khusus untuk kelas `person` (indeks 0) untuk performa inferensi cepat mendekati *real-time*.
* **Object Tracking:** Algoritma *ByteTRACK* memanfaatkan kombinasi *Kalman Filter* (prediksi vektor pergerakan) dan *Hungarian Algorithm* (asosiasi matriks *Intersection over Union* / $IoU$) untuk menjaga konsistensi *tracking ID* unik pada tiap orang.
* **Logika Waktu & Skala Simulasi (*Time Scaling*):** Sistem menghitung akumulasi *frame* aktif per ID objek secara mandiri, lalu mengonversinya ke satuan detik riil berdasarkan nilai FPS video. Untuk kebutuhan validasi, batas waktu istirahat riil (misal 2 jam) disimulasikan (*time-scaled*) menjadi **10 detik** di dalam kode program.
  * **Status Aman (Hijau):** Durasi duduk objek $\le$ 10 detik.
  * **Status Peringatan K3 (Merah):** Durasi duduk objek $>$ 10 detik (Sistem mendeteksi pengguna kurang istirahat).

---

## 📊 Karakteristik Dataset Uji

Pengujian dilakukan menggunakan 4 variasi berkas video primer berdurasi masing-masing 15 detik dengan resolusi *Full HD* (1920x1080 piksel) pada kecepatan 30 fps:
1. `Video 1`: POV Webcam - User Aktif (Pengguna tunggal mengetik).
2. `Video 2`: POV Webcam - Meja Kosong (Validasi jeda/masa istirahat skala dekat).
3. `Video 3`: POV CCTV - User Aktif (Multi-person dari sudut pojok atas laboratorium).
4. `Video 4`: POV CCTV - Ruangan Kosong (Validasi kondisi kontrol laboratorium kosong).

---

## 📁 Struktur Repositori

```text
├── Datasets/                 # Folder berisi 4 video uji primer (.mp4)
├── output/                   # Hasil export video teranotasi sistem
├── Sistem_K3_Eskalator.ipynb # Jupyter Notebook / Google Colab script
├── Drawio_Flowchart.xml      # File diagram alir sistem (ANSI/ISO standar)
└── README.md                 # Dokumentasi proyek
