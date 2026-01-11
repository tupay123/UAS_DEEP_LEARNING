# 📊 Analisis Sentimen Ulasan Gadget (Studi Kasus: Komentar YouTube Gadgetin)

Proyek ini membangun sistem **analisis sentimen** untuk mengklasifikasikan komentar penonton pada video review gadget di channel YouTube **Gadgetin** ke dalam tiga kelas: **Positif**, **Negatif**, dan **Netral**.

## 👥 Anggota Kelompok
- Arphan Maulana Firdaus — 2230511140  
- Naufal Zacky Ardiitya — 2230511107  
- Rafli Zacky Ardiyasa — 2230511108  

---

## 1. Latar Belakang
Di era digital, ulasan/opini publik pada produk sangat memengaruhi keputusan pembelian. Channel YouTube **Gadgetin** menjadi salah satu referensi teknologi populer di Indonesia, dan kolom komentar video review-nya memuat ribuan opini terkait harga, spesifikasi, serta kualitas produk (misalnya iPhone, Xiaomi, dll).

Karena jumlah komentar sangat besar, analisis manual tidak efisien. Oleh karena itu, proyek ini mengembangkan model otomatis untuk memetakan sentimen komentar menjadi **Positif/Negatif/Netral**.

---

## 2. Tujuan
1. Mengumpulkan komentar YouTube dari video review gadget di channel Gadgetin melalui proses **scraping mandiri**.  
2. Melakukan **preprocessing** agar teks siap dipakai untuk pelatihan model.  
3. Melatih dan membandingkan performa 3 pendekatan:
   - **BiLSTM** (Deep Learning)
   - **CNN-char** (Deep Learning berbasis karakter)
   - **SVM + TF-IDF** (Machine Learning sebagai baseline)
4. Menentukan model terbaik dan mendemonstrasikan **inference** pada komentar baru.

---

## 3. Dataset dan Sumber Data
- **Sumber**: Kolom komentar video-video review gadget pada channel YouTube Gadgetin  
- **Metode pengambilan**: Scraping mandiri  
- **Format file**: `.csv` (mis. `data_komentar_lengkap.csv`)  
- **Jumlah data mentah**: > 3.100 komentar  
- **Jumlah data terpakai (setelah proses)**: 3.064 komentar  

Komentar umumnya berisi opini terkait:
- Harga (mahal/murah)
- Perbandingan merek (iPhone vs Android, dll)
- Kualitas/fitur produk

---

## 4. Pelabelan Sentimen
Data dilabeli ke dalam 3 kelas:
- **Netral**
- **Positif**
- **Negatif**

> Catatan: Pada komentar YouTube, kelas *Netral* sering mendominasi karena banyak komentar berupa pertanyaan teknis atau komentar ringan/intermezzo.

---

## 5. Distribusi Kelas
Distribusi data setelah pelabelan:
- **Netral**: 2.591 (84,56%)
- **Positif**: 382 (12,47%)
- **Negatif**: 91 (2,97%)

Dataset bersifat **imbalanced** (kelas Negatif sangat sedikit), sehingga metrik seperti **precision/recall/F1-score** penting untuk diperhatikan, bukan hanya akurasi.

---

## 6. Pemrosesan Data (Preprocessing)
Tahapan pembersihan yang dilakukan:
- **Case folding**: semua huruf menjadi lowercase  
- **Regex cleaning**: menghapus angka yang tidak relevan, URL, dan tanda baca berlebih  
- **Emoji removal**: menghapus emoji agar model fokus pada teks  
- **Stopword removal**: menghapus kata-kata umum yang tidak membawa sentimen (mis. “dan”, “yang”, “di”)  

---

## 7. Pembagian Data (Splitting)
Data dibagi dengan rasio **80:20** menggunakan `stratify=y` agar proporsi kelas pada train dan test tetap terjaga.
- **Train**: 2.451 sampel  
- **Test**: 613 sampel  

---

## 8. Arsitektur Model dan Metodologi

### 8.1 Percobaan 1 — BiLSTM (Deep Learning)
Menggunakan **Bidirectional LSTM** untuk menangkap konteks kalimat dari dua arah (maju dan mundur), cocok untuk komentar yang memiliki dependensi konteks.

