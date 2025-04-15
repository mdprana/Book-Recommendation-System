# Laporan Proyek Machine Learning - Sistem Rekomendasi Buku

## Project Overview

Sistem rekomendasi buku merupakan sistem yang memberikan rekomendasi buku kepada pembaca berdasarkan preferensi mereka. Sistem ini umum digunakan pada platform seperti Goodreads, Amazon, dan toko buku online lainnya. Pada proyek ini, saya membangun sistem rekomendasi buku berdasarkan preferensi pengguna di masa lalu serta rating yang diberikan pengguna terhadap buku-buku tertentu.

Di era digital saat ini, terdapat jutaan buku yang tersedia secara online. Tanpa adanya sistem rekomendasi yang efektif, pembaca akan kesulitan menemukan buku yang sesuai dengan minat mereka. Hal ini dapat menyebabkan pengalaman pengguna yang buruk dan penurunan tingkat keterlibatan pengguna pada platform tersebut.

Penelitian menunjukkan bahwa sistem rekomendasi yang efektif dapat meningkatkan engagement pengguna hingga 40% dan meningkatkan konversi penjualan hingga 30% (McKinsey, 2022). Dalam industri buku, di mana lebih dari 1 juta judul baru diterbitkan setiap tahun secara global, sistem rekomendasi menjadi semakin penting untuk membantu pembaca menemukan konten yang sesuai dengan minat mereka.

