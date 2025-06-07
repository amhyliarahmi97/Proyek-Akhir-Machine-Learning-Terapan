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

Dataset terdiri dari tiga file utama:
- `BX-Books.csv` (10.000 baris, 8 kolom)
- `BX-Users.csv` (278.858 baris, 3 kolom)
- `BX-Book-Ratings.csv` (1.149.780 baris, 3 kolom)

### Penjelasan Fitur

- `isbn`: ID unik buku
- `title`: Judul buku
- `author`: Penulis
- `year`: Tahun terbit
- `publisher`: Penerbit
- `image_s/m/l`: URL gambar (tidak digunakan)
- `user_id`: ID pengguna
- `location`: Lokasi pengguna
- `age`: Usia pengguna
- `rating`: Skor 0–10

### Insight Tambahan
- Kolom `age` mengandung nilai hilang dan outlier (misal usia <5 atau >100).
- Rating dengan nilai 0 dianggap implisit dan tidak digunakan dalam pelatihan model.

---

## 4. Data Preparation

### a. Pembersihan Data
- Menghapus duplikat ISBN pada data buku.
- Memfilter buku dengan tahun terbit antara 1900–2025.
- Menghapus kolom gambar yang tidak digunakan.
- Menghapus rating eksplisit bernilai 0.

### b. Persiapan untuk Content-Based Filtering
- Mengisi nilai kosong di kolom `title` dan `author` dengan string kosong.
- Membuat fitur `combined_features` dari gabungan `title` dan `author`.
- Menggunakan TF-IDF vectorizer untuk mengubah teks menjadi fitur numerik.

---

## 5. Modeling

### a. Collaborative Filtering (SVD)

Menggunakan library `Surprise`, model dilatih pada data eksplisit dengan teknik SVD. Proses meliputi:
- Split data 80:20
- Latih model
- Evaluasi menggunakan RMSE
- Berikan rekomendasi untuk user tertentu

#### Contoh Output Rekomendasi (Top-5)

| No | Judul Buku                              |
|----|------------------------------------------|
| 1  | The Lovely Bones: A Novel	                                |
| 2  | A Prayer for Owen Meany                           |
| 3  | The Return of the King       |
| 4  | Harry Potter and the Prisoner of Azkaban (Book 3)                     |
| 5  | Voyager                       |

### b. Content-Based Filtering

Langkah-langkah:
- Gabungkan judul dan penulis
- TF-IDF vectorizer → Cosine Similarity
- Cari buku paling mirip berdasarkan judul

#### Contoh Output Rekomendasi

Untuk judul **Harry Potter and the Chamber of Secrets (Book 2)**:

| No | Judul Buku                                 |
|----|---------------------------------------------|
| 1  | Harry Potter and the Chamber of Secrets (Book 2)     |
| 2  | Harry Potter and the Chamber of Secrets (Book 2)    |
| 3  | Harry Potter and the Goblet of Fire (Book 4)         |
| 4  | Harry Potter and the Goblet of Fire (Book 4)   |
| 5  | Harry Potter and the Order of the Phoenix (Book 5)      |

---

## 6. Evaluation

### a. Evaluasi SVD

Metrik evaluasi:
- **RMSE**: 1.6399
- **Precision@5**: 0.7062
- **Recall@5**: 0.7025

### b. Evaluasi Content-Based Filtering

Evaluasi dilakukan secara manual berdasarkan kemiripan konten. Model ini cocok untuk pengguna baru (cold-start problem) karena tidak bergantung pada data rating pengguna lain.

---

## 7. Kesimpulan

- **Model SVD** efektif untuk pengguna dengan riwayat interaksi.
- **Content-Based Filtering** cocok untuk pengguna baru.
- Evaluasi menunjukkan bahwa sistem rekomendasi memberikan hasil relevan.

---

