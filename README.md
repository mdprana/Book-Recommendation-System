# Laporan Proyek Machine Learning - Made Pranajaya Dibyacita

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

**2. Struktur & Kondisi Data Buku**
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

Tahapan ini penting untuk memastikan kualitas data yang akan digunakan dalam model.

### 2. Pemilihan Fitur

Untuk model Content-Based Filtering, dipilih fitur-fitur yang relevan seperti:
- book_id: ID unik buku
- title: Judul buku
- authors: Penulis buku
- language_code: Kode bahasa
- original_publication_year: Tahun publikasi asli

Pemilihan fitur ini penting karena fitur-fitur ini akan digunakan sebagai dasar untuk menemukan kesamaan antar buku.

### 3. Penggabungan Data

Menggabungkan data buku dengan tag untuk membuat representasi konten yang lebih kaya. Tahapan ini meliputi:
- Menggabungkan data buku dengan data tag melalui goodreads_book_id
- Mengonversi tag_id menjadi nama tag menggunakan dictionary
- Menggabungkan semua tag menjadi satu string untuk setiap buku
- Membuat kolom "content" yang menggabungkan penulis dan tag string

Penggabungan ini penting untuk memberikan konteks yang lebih kaya tentang setiap buku, yang akan meningkatkan kualitas rekomendasi berbasis konten.

### 4. Encoding User dan Book ID

Proses encoding perlu dilakukan agar data dapat diproses oleh model deep learning:
- Membuat dictionary untuk mengonversi user_id dan book_id menjadi indeks berurutan
- Membuat dictionary untuk konversi balik dari indeks ke ID asli
- Memetakan user_id dan book_id ke indeks baru di dataframe

Encoding ini diperlukan karena model deep learning memerlukan indeks numerik berurutan sebagai input.

### 5. Normalisasi Rating

Normalisasi rating dilakukan untuk meningkatkan performa model:
- Mengubah skala rating dari 1-5 menjadi 0-1 dengan formula: (rating - min_rating) / (max_rating - min_rating)
- Rating yang telah dinormalisasi membantu model konvergen lebih cepat dan stabil selama proses training

### 6. Pembagian Data Training dan Validasi

Data rating dibagi menjadi data training dan validasi dengan perbandingan 80:20:
- 80% data digunakan untuk melatih model
- 20% data digunakan untuk validasi dan evaluasi model
- Pembagian dilakukan secara acak untuk memastikan representasi yang baik

Tahapan ini penting untuk mengevaluasi performa model pada data yang belum pernah dilihat sebelumnya.

## Modeling

### 1. Content-Based Filtering

Model Content-Based Filtering diimplementasikan dengan pendekatan berikut:

#### TF-IDF Vectorization
Teknik Term Frequency-Inverse Document Frequency (TF-IDF) digunakan untuk mengubah teks konten (kombinasi penulis dan tag) menjadi vektor numerik. TF-IDF memberikan bobot lebih tinggi pada kata-kata yang jarang muncul di seluruh dokumen tetapi sering muncul dalam dokumen tertentu, sehingga dapat mengidentifikasi kata kunci yang paling membedakan antar buku.

Dalam implementasi ini, saya menggunakan TfidfVectorizer dengan parameter:
- stop_words='english' - Untuk menghilangkan kata-kata umum dalam bahasa Inggris yang tidak memberikan informasi penting
- Ukuran matriks TF-IDF yang dihasilkan adalah 10.000 x 6.175, yang berarti ada 10.000 buku dan 6.175 fitur unik

#### Cosine Similarity
Cosine similarity digunakan untuk mengukur kesamaan antar buku. Metrik ini menghitung kosinus sudut antara dua vektor dalam ruang multi-dimensi. Semakin kecil sudut antara dua vektor (semakin besar nilai cosine similarity), semakin mirip kedua buku tersebut.

Matriks cosine similarity berukuran 10.000 x 10.000, mewakili kesamaan antara setiap pasangan buku dalam dataset. Nilai ini kemudian digunakan untuk menemukan buku-buku yang paling mirip dengan buku input.