Referensi:
- [Adomavicius, G., & Tuzhilin, A. (2019). Personalization technologies: a process-oriented perspective. Communications of the ACM, 48(10), 83-90.](https://dl.acm.org/doi/10.1145/1089107.1089109)
- [Harper, F. M., & Konstan, J. A. (2016). The movielens datasets: History and context. ACM transactions on interactive intelligent systems, 5(4), 1-19.](https://dl.acm.org/doi/10.1145/2827872)

## Business Understanding

### Problem Statements

1. Bagaimana cara merekomendasikan buku-buku yang sesuai dengan preferensi pembaca berdasarkan perilaku membaca mereka di masa lalu?
2. Bagaimana cara memanfaatkan data rating dan metadata buku untuk memberikan rekomendasi yang akurat?
3. Bagaimana cara mengevaluasi kinerja sistem rekomendasi yang dibangun?

### Goals

1. Membangun sistem rekomendasi yang dapat menyarankan buku-buku yang mungkin disukai oleh pengguna berdasarkan preferensi mereka.
2. Mengembangkan model yang dapat memberikan rekomendasi personalisasi dengan tingkat akurasi yang tinggi.
3. Mengevaluasi dan membandingkan kinerja dari pendekatan-pendekatan yang diimplementasikan.

### Solution Approach

Untuk mencapai tujuan di atas, saya akan mengimplementasikan dua pendekatan sistem rekomendasi:

1. **Content-Based Filtering**: Algoritma ini merekomendasikan buku berdasarkan kesamaan konten atau karakteristik buku yang sebelumnya disukai oleh pengguna. Model ini akan menggunakan fitur seperti genre, penulis, dan tag buku untuk menemukan kesamaan antar buku. Pendekatan ini cocok untuk pengguna yang memiliki preferensi yang spesifik dan konsisten.

2. **Collaborative Filtering**: Algoritma ini merekomendasikan buku berdasarkan preferensi pengguna lain yang memiliki selera serupa. Model ini akan menganalisis pola rating dari banyak pengguna untuk menemukan kesamaan antara pengguna atau buku, dan membuat rekomendasi berdasarkan hal tersebut. Pendekatan ini efektif untuk menemukan preferensi baru yang mungkin tidak terduga oleh pengguna.

## Data Understanding

Dataset yang digunakan dalam proyek ini adalah [goodbooks-10k](https://www.kaggle.com/datasets/zygmunt/goodbooks-10k) yang dapat diunduh dari Kaggle. Dataset ini berisi informasi tentang 10.000 buku populer dan rating yang diberikan oleh pengguna Goodreads.

Dataset terdiri dari beberapa file CSV:

1. **books.csv**: Berisi metadata untuk setiap buku (ID Goodreads, penulis, judul, rating rata-rata, dll.).
2. **ratings.csv**: Berisi rating yang diberikan pengguna untuk buku (user_id, book_id, rating).
3. **book_tags.csv**: Berisi tag/shelves/genre yang diberikan oleh pengguna untuk buku.
4. **tags.csv**: Berisi terjemahan ID tag ke nama tag.
5. **to_read.csv**: Berisi ID buku yang ditandai "to read" oleh setiap pengguna.

### Eksplorasi Dataset

Setelah melakukan eksplorasi data, berikut adalah beberapa temuan penting:

**1. Jumlah Data**
```
Jumlah data buku: 10000
Jumlah data rating: 981756
Jumlah data tag: 34252
Jumlah data book_tags: 999912
Jumlah data to_read: 912705
```

**2. Struktur Data Buku**
```
Data columns (total 23 columns):
 #   Column                     Non-Null Count  Dtype  
---  ------                     --------------  -----  
 0   id                         10000 non-null  int64  
 1   book_id                    10000 non-null  int64  
 2   best_book_id               10000 non-null  int64  
 3   work_id                    10000 non-null  int64  
 4   books_count                10000 non-null  int64  
 5   isbn                       9300 non-null   object 
 6   isbn13                     9415 non-null   float64
 7   authors                    10000 non-null  object 
 8   original_publication_year  9979 non-null   float64
 9   original_title             9415 non-null   object 
 10  title                      10000 non-null  object 
 11  language_code              8916 non-null   object 
 12  average_rating             10000 non-null  float64
 13  ratings_count              10000 non-null  int64  
 14  work_ratings_count         10000 non-null  int64  
 15  work_text_reviews_count    10000 non-null  int64  
 16  ratings_1                  10000 non-null  int64  
 17  ratings_2                  10000 non-null  int64  
 18  ratings_3                  10000 non-null  int64  
 19  ratings_4                  10000 non-null  int64  
 20  ratings_5                  10000 non-null  int64  
 21  image_url                  10000 non-null  object 
 22  small_image_url            10000 non-null  object 
```

**3. Distribusi Rating**

Analisis distribusi rating menunjukkan bahwa mayoritas rating berada pada nilai 4 dan 5, yang menunjukkan kecenderungan pengguna untuk memberikan rating tinggi.

```
Statistik deskriptif rating:
count    981756.000000
mean          3.856534
std           0.983941
min           1.000000
25%           3.000000
50%           4.000000
75%           5.000000
max           5.000000
```

**4. Top 10 Buku dengan Rating Tertinggi**

```
10 buku dengan rating tertinggi (minimal 100 rating):
                                                 title                         authors  rating_mean  rating_count
469                           The Beautiful and Damned        F. Scott Fitzgerald        4.66           100
669                         The Taste of Home Cookbook  Janet Briggs, Beth Wittlinger   4.55           100
179            A People's History of the United States                Howard Zinn        4.54           100
59                           Girl with a Pearl Earring             Tracy Chevalier        4.53           100
58                                     Deception Point                   Dan Brown        4.50           100
```

**5. Visualisasi Data**

Berikut adalah visualisasi distribusi buku berdasarkan tahun publikasi:

![Distribusi Buku Berdasarkan Tahun Publikasi](https://i.ibb.co.com/33Y7hMd/433922366-000c46f7-eb1a-4b50-924e-25b369159956.png)

## Data Preparation

Beberapa tahapan persiapan data yang dilakukan pada proyek ini:

### 1. Pembersihan Data

Langkah pertama adalah mengatasi missing value pada dataset. Terdapat beberapa missing value pada kolom tertentu di books.csv:
- Menghapus buku dengan judul atau penulis yang kosong karena kedua informasi ini sangat penting untuk sistem rekomendasi.
- Membersihkan data rating untuk memastikan hanya menggunakan rating untuk buku yang valid.

Tahapan ini penting dilakukan untuk memastikan kualitas data yang akan digunakan dalam model.

### 2. Pemilihan Fitur

Untuk model Content-Based Filtering, dipilih fitur-fitur yang relevan seperti:
- book_id: ID unik buku
- title: Judul buku
- authors: Penulis buku
- language_code: Kode bahasa
- original_publication_year: Tahun publikasi asli

Pemilihan fitur ini penting karena fitur-fitur ini akan digunakan sebagai dasar untuk menemukan kesamaan antar buku.

### 3. Penggabungan Data

Menggabungkan data buku dengan tag untuk membuat representasi konten yang lebih kaya:
```bash
# Menggabungkan data buku dengan tag
book_tag_counts = book_tags.groupby('goodreads_book_id').agg({
    'tag_id': list, 
    'count': sum
}).reset_index()

tags_dict = dict(zip(tags['tag_id'], tags['tag_name']))

# Fungsi untuk mendapatkan tag name dari tag id
def get_tag_names(tag_ids):
    return [tags_dict.get(tag_id, "") for tag_id in tag_ids if tag_id in tags_dict]

# Menambahkan daftar tag ke data buku
book_tag_counts['tag_names'] = book_tag_counts['tag_id'].apply(get_tag_names)
book_tag_counts['tag_string'] = book_tag_counts['tag_names'].apply(lambda x: ' '.join(x))
```

Penggabungan ini penting dilakukan untuk memberikan konteks yang lebih kaya tentang setiap buku, yang akan meningkatkan kualitas rekomendasi berbasis konten.

### 4. Encoding User dan Book ID

Untuk model Collaborative Filtering, kita perlu mengubah user_id dan book_id menjadi indeks numerik yang berurutan:
```bash
user_ids = ratings_subset['user_id'].unique().tolist()
user_to_user_encoded = {x: i for i, x in enumerate(user_ids)}
user_encoded_to_user = {i: x for i, x in enumerate(user_ids)}

book_ids = ratings_subset['book_id'].unique().tolist()
book_to_book_encoded = {x: i for i, x in enumerate(book_ids)}
book_encoded_to_book = {i: x for i, x in enumerate(book_ids)}
```

Encoding ini diperlukan karena model deep learning memerlukan indeks numerik berurutan sebagai input.

### 5. Normalisasi Rating

Untuk meningkatkan performa model, rating dinormalisasi ke rentang 0-1:
```bash
ratings_subset['rating'] = ratings_subset['rating'].apply(lambda x: (x - min_rating) / (max_rating - min_rating))
```

Normalisasi ini penting agar membantu konvergensi model yang lebih cepat selama proses training.

## Modeling

### 1. Content-Based Filtering

Model Content-Based Filtering diimplementasikan dengan langkah-langkah berikut:

1. **TF-IDF Vectorization**: Mengubah teks konten (kombinasi penulis dan tag) menjadi vektor numerik menggunakan TF-IDF.
   ```bash
   tfidf = TfidfVectorizer(stop_words='english')
   tfidf_matrix = tfidf.fit_transform(books_with_tags['content'])
   ```

2. **Cosine Similarity**: Menghitung kesamaan antara buku menggunakan metrik cosine similarity.
   ```bash
   cosine_sim = cosine_similarity(tfidf_matrix, tfidf_matrix)
   ```

3. **Recommendation Function**: Membuat fungsi untuk memberikan rekomendasi berdasarkan kesamaan.
   ```bash
   def get_content_based_recommendations(title, cosine_sim=cosine_sim, df=books_with_tags, indices=indices, verbose=True):
       # Mengambil indeks buku yang sesuai dengan judul
       idx = indices[title]
       
       # Mendapatkan skor kesamaan buku dengan semua buku
       sim_scores = list(enumerate(cosine_sim[idx]))
       
       # Mengurutkan buku berdasarkan skor kesamaan
       sim_scores = sorted(sim_scores, key=lambda x: x[1], reverse=True)
       
       # Mendapatkan 10 buku dengan skor tertinggi (tidak termasuk buku itu sendiri)
       sim_scores = sim_scores[1:11]
       
       # Mendapatkan indeks buku
       book_indices = [i[0] for i in sim_scores]
       
       # Mengembalikan 10 buku teratas
       result = df.iloc[book_indices][['title', 'authors', 'original_publication_year']]
       
       if verbose:
           # Kode untuk menampilkan hasil
           ...
           
       return result if not verbose else None
   ```

**Contoh Hasil Rekomendasi Content-Based Filtering**:

Berikut adalah contoh rekomendasi untuk buku "The Hobbit":

```
Rekomendasi Buku Berdasarkan: The Hobbit
====================================================================================================
No   Judul                                              Penulis                        Tahun     
----------------------------------------------------------------------------------------------------
1    The Fellowship of the Ring (The Lord of the Rin... J.R.R. Tolkien                 1954      
2    The Two Towers (The Lord of the Rings, #2)         J.R.R. Tolkien                 1954      
3    The Return of the King (The Lord of the Rings, ... J.R.R. Tolkien                 1955      
4    The Lord of the Rings (The Lord of the Rings, #... J.R.R. Tolkien                 1955      
5    J.R.R. Tolkien 4-Book Boxed Set: The Hobbit and... J.R.R. Tolkien                 1973      
6    Unfinished Tales of Númenor and Middle-Earth       J.R.R. Tolkien, Christopher... 1980      
7    The Children of Húrin                              J.R.R. Tolkien, Christopher... 2007      
8    The Silmarillion (Middle-Earth Universe)           J.R.R. Tolkien, Christopher... 1977      
9    The Complete Guide to Middle-Earth                 Robert Foster, J.R.R. Tolki... 1974      
10   The History of the Hobbit, Part One: Mr. Baggin... John D. Rateliff, J.R.R. To... 2007      
```

**Kelebihan dan Kekurangan Content-Based Filtering**:

**Kelebihan:**
- Tidak memerlukan data dari pengguna lain untuk membuat rekomendasi
- Dapat merekomendasikan buku yang baru atau belum banyak di-rating
- Mampu menangkap preferensi spesifik pengguna

**Kekurangan:**
- Tidak dapat menemukan preferensi baru di luar yang sudah diketahui pengguna
- Sangat bergantung pada kualitas metadata buku
- Terbatas pada fitur eksplisit yang tersedia

### 2. Collaborative Filtering

Model Collaborative Filtering diimplementasikan menggunakan deep learning dengan TensorFlow:

1. **Model Architecture**: Menggunakan embedding layer untuk user dan book.
   ```bash
   class RecommenderNet(keras.Model):
       def __init__(self, num_users, num_books, embedding_size, **kwargs):
           super(RecommenderNet, self).__init__(**kwargs)
           self.num_users = num_users
           self.num_books = num_books
           self.embedding_size = embedding_size
           self.user_embedding = layers.Embedding(
               num_users,
               embedding_size,
               embeddings_initializer='he_normal',
               embeddings_regularizer=keras.regularizers.l2(1e-6)
           )
           self.user_bias = layers.Embedding(num_users, 1)
           self.book_embedding = layers.Embedding(
               num_books,
               embedding_size,
               embeddings_initializer='he_normal',
               embeddings_regularizer=keras.regularizers.l2(1e-6)
           )
           self.book_bias = layers.Embedding(num_books, 1)
       
       def call(self, inputs):
           user_vector = self.user_embedding(inputs[:, 0])
           user_bias = self.user_bias(inputs[:, 0])
           book_vector = self.book_embedding(inputs[:, 1])
           book_bias = self.book_bias(inputs[:, 1])
           
           dot_user_book = tf.tensordot(user_vector, book_vector, 2)
           
           x = dot_user_book + user_bias + book_bias
           
           return tf.nn.sigmoid(x)
   ```

2. **Training Model**: Model dilatih menggunakan data rating dari pengguna dengan learning rate 0.001 dan 100 epoch.

3. **Recommendation Function**: Membuat fungsi untuk memberikan rekomendasi berdasarkan prediksi model.
   ```bash
   def get_collaborative_recommendations(user_id, top_n=10):
       # Kode untuk mendapatkan rekomendasi
       ...
   ```

**Contoh Hasil Rekomendasi Collaborative Filtering**:

Berikut adalah contoh rekomendasi untuk pengguna acak:

```
Rekomendasi Buku Collaborative Filtering untuk User 40793:
================================================================================
No   Judul                                              Penulis                        Rating    
--------------------------------------------------------------------------------
1    The Last Juror                                     John Grisham                   0.89      
2    Don Quixote                                        Miguel de Cervantes Saavedr... 0.86      
3    The Taste of Home Cookbook                         Janet Briggs, Beth Wittling... 0.85      
4    A People's History of the United States            Howard Zinn                    0.84      
5    Girl with a Pearl Earring                          Tracy Chevalier                0.82      
6    The Beautiful and Damned                           F. Scott Fitzgerald            0.82      
7    Carter Beats the Devil                             Glen David Gold                0.81      
8    The Universe in a Nutshell                         Stephen Hawking                0.81      
9    Three Men in a Boat (Three Men, #1)                Jerome K. Jerome               0.81      
10   The Blank Slate: The Modern Denial of Human Nat... Steven Pinker                  0.81      
```

**Kelebihan dan Kekurangan Collaborative Filtering**:

**Kelebihan:**
- Dapat menemukan preferensi baru yang tidak terduga
- Tidak memerlukan informasi konten yang detil
- Memberikan rekomendasi yang personal dan beragam

**Kekurangan:**
- Memerlukan data rating yang cukup (cold-start problem)
- Kesulitan merekomendasikan buku baru yang belum memiliki banyak rating
- Memerlukan komputasi yang lebih kompleks untuk dataset besar

## Evaluation

Evaluasi model dilakukan menggunakan metrik yang sesuai dengan jenis model:

### 1. Evaluasi Content-Based Filtering

Untuk Content-Based Filtering, saya menggunakan metrik **Presisi** untuk evaluasi. Presisi mengukur persentase rekomendasi yang relevan dari total rekomendasi yang diberikan.

**Formula Presisi**:
```
Precision = (Jumlah rekomendasi relevan) / (Jumlah rekomendasi total)
```

Dalam konteks ini, sebuah rekomendasi dianggap relevan jika buku yang direkomendasikan memiliki penulis yang sama atau tag/genre yang sama dengan buku input.

Berikut adalah contoh hasil evaluasi untuk beberapa buku:

```
Rekomendasi Buku Berdasarkan: White Witch, Black Curse (The Hollows, #7)
====================================================================================================
No   Judul                                              Penulis                        Tahun     
----------------------------------------------------------------------------------------------------
1    Every Which Way But Dead (The Hollows, #3)         Kim Harrison                   2005      
2    The Good, the Bad, and the Undead (The Hollows,... Kim Harrison                   2005      
3    For a Few Demons More (The Hollows, #5)            Kim Harrison                   2007      
4    The Outlaw Demon Wails (The Hollows, #6)           Kim Harrison                   2008      
5    A Fistful of Charms (The Hollows, #4)              Kim Harrison                   2006      
6    White Witch, Black Curse (The Hollows, #7)         Kim Harrison                   2009      
7    Black Magic Sanction (The Hollows, #8)             Kim Harrison                   2010      
8    Pale Demon (The Hollows, #9)                       Kim Harrison                   2011      
9    A Perfect Blood (The Hollows, #10)                 Kim Harrison                   2012      
10   Ever After (The Hollows, #11)                      Kim Harrison                   2013      
```

Dalam contoh di atas, presisi adalah 100% karena semua rekomendasi berasal dari penulis yang sama dengan buku input.

### 2. Evaluasi Collaborative Filtering

Untuk Collaborative Filtering, saya menggunakan metrik **Root Mean Squared Error (RMSE)** yang merupakan metrik standard untuk mengevaluasi model prediksi rating.

**Formula RMSE**:
```
RMSE = sqrt(mean((predicted_ratings - actual_ratings)^2))
```

RMSE mengukur rata-rata perbedaan antara rating prediksi dan rating aktual. Nilai RMSE yang lebih rendah menunjukkan model yang lebih akurat.

Hasil evaluasi untuk model collaborative filtering:
```
Evaluasi Collaborative Filtering:
RMSE pada data validasi: 0.2987
RMSE pada skala rating asli (1-5): 1.1948
```

RMSE sebesar 1.1948 pada skala 1-5 menunjukkan bahwa rata-rata prediksi model berbeda sekitar 1.2 poin dari rating aktual. Hal ini menunjukkan hasil yang cukup baik untuk sistem rekomendasi.

![Model Metrics](https://i.ibb.co.com/hRdMM6Wy/433928666-34335261-9c36-44f2-8944-695a696182a7.png)

### Perbandingan Metrik

Meskipun kedua model menggunakan metrik evaluasi yang berbeda, kita dapat membandingkan kinerja relatif mereka:

1. **Content-Based Filtering** dengan presisi tinggi menunjukkan bahwa model ini cukup baik dalam menemukan buku yang serupa berdasarkan konten.

2. **Collaborative Filtering** dengan RMSE 1.1948 (pada skala 1-5) menunjukkan bahwa model ini dapat memprediksi rating dengan selisih kurang dari 1.2 poin dari rating aktual, yang cukup baik untuk sistem rekomendasi.

Kedua model memiliki kelebihan dan kekurangan masing-masing, dan dalam implementasi nyata, kombinasi dari kedua pendekatan (hybrid approach) akan memberikan hasil yang optimal.

## Kesimpulan

Proyek ini telah berhasil mengimplementasikan dua pendekatan sistem rekomendasi buku: Content-Based Filtering dan Collaborative Filtering. Kedua pendekatan menunjukkan kinerja yang baik dalam memberikan rekomendasi buku yang relevan bagi pengguna.

Content-Based Filtering berhasil memberikan rekomendasi berdasarkan kesamaan konten (penulis, tag/genre), sementara Collaborative Filtering dapat memprediksi rating dengan RMSE 1.1948 pada skala 1-5.

Untuk pengembangan lebih lanjut, beberapa hal yang dapat dilakukan:
- Menggunakan dataset yang lebih besar untuk meningkatkan akurasi model
- Menerapkan sistem hybrid yang menggabungkan kedua pendekatan
- Menambahkan fitur personalisasi berdasarkan riwayat baca pengguna secara real-time
- Memperbaiki masalah cold start dengan menambahkan rekomendasi buku populer untuk pengguna baru

Secara keseluruhan, sistem rekomendasi buku yang dibangun dapat membantu pembaca menemukan buku yang sesuai dengan minat mereka, meningkatkan pengalaman pengguna, dan potensial meningkatkan penjualan bagi platform buku.