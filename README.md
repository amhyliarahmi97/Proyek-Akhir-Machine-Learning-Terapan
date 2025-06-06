# 📚 Proyek Akhir: Sistem Rekomendasi Buku Menggunakan Book-Crossing Dataset

**Nama:** Rahmi Amilia

**Platform Dataset:** [Book-Crossing Dataset](http://www2.informatik.uni-freiburg.de/~cziegler/BX/)

---

## 1. Project Overview

Sistem rekomendasi merupakan bagian penting dalam membantu pengguna menemukan konten yang relevan di tengah banyaknya pilihan yang tersedia. Di industri buku, pengguna kerap kali kesulitan memilih buku yang sesuai dengan preferensinya. Oleh karena itu, dibutuhkan sistem rekomendasi buku yang mampu memberikan saran secara otomatis berdasarkan minat dan aktivitas pengguna sebelumnya.

Dataset Book-Crossing menyediakan informasi tentang pengguna, buku, dan interaksi pengguna dalam bentuk rating, yang sangat cocok digunakan untuk membangun sistem rekomendasi berbasis Collaborative Filtering dan Content-Based Filtering.

> 📌 Referensi:
>
> * Ziegler, C. et al. “Book-Crossing Dataset.” University of Freiburg, 2004.

---

## 2. Business Understanding

Proyek ini bertujuan untuk membangun sistem rekomendasi buku berdasarkan data rating pengguna menggunakan pendekatan:

* **Collaborative Filtering** dengan algoritma SVD (Singular Value Decomposition)
* **Content-Based Filtering** dengan pendekatan TF-IDF dan cosine similarity

Sistem ini diharapkan mampu meningkatkan pengalaman pengguna dengan merekomendasikan buku yang relevan dengan preferensi mereka.

---

## 3. Data Understanding

Dataset yang digunakan terdiri dari tiga file utama:

* `Books.csv` : berisi informasi detail buku.
* `Users.csv` : berisi informasi pengguna.
* `Ratings.csv` : berisi rating yang diberikan pengguna terhadap buku.

### Ringkasan Dataset:

* Books.csv: **10.000 baris**, 8 kolom
* Ratings.csv: **1.149.780 baris**, 3 kolom
* Users.csv: **278.858 baris**, 3 kolom

### Penjelasan Fitur:

* **ISBN**: Kode unik setiap buku
* **Book-Title**: Judul buku
* **Book-Author**: Nama penulis buku
* **Year-Of-Publication**: Tahun terbit
* **Publisher**: Nama penerbit buku
* **Image-URL-S/M/L**: URL gambar cover buku dalam ukuran kecil/sedang/besar (tidak digunakan dalam model)
* **User-ID**: ID pengguna
* **Location**: Lokasi pengguna dalam format kota, negara bagian, negara
* **Age**: Usia pengguna
* **Book-Rating**: Skor yang diberikan pengguna untuk buku (skala 0–10)

> 📅 Catatan: Kolom `age` mengandung missing values dan nilai tidak valid (seperti 0 atau >100), sehingga perlu dibersihkan pada tahap data preparation.

---

## 4. Data Preparation

### a. Penyesuaian Kolom dan Pembersihan Awal

* Menstandarkan nama kolom agar seragam.
* Menghapus duplikasi pada kolom `ISBN`
* Menghapus kolom gambar (`Image-URL-S`, `Image-URL-M`, `Image-URL-L`) karena tidak relevan
* Memfilter data `Year-Of-Publication` agar hanya antara tahun 1900 dan 2025

### b. Filter Rating Eksplisit

Hanya rating eksplisit yang digunakan (rating > 0) untuk memastikan interaksi pengguna benar-benar merepresentasikan preferensi:

```python
ratings = ratings[ratings['Book-Rating'] > 0]
```

### c. Penanganan Missing Values untuk Content-Based

Untuk fitur `title` dan `author`, nilai kosong diganti dengan string kosong:

```python
books['title'] = books['title'].fillna('')
books['author'] = books['author'].fillna('')
```

### d. Pembuatan Fitur Gabungan

Gabungan `title` dan `author` digunakan sebagai input vectorisasi TF-IDF:

```python
books['combined_features'] = books['title'] + ' ' + books['author']
```

### e. TF-IDF Vectorization

Mengubah teks `combined_features` menjadi matriks numerik menggunakan TF-IDF, dilanjutkan dengan cosine similarity untuk mengukur kemiripan antar buku.

---

## 5. Modeling

### a. Model SVD (Collaborative Filtering)

Menggunakan library `Surprise`, kami membangun model SVD untuk memprediksi rating dari user terhadap buku yang belum dirating. Dataset dibagi menjadi train dan test set, kemudian model dilatih dan dievaluasi.

#### Contoh Hasil Rekomendasi Top-5 (User ID: 276729)

| No | Judul Buku                | Penulis            |
| -- | ------------------------- | ------------------ |
| 1  | Harry Potter and the Sorcerer's Stone | J. K. Rowling       |
| 2  | The Cat in the Hat                | Dr. Seuss     |
| 3  | The Return of the King                      | The Return of the King        |
| 4  | Harry Potter and the Prisoner of Azkaban (Book 3)	                 | J. K. Rowling        |
| 5  | Harry Potter and the Sorcerer's Stone (Book 1)            |J. K. Rowling|

### b. Model Content-Based Filtering

Menggunakan fitur `Book-Title` dan `Book-Author`, kami melakukan:

* TF-IDF Vectorization terhadap fitur gabungan
* Cosine Similarity antar buku
* Rekomendasi dilakukan berdasarkan kemiripan judul input dengan buku lain

#### Contoh Hasil Rekomendasi

| No | Judul Buku                                        | Penulis             |
| -- | ------------------------------------------------- | ------------------- |
| 1  | Harry Potter and the Sorcerer's Stone (Book 1)    | J.K. Rowling        |
| 2  | Harry Potter and the Prisoner of Azkaban (Book 3) | J.K. Rowling        |
| 3  | Eragon                                            | Christopher Paolini |
| 4  | Inkheart                                          | Cornelia Funke      |
| 5  | Artemis Fowl                                      | Eoin Colfer         |

---

## 6. Evaluation

### a. Evaluasi Collaborative Filtering (SVD)

Model dievaluasi menggunakan metrik RMSE (Root Mean Square Error):

$$
\text{RMSE} = \sqrt{ \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2 }
$$

* **RMSE = 1.6394**

### b. Precision\@K dan Recall\@K (SVD)

Hasil evaluasi pada testset untuk `k = 5`:

* **Precision\@5 = 0.7066**
* **Recall\@5 = 0.7028**

### c. Evaluasi Content-Based Filtering

Karena Content-Based tidak menghasilkan rating prediksi, maka evaluasi dilakukan secara kualitatif. Contohnya:

> Jika input judul "Harry Potter and the Chamber of Secrets", sistem merekomendasikan judul-judul serupa dari seri yang sama atau genre sejenis (fantasy).

Untuk ke depannya, evaluasi kuantitatif dapat dilakukan dengan user study atau implicit feedback.

---

## 7. Kesimpulan

* Model SVD efektif untuk pengguna aktif dengan riwayat rating.
* Content-Based Filtering sangat berguna untuk pengguna baru atau ketika data rating terbatas.
* Evaluasi menunjukkan hasil memuaskan dengan Precision dan Recall yang cukup tinggi.

---

## 8. Saran

* Kombinasi kedua pendekatan (hybrid model) berpotensi meningkatkan kualitas rekomendasi.
* Menambahkan fitur tambahan seperti genre dan sinopsis buku dapat meningkatkan kualitas model Content-Based.
* Diperlukan sistem evaluasi lebih komprehensif menggunakan data aktual dari pengguna.

---