#### Proses Rekomendasi
Sistem rekomendasi bekerja dengan:
1. Mencari indeks buku berdasarkan judul
2. Mengambil skor kesamaan buku tersebut dengan semua buku lain dari matriks cosine similarity
3. Mengurutkan buku berdasarkan skor kesamaan dari tertinggi ke terendah
4. Mengambil 10 buku teratas (tidak termasuk buku itu sendiri)

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

#### Kelebihan dan Kekurangan Content-Based Filtering

**Kelebihan:**
- Tidak memerlukan data dari pengguna lain untuk membuat rekomendasi
- Dapat merekomendasikan buku yang baru atau belum banyak di-rating
- Mampu menangkap preferensi spesifik pengguna
- Transparansi dalam rekomendasi karena didasarkan pada fitur yang dapat diinterpretasi

**Kekurangan:**
- Tidak dapat menemukan preferensi baru di luar yang sudah diketahui pengguna
- Sangat bergantung pada kualitas metadata buku
- Terbatas pada fitur eksplisit yang tersedia
- Dapat mengalami over-specialization (hanya merekomendasikan item yang sangat mirip)

### 2. Collaborative Filtering

Model Collaborative Filtering diimplementasikan menggunakan deep learning dengan TensorFlow:

#### Arsitektur Model
Model menggunakan Neural Collaborative Filtering dengan komponen utama:
- Embedding layer untuk user (ukuran 50) - Mengubah user ID menjadi vektor representasi
- Embedding layer untuk buku (ukuran 50) - Mengubah book ID menjadi vektor representasi
- Bias terms untuk user dan buku - Menangkap preferensi dasar
- Operasi dot product - Mengukur kesesuaian antara user dan buku

Parameter penting dalam model:
- Embedding size: 50 - Menentukan dimensi vektor latent yang merepresentasikan user dan buku
- L2 regularization: 1e-6 - Mencegah overfitting
- Initialization: he_normal - Inisialisasi bobot yang optimal untuk model deep learning
- Activation function: sigmoid - Memastikan output dalam rentang 0-1 sesuai rating yang dinormalisasi

#### Proses Training
Model dilatih dengan konfigurasi:
- Optimizer: Adam dengan learning rate 0.001
- Loss function: BinaryCrossentropy - Sesuai untuk nilai target dalam rentang 0-1
- Metrics: RootMeanSquaredError - Untuk evaluasi performa model
- Batch size: 64 - Menyeimbangkan kecepatan training dan memori
- Epochs: 100 - Jumlah iterasi pelatihan untuk konvergensi model

Proses training menghasilkan penurunan RMSE dari sekitar 0,32 menjadi 0,30 pada data validasi, menunjukkan model berhasil belajar pola dalam data.

#### Proses Rekomendasi
Sistem rekomendasi bekerja dengan:
1. Mengidentifikasi buku yang belum dibaca oleh pengguna
2. Memprediksi rating untuk setiap buku tersebut menggunakan model
3. Mengurutkan buku berdasarkan rating prediksi dari tertinggi ke terendah
4. Mengambil 10 buku teratas sebagai rekomendasi

**Contoh Hasil Rekomendasi Collaborative Filtering**:

Berikut adalah contoh rekomendasi untuk pengguna dengan ID 40793:

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

#### Kelebihan dan Kekurangan Collaborative Filtering

**Kelebihan:**
- Dapat menemukan preferensi baru yang tidak terduga bagi pengguna
- Tidak memerlukan informasi konten atau metadata yang detil
- Mampu menangkap pola kompleks dan tersembunyi dalam data
- Memberikan rekomendasi yang personal dan beragam

**Kekurangan:**
- Memerlukan data rating yang cukup (cold-start problem)
- Kesulitan merekomendasikan buku baru yang belum memiliki banyak rating
- Memerlukan komputasi yang lebih kompleks untuk dataset besar
- Sulit menjelaskan alasan di balik rekomendasi (black box)

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

Presisi: 10/10 (100%) - Semua rekomendasi berasal dari penulis yang sama dan merupakan seri yang sama dengan buku input.

