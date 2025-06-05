
# 📚 Proyek Akhir: Sistem Rekomendasi Buku Menggunakan Book-Crossing Dataset

**Nama:** Rahmi Amilia  
**Platform Dataset:** [Book-Crossing Dataset](http://www2.informatik.uni-freiburg.de/~cziegler/BX/)

---

## 1. Project Overview

Sistem rekomendasi merupakan bagian penting dalam membantu pengguna menemukan konten yang relevan di tengah banyaknya pilihan yang tersedia. Di industri buku, pengguna kerap kali kesulitan memilih buku yang sesuai dengan preferensinya. Oleh karena itu, dibutuhkan sistem rekomendasi buku yang mampu memberikan saran secara otomatis berdasarkan minat dan aktivitas pengguna sebelumnya.

Dataset Book-Crossing menyediakan informasi tentang pengguna, buku, dan interaksi pengguna dalam bentuk rating, yang sangat cocok digunakan untuk membangun sistem rekomendasi berbasis Collaborative Filtering.

> 📌 Referensi:  
> - Ziegler, C. et al. “Book-Crossing Dataset.” University of Freiburg, 2004.

---

## 2. Business Understanding
Proyek ini bertujuan untuk membangun sistem rekomendasi buku berdasarkan data rating pengguna menggunakan pendekatan **Collaborative Filtering (SVD)** dan **Content-Based Filtering**. Sistem ini diharapkan mampu meningkatkan pengalaman pengguna dengan merekomendasikan buku yang relevan dengan preferensi mereka.

---

## 3. Data Understanding

Dataset yang digunakan merupakan dataset "Book-Crossing Dataset" yang terdiri atas tiga file utama:

- `Books.csv` : berisi informasi detail buku.
- `Users.csv` : berisi informasi pengguna.
- `Ratings.csv` : berisi rating yang diberikan pengguna terhadap buku.

Berikut penjelasan beberapa fitur penting:

- **ISBN**: Kode unik setiap buku.
- **Book-Title**: Judul buku.
- **Book-Author**: Nama penulis buku.
- **Year-Of-Publication**: Tahun terbit.
- **Publisher**: Nama penerbit buku.
- **Image-URL-S/M/L**: URL gambar cover buku dalam ukuran kecil/sedang/besar.
- **User-ID**: ID pengguna.
- **Book-Rating**: Skor yang diberikan pengguna untuk buku (skala 0-10).

---

## 4. Data Preparation

### a. Pembersihan Data

- Menghapus nilai kosong (null) dan duplikat.
- Menstandarkan nama kolom dan menghapus kolom yang tidak relevan seperti kolom gambar (`Image-URL-*`).
- Menghapus data dengan `Year-Of-Publication` yang tidak valid atau ekstrem.

### b. Filter Rating Eksplisit

Kami menyaring hanya rating eksplisit, yaitu rating yang lebih besar dari nol:

```python
ratings = ratings[ratings['Book-Rating'] > 0]
```

Langkah ini penting untuk menghindari bias dari rating 0 yang tidak menunjukkan preferensi pengguna.

---

## 5. Modeling

### a. Model SVD (Collaborative Filtering)

Menggunakan library `Surprise`, kami membangun model SVD (Singular Value Decomposition). Dataset di-split menjadi data latih dan uji, dan model dilatih untuk memprediksi rating yang belum diberikan.

#### Contoh Hasil Rekomendasi (Top-5)

| No | Judul Buku                |
|----|---------------------------|
| 1  | The Giver (21st Century Reference)         |
| 2  | The Princess Bride: S Morgenstern's Classic          |
| 3  | The Handmaid's Tale |
| 4  | Ender's Game (Ender Wiggins Saga (Paperback)          |
| 5  | Harry Potter and the Sorcerer's Stone (Book 1)	              |

### b. Model Content-Based Filtering

Menggunakan fitur `Book-Title` dan `Book-Author`, dilakukan TF-IDF vectorization dan cosine similarity untuk merekomendasikan buku yang mirip.

#### Contoh Hasil Rekomendasi

| No | Judul Buku                |
|----|---------------------------|
| 1  | El Vendedor De Noticias (Espasa Juvenil) |
| 2  | Vieja Sirena, La |

---

## 6. Evaluation

### a. Evaluasi SVD

Model dievaluasi menggunakan metrik **RMSE (Root Mean Square Error)**:

$$
\text{RMSE} = \sqrt{ \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2 }
$$

Hasil:

- **RMSE = 1.6394**

Nilai ini menunjukkan deviasi rata-rata prediksi sekitar 3.5 poin dari rating aktual.

### b. Evaluasi Content-Based Filtering

Menggunakan metrik `Precision@5` dan `Recall@5` pada subset data pengguna.

Hasil:

- **Precision@5 = 0.7066**
- **Recall@5 = 0.7028**

---

## 7. Kesimpulan

- Model SVD menghasilkan rekomendasi berdasarkan pola rating pengguna lain yang mirip.
- Model Content-Based memberikan rekomendasi berdasarkan kesamaan konten buku.
- Evaluasi menunjukkan bahwa kedua pendekatan memiliki potensi, dengan SVD menunjukkan akurasi prediksi rating, dan Content-Based berguna untuk pengguna baru (cold-start).

---

## 7. Saran

- Menggabungkan kedua pendekatan (hybrid model) untuk meningkatkan performa.
- Menambahkan fitur seperti genre atau sinopsis buku untuk memperkuat Content-Based Filtering.