### 8.2 Percobaan 2 — CNN-char (Deep Learning)
Menggunakan **CNN berbasis karakter** untuk menangkap pola penulisan khas komentar YouTube, misalnya:  
`"bagusss"`, `"kereeenn"`, typo, atau variasi karakter.

### 8.3 Percobaan 3 — SVM + TF-IDF (Machine Learning)
Menggunakan **TF-IDF** sebagai representasi fitur teks, kemudian diklasifikasikan oleh **Support Vector Machine** sebagai baseline pembanding.

---

## 9. Konfigurasi Pelatihan (Hyperparameters)
Konfigurasi pelatihan untuk model deep learning disamakan untuk menjaga konsistensi perbandingan:
- **Epoch**: 8 (berdasarkan konvergensi stabil pada epoch ke-8)
- **Optimizer**: Adam
- **Loss function**: (Categorical/Sparse) Crossentropy untuk klasifikasi multi-kelas
- **Callbacks**: monitoring `validation loss` untuk mencegah overfitting

---

## 10. Hasil Evaluasi

### 10.1 Rekap Akurasi
| No | Model | Akurasi Train | Akurasi Test |
|---:|------|--------------:|-------------:|
| 1 | BiLSTM | 100,00% | 93,64% |
| 2 | CNN-char | 93,06% | 92,82% |
| 3 | SVM (TF-IDF) | 99,92% | 95,27% |

**Model terbaik (berdasarkan akurasi test)**: **SVM + TF-IDF** (95,27%).

### 10.2 Classification Report (Test Set)
Ringkasan metrik per kelas (precision/recall/F1) menunjukkan tantangan utama ada pada kelas **Negatif** (support kecil).

**BiLSTM**
- Negatif: P=0,75 | R=0,33 | F1=0,46 (support=18)  
- Netral:  P=0,95 | R=0,98 | F1=0,96 (support=518)  
- Positif: P=0,87 | R=0,79 | F1=0,83 (support=77)  

**CNN-char**
- Negatif: P=1,00 | R=0,22 | F1=0,36 (support=18)  
- Netral:  P=0,94 | R=0,97 | F1=0,96 (support=518)  
- Positif: P=0,82 | R=0,78 | F1=0,80 (support=77)  

**SVM + TF-IDF**
- Negatif: P=1,00 | R=0,44 | F1=0,62 (support=18)  
- Netral:  P=0,95 | R=0,99 | F1=0,97 (support=518)  
- Positif: P=0,94 | R=0,81 | F1=0,87 (support=77)  

---

## 11. Analisis Hasil
- Secara umum, ketiga model **melampaui target akurasi >92%** pada data uji.  
- Kelas **Netral** memiliki performa paling tinggi (support besar, pola relatif jelas).  
- Kelas **Negatif** sulit dipelajari dengan baik karena jumlahnya sangat sedikit (imbalanced), sehingga **recall** Negatif cenderung rendah pada semua model.  
- **SVM + TF-IDF** memberikan performa paling stabil dan akurasi tertinggi pada test set.

---

## 12. Validasi Tambahan (Model Terbaik)
Untuk memastikan performa terbaik SVM + TF-IDF bukan kebetulan, dilakukan **5-Fold Cross Validation**. Hasilnya menunjukkan akurasi validasi yang **konsisten di kisaran ~95%** pada setiap fold.

---

## 13. Inference / Prediksi Sentimen
Model terbaik dapat digunakan untuk memprediksi sentimen komentar baru. Secara umum alurnya:
1. Input komentar baru  
2. Terapkan preprocessing yang sama  
3. Konversi fitur (TF-IDF / tokenisasi untuk DL)  
4. Model mengeluarkan label: **Negatif / Netral / Positif**

---

## 14. Tools & Libraries
- Python (Pandas, NumPy) — pengolahan data  
- Scikit-learn — TF-IDF, SVM, splitting, evaluasi  
- TensorFlow/Keras — BiLSTM, CNN-char, dan pipeline training DL  
- Matplotlib/Seaborn — visualisasi  

---

## 15. Referensi
- Sumber data: komentar YouTube Gadgetin (scraping mandiri; dicatat pada Desember 2025)  
- Dokumentasi resmi Scikit-learn dan TensorFlow untuk klasifikasi teks  

---

