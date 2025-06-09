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

### Problem Statements
1. Pengguna kesulitan menemukan buku yang sesuai dengan minat mereka karena banyaknya pilihan.
2. Tidak semua pengguna memberikan ulasan secara eksplisit, sehingga perlu metode yang mampu mengatasi data yang sparse.

### Goals
- Membangun sistem rekomendasi yang dapat memberikan saran buku kepada pengguna berdasarkan preferensi.
- Menyediakan hasil rekomendasi Top-N untuk masing-masing pengguna.

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
- Penulis paling populer adalah Stephen King (68 buku).
- Penerbit paling umum adalah Ballantine Books (300 buku).
- Kolom `age` mengandung nilai hilang dan outlier (misal usia <5 atau >100).
- Rating dengan nilai 0 dianggap implisit dan tidak digunakan dalam pelatihan model.

---

## 4. Data Preparation

### a. Pembersihan Data
- Menghapus nilai duplikat isbn.
- Memfilter year agar berada di rentang valid (1900–2025).
- Menghapus kolom image_s, image_m, image_l karena tidak digunakan.
- Menghapus rating bernilai 0 (implicit rating) dari proses pelatihan model.
- Mengganti nilai kosong pada kolom title dan author dengan string kosong.

### b. Persiapan untuk Content-Based Filtering
- Mengganti nama kolom pada ratings menjadi user_id, isbn, rating
- Menggunakan modul Surprise
- Menginisialisasi objek Reader dengan rentang nilai rating.
- Menggunakan Dataset.load_from_df untuk membentuk dataset dari DataFrame.
- Membagi data menjadi data latih dan data uji menggunakan train_test_split.
- Mengganti nama kolom pada dataset `books` dan `users` agar konsisten dan mudah digunakan dalam pemodelan.
- Membuat fitur baru `combined_features` dari penggabungan `title` dan `author`, yang digunakan sebagai representasi konten buku untuk sistem Content-Based Filtering.
- Mengubah fitur `combined_features` ke dalam bentuk representasi numerik menggunakan **TF-IDF Vectorizer**, lalu menghitung kemiripan antar buku menggunakan **cosine similarity**.


---

## 5. Modeling

### a. Collaborative Filtering (SVD)

Metode **Collaborative Filtering** memanfaatkan pola interaksi antara pengguna dan item.  
Dengan algoritma **SVD (Singular Value Decomposition)**, sistem mencoba memprediksi rating yang akan diberikan pengguna terhadap buku yang belum dibaca.

- Algoritma: SVD dari `Surprise`.
- Output: Top-N rekomendasi buku berdasarkan User ID.

Menggunakan library `Surprise`, model dilatih pada data eksplisit dengan teknik SVD. Proses meliputi:
- Split data 80:20
- Latih model
- Evaluasi menggunakan RMSE
- Berikan rekomendasi untuk user tertentu

#### Contoh Output Rekomendasi (Top-5)

| No | Judul Buku                              |
|----|------------------------------------------|
| 1  | Lies and the Lying Liars Who Tell Them	                                |
| 2  | The Fellowship of the Ring                           |
| 3  | Snow Falling on Cedars       |
| 4  | The Return of the King                     |
| 5  | Harry Potter and the Sorcerer's Stone (Book 1)                       |

### Content-Based Filtering (CBF)
Content-Based Filtering merekomendasikan buku berdasarkan kemiripan kontennya, dalam hal ini berdasarkan judul buku.

- Representasi konten: TF-IDF dari judul buku.
- Pengukuran kemiripan: Cosine Similarity antar vektor TF-IDF.
- Output: Buku-buku yang mirip dengan buku input.

**Contoh Output (Input: Harry Potter and the Chamber of Secrets (Book 2))**  
1. *Harry Potter and the Sorcerer's Stone (Harry Potter (Paperback))*  
2. *Harry Potter and the Prisoner of Azkaban (Book 3)*  
3. *Harry Potter and the Goblet of Fire (Book 4)*  
4. *Harry Potter and the Order of the Phoenix (Book 5)*  
5. *Harry Potter and the Half-Blood Prince (Book 6)*


---

## 6. Evaluation

### a. Evaluasi SVD

Metrik evaluasi:
- **RMSE**: 1.6397
- **Precision@5**: 0.7063
- **Recall@5**: 0.7024

### b. Evaluasi Content-Based Filtering

Evaluasi dilakukan menggunakan metrik **Precision@5** untuk mengukur relevansi dari lima rekomendasi teratas.  
Hasil evaluasi menunjukkan nilai **Precision@5 = 1.00**, yang berarti semua rekomendasi relevan terhadap input pengguna.

Model ini juga cocok untuk pengguna baru (cold-start problem), karena tidak bergantung pada histori rating pengguna lain.

---

## 7. Kesimpulan

- **Model SVD** efektif untuk pengguna dengan riwayat interaksi.
- **Content-Based Filtering** cocok untuk pengguna baru.
- Evaluasi menunjukkan bahwa sistem rekomendasi memberikan hasil relevan.

---