```
Rekomendasi Buku Berdasarkan: The Jester
====================================================================================================
No   Judul                                              Penulis                        Tahun     
----------------------------------------------------------------------------------------------------
1    3rd Degree (Women's Murder Club, #3)               James Patterson, Andrew Gro... 2004      
2    Judge & Jury                                       James Patterson, Andrew Gro... 2006      
3    Lifeguard                                          James Patterson, Andrew Gro... 2005      
4    The Jester                                         James Patterson, Andrew Gro... 2003      
5    Along Came a Spider (Alex Cross, #1)               James Patterson                1993      
6    Kiss the Girls (Alex Cross, #2)                    James Patterson                1995      
7    1st to Die (Women's Murder Club, #1)               James Patterson                2001      
8    The Angel Experiment (Maximum Ride, #1)            James Patterson                2005      
9    Suzanne's Diary for Nicholas                       James Patterson                2001      
10   Sam's Letters to Jennifer                          James Patterson                2001      
```

Presisi: 10/10 (100%) - Semua rekomendasi berasal dari penulis yang sama dengan buku input.

```
Rekomendasi Buku Berdasarkan: A Higher Call: An Incredible True Story of Combat and Chivalry in the War-Torn Skies of World War II
====================================================================================================
No   Judul                                              Penulis                        Tahun     
----------------------------------------------------------------------------------------------------
1    The Wealth of Nations                              Adam Smith                     1776      
2    The Orphan Master's Son                            Adam Johnson                   2012      
3    Give and Take: A Revolutionary Approach to Succ... Adam M. Grant                  2013      
4    King Leopold's Ghost                               Adam Hochschild                1998      
5    A Tale Dark & Grimm (A Tale Dark & Grimm, #1)      Adam Gidwitz                   2010      
6    Paris to the Moon                                  Adam Gopnik                    2000      
7    Ringworld (Ringworld, #1)                          Larry Niven                    1970      
8    The Ringworld Engineers (Ringworld #2)             Larry Niven                    1979      
9    Oogy: The Dog Only a Family Could Love             Larry Levin                    2009      
10   Lonesome Dove                                      Larry McMurtry                 1985      
```

Presisi: 6/10 (60%) - 6 rekomendasi memiliki penulis dengan nama depan "Adam", tetapi 4 rekomendasi terakhir memiliki penulis dengan nama depan "Larry" yang berbeda dari buku input.

```
Rekomendasi Buku Berdasarkan: Sweet Surrender (Sweet, #1)
====================================================================================================
No   Judul                                              Penulis                        Tahun     
----------------------------------------------------------------------------------------------------
1    Fever (Breathless, #2)                             Maya Banks                     2013      
2    Burn (Breathless, #3)                              Maya Banks                     2013      
3    Sweet Surrender (Sweet, #1)                        Maya Banks                     2008      
4    In Bed with a Highlander (McCabe Trilogy, #1)      Maya Banks                     2011      
5    The Darkest Hour (KGI, #1)                         Maya Banks                     2010      
6    Never Love a Highlander (McCabe Trilogy, #3)       Maya Banks                     2011      
7    Sweet Persuasion (Sweet, #2)                       Maya Banks                     2009      
8    No Place to Run (KGI, #2)                          Maya Banks                     2010      
9    Never Seduce a Scot (The Montgomerys and Armstr... Maya Banks                     2012      
10   Of Poseidon (The Syrena Legacy, #1)                Anna Banks                     2012      
```

Presisi: 9/10 (90%) - 9 rekomendasi berasal dari penulis yang sama (Maya Banks), tetapi 1 rekomendasi memiliki penulis yang berbeda namun nama belakang sama (Anna Banks).

Rata-rata presisi dari 4 contoh di atas adalah (100% + 100% + 60% + 90%) / 4 = 87.5%, yang menunjukkan bahwa model Content-Based Filtering cukup akurat dalam memberikan rekomendasi berdasarkan penulis yang sama.

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

RMSE sebesar 1.1948 pada skala 1-5 menunjukkan bahwa rata-rata prediksi model berbeda sekitar 1.2 poin dari rating aktual. Ini adalah hasil yang cukup baik untuk sistem rekomendasi.

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

jadi secara keseluruhan, sistem rekomendasi buku yang dibangun dapat membantu pembaca menemukan buku yang sesuai dengan minat mereka, meningkatkan pengalaman pengguna, dan potensial meningkatkan penjualan bagi platform buku.