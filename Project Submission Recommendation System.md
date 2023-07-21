# Laporan Proyek Machine Learning -  Sistem Rekomendasi _Content Based Filtering_ untuk _Database_ Anime dari Komunitas MyAnimeList

#### Penulis: Billy Akbar Prabowo

 Proyek ini ditulis untuk pemenuhan _submission_ kedua pada tema _recommendation system_. Proyek ini membahas mengenai model rekomendasi sistem menggunakan metode _Content Based Filtering_ atau CBF pada data kumpulan anime dari Komunitas Virtul MyAnimeList. Proyek ini memiliki _output_ untuk mampu menentukan top-N rekomendasi anime bagi para pengguna.

## Domain Proyek

<br>

![099709400_1424167480-detectiveconan-851](https://github.com/b111y/anime-recommendation-list/assets/84972036/b129a95e-034c-4566-9c56-6577c10bffc9)

**[Gambar 1: Anime Serial Detective Conan](https://www.bola.com/ragam/read/4280247/28-kata-kata-keren-serial-detective-conan-penuh-makna)**

<br>

Disadur dari Wikipedia, animasi didefinisikan sebagai film yang merupakan karya tangan atau gambar yang bergerak. Pada awalnya, animasi dibuat dari berlembar-lembar kertas gambar yang dikipaskan secara cepat sehingga muncul efek gambar bergerak [1]. Pada perkembangannya, animasi telah bertransformasi dari dua dimensi menjadi tiga dimensi karena bantuan teknologi dan grafika komputer. Di masa kini, animasi telah menjadi salah satu kebudayaan yang melekat di masyarakat dan menjadi bisnis miliaran rupiah.

Di Jepang, animasi disebut dengan アニメーション (animeshon) atau disingkat dengan アニメ(anime). Saat ini, anime telah menjadi bisnis miliaran Yen yang perkembangannya dimulai dari pertama kali rilis serial kartun _Norakuro_ pada tahun 1931 [2]. Saat ini anime dapat disaksikan dimana saja terutama sejak munculnya _streaming video platform_ secara gratis atau berbayar seperti Netflix, YouTube, Hotstar, Crunchyroll, dan lainnya. Namun, adanya puluhan ribuan judul yang beredar saat ini sangatlah besar dan membuat para pengguna merasa kewalahan untuk menentukananime yang sesuai dengan selera mereka. 

Atas dasar itu, proyek ini akan memberikan rekomendasi anime berdasarkan kemiripan genre dari anime yang menjadi referensi. Rekomendasi tersebut akan menggunakan algoritma _Content Based Filtering_ atau CBF yang menghasilkan top-N rekomendasi anime dan dievaluasi berdasarkan nilai presisi. Dengan ada studi ini, maka diharapkan waktu yang diperlukan oleh pengguna bisa lebih singkat dan dapat membawa keuntungan bagi _developer_ sebagai penyedia jasa.

## Business Understanding
Berdasarkan dari latar belakang tersebut, maka pada laporan ini akan mencakup beberapa aspek berikut, mencakup:

### Problem Statements
- Bagaimana hasil rekomendasi Top-N dari algoritma CBF dari genre anime yang sama?
- Bagaimana hasil evaluasi pemodelan CBF?
  
### Goals
- Mampu menghasilkan rekomendasi Top-N dari algoritma CBF berdasarkan genre anime yang sama.
- Mampu menghitung presisi algoritma  CBF dan sesuai dengan rekomendasi anime di genre yang sama.

### Solution statements
- Menganalisis dan memahami data dengan melakukan EDA dan visualisasi.
- Menyiapkan data agar bisa digunakan dalam membangun model (menghilangkan _missing value_)
- Melakukan pengembangan model dengan algoritma CBF serta melakukan penilaian presisi.

## Data Understanding
_Dataset_ yang digunakan dalam proyek ini merupakan data _tweet_ pada Pemilihan Gubernur DKI Jakarta 2017 yang dapat diunduh di  [Kaggle : Anime Database for Recommendation system](https://www.kaggle.com/datasets/vishalmane109/anime-recommendations-database).

**Tabel 1: Informasi mengenai _dataset_**
| Jenis                  | Keterangan                                                                                                        |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Sumber                 |  [Kaggle : Anime Database for Recommendation system](https://www.kaggle.com/datasets/vishalmane109/anime-recommendations-database)                   |
| Linsensi               |  CC0: Public Domain                                                                                               |
| Kategori               | Anime & Manga                                                                                                            |
| Jenis & Ukuran berkas  | CSV (9.2 MB)                                                                                                       |  
| Nama dataset           | Anime_data.csv                                                                      |  
| Jumlah data            | 15 kolom, 17002 baris                                        |  
  
### Variabel-variabel pada _dataset_:
Walaupun ada 15 kolom yang masing-masing berisi 15 data berbeda (seperti tipe, genre, rating, episode, dan yang lainnya), namun terdapat 5 variabel utama yang digunakan pada proyek ini pada EDA maupun simulasi CBF, yaitu sebagai berikut:

**Tabel 2: Informasi mengenai variabel**
| Variabel                  | Keterangan                                                                                                        |
| --------------------------| ----------------------------------------------------------------------------------------------------------------- |
| Rating                 | Penilaian rata-rata anime dari pengguna yang nilainya dari 0-10             |
| Title                | Judul anime              |
| Type                 | Tipe anime (film/seri)            |
| Members                | Jumlah pengguna yang menambahkan anime tersebut pada daftar tontonan mereka              |
| Genre                 | Genre anime             |


## _Data Preparation_
Sebelum memulai pengolahan data, maka sebelumnya diperlukan beberapa tahapan seperti berikut:
- Mengimpor library dengan kode `import`
- Menghubungkan Google Colab dihubungkan dengan sumber _dataset_ (pada kasus ini _dataset_ disimpan pada Google Drive) dengan kode `import()`
- Membaca _dataset_ dengan kode `pd.read_csv()`
  <br>
  
  Setelah tanda baca, tautan, dan simbol yang tidak perlu telah dikurangi, maka data eksisting akan terlihat seperti gambar di bawah ini
**Tabel 3: Cuplikan _dataset_**
  <br>
  
| Anime_id  	| Title  	|  Genre 	|  Synopsis 	|  Type 	| Producer  	| Studio  	|  Rating							 	| ScoredBy  	| Popularity  	|  Members 	| Episodes  	| Source  	| Aired  	| Link  	|
|---	|---	|---	|---	|---	|---	|---	|---	|---	|---	|---	|---	|---	|---	|---	|
|  1 	| Cowboy Bebop|  ['Action', 'Adventure', 'Comedy', 'Drama', 'Sc... 	|  In the year 2071, humanity has colonized sever... 	|  TV 	|  ['Bandai Visual'] 	|  ['Sunrise'] 	|  8.81 	| 363889.0  	| 39.0  	| 704490.0  	| 26.0  	|  Original 	|  Apr 3, 1998 to Apr 24, 1999 	|  https://myanimelist.net/anime/1/Cowboy_Bebop 	|
|  5	 |  Cowboy Bebop: Tengoku no Tobira		| ['Action', 'Space', 'Drama', 'Mystery', 'Sci-Fi']	  	| Another day, another bounty—such is the life o...	  	| Movie	   	|  ['Sunrise', 'Bandai Visual']	 	| ['Bones']	  	| 8.41	  	| 111187.0	 	|475.0	   	| 179899.0	   	|1.0	   	| Original	  	|  Sep 1, 2001	 	| https://myanimelist.net/anime/5/Cowboy_Bebop__...  	|
| 6	  	|  Trigun	 	| ['Action', 'Sci-Fi', 'Adventure', 'Comedy', 'D...	  	|  Vash the Stampede is the man with a $$60,000,0...	 	| TV	 	| ['Victor Entertainment']	  	|   ['Madhouse']	 	|  8.31 	| 	197451.0  	| 158.0 	 	| 372709.0	  	| 26.0	 	  	| Manga 	| 	Apr 1, 1998 to Sep 30, 1998	  	|  https://myanimelist.net/anime/6/Trigun 	|

<br>

- Melihat bentuk/shape data untuk mengetahui kolom dan baris pada _dataset_
- Mendeteksi _missing_ value dengan cara menggunakan kode `df.isna().sum`
Pengecekan _missing value_ menghasilkan data seperti tabel berikut:
<br>

**Tabel 4: Tabel kolom dan _missing value_**

  | Kolom  	| Jumlah _missing value_  	|
|---	|---	|
| Anime_id          	|   0 	|
|  Title           |  0 	|
| Genre        	| 2012  	|
|  Synopsis      	|  1419 	|
|  Type           | 634  	|
|  Producer     	|   9367 	|
|  Studio        |  9083 	|
| Rating          	|  2577 	|
| ScoredBy        	|  3775 	|
|  Popularity      	|  634 	|
|  Members           	|  0 	|
|   Episodes      	| 2917  	|
|  Source         	| 1927  	|
|   Aired          	| 634  	|
|   Link          	| 634  	|


- Menghilangkan _missing value_ pada _dataset_ menggunakan kode `df=df.dropna()`
- Mengecek kolom dan jumlah _missing value_, pada kasus ini data sudah bersih dan tidak mengandung _missing value_ seperti tabel di bawah ini,
<br>

**Tabel 5: Tabel kolom dan _missing value_ setelah menghapus _missing value_**

  | Kolom  	| Jumlah _missing value_  	|
|---	|---	|
| Anime_id          	|   0 	|
|  Title           |  0 	|
| Genre        	| 0  	|
|  Synopsis      	|  0 	|
|  Type           | 0  	|
|  Producer     	|   0 	|
|  Studio        |  0 	|
| Rating          	|  0 	|
| ScoredBy        	|  0 	|
|  Popularity      	|  0 	|
|  Members           	|  0 	|
|   Episodes      	| 0  	|
|  Source         	| 0  	|
|   Aired          	| 0  	|
|   Link          	| 0  	|
<br>


- Mengecek data type dari tiap kolom dengan kode `df.dtypes` dan mengubah tipe data kolom Genre menjadi string dengan kode `df['Genre'] = df['Genre'].astype('str')`
- Menghilangkan tanda baca maupun simbol yang tidak perlu dengan tujuan untuk mengurangi _noise_
- Melakukan _Exploratory Data Analysis_ atau EDA dengan melihat data statistik dari _dataset_ yang telah dimodifikasi. Dari data tersebut diperoleh bahwa rating tertinggi ialah 10.00 dan rating terendah adalah 2.33, dimana _members_ tertinggi dalam suatu anime mencapai 1.451.708 pengguna!
- Melihat rerata _rating_ menggunakan _bar chart_, dari gambar di bawah dapat disimpulkan bahwa rating 7 merupakan frekuensi tertinggi dari _dataset_.

![Gambar 2: Analisis distribusi rating anime](https://github.com/b111y/anime-recommendation-list/assets/84972036/55a695d9-9504-4a5d-aeee-fa77fecb5747)

**Gambar 2: Analisis distribusi rating anime menggunakan _bar chart_**
<br>

- Melihat jumlah tipe anime yang ada di komunitas MyAnimeList menggunakan _bar chart_. Dari data tersebut dapat dilihat bahwa frekuensi terbanyak tipe anime adalah TV, dan diikuti oleh OVA, Movie, dan yang lainnya.
- 
![image](https://github.com/b111y/anime-recommendation-list/assets/84972036/b43ddd39-7a24-4f8e-bb4c-d05f611b535b)

**Gambar 3: Analisis distribusi tipe anime menggunakan _bar chart_**

- Mengecek anime dengan member tertinggi menggunakan kode `df.sort_values`, diperoleh bahwa top 3 anime dengan member tertinggi adalah Death Note, Shingeki no Kyojin, dan Sword Art Online.
- Mengecek anime dengan rating tertinggi menggunakan kode `df.sort_values`, diperoleh bahwa top 3 anime dengan rating tertinggi adalah Death Note, Shingeki no Kyojin, dan Sword Art Online.
-  Mengaplikasikan _Term Frequency-Inverse Document Frequency (TF-IDF)_ `TfidfVectorizer()` yang akan menilai dan melakukan tokenisasi dan digunakan untuk mengetahui frekuensi suatu kata muncul di dalam dokumen pada _dataset_.


## Modeling
Pada tahap _modeling_, data yang telah dipreparasi akan diuji dengan metode CBF. Metode CBF berusaha untuk menghitung atau menentukan rekomendasi berdasarkan kemiripan [3]. Jika suatu konten atau anime memiliki suatu karakteristik yang sama dengan anime lainnya, maka algoritma CBF akan mengidentifikasi bahwa kedua anime tersebut mirip. Algoritma ini memiliki kelebihan yaitu mudah diaplikasikan, memudahkan calon pengguna yang baru saja mengakses atau mencari suatu hal yang baru, CBF juga menghendaki independensi pengguna melalui data historikal dan rating eksklusif yang digunakan oleh pengguna aktif untuk membangun profil mereka sendiri [3]. Namun, CBF juga memiliki limitasi seperti erbatasnya rekomendasi hanya pada item-item yang mirip sehingga tidak ada kesempatan untuk mendapatkan item yang tidak terduga [3].

Selain CBF, maka salah satu metode yang digunakan dalam pemrosesan data ialah _Term Frequency-Inverse Document Frequency_ atau TF-IDF. Metode ini berguna untuk menghitung bobot setiap kata yang umum digunakan dan melakukan tokenisasi di setiap dokumen dalam korpus. Maka dari itu, metode TF-IDF digunakan untuk mengetahui berapa sering suatu kata muncul di dalam dokumen. Peningkatan nilai TF-IDF secara linear sesuai dengan jumlah kemunculan kata tersebut. Setelah itu, hasil TF-IDF tersebut ditransformasikan dalam bentuk matriks dengan fungsi `todense()`.

Pada konsepnya, jika matriks TF-IDF memiliki nilai matriks yang semakin tinggi, maka semakin sama atau mirip hubungan antara anime dengan genre atau anime tersebut dengan anime pembanding. Pada tabel di bawah ini, setelah dilakukan pemetaan matriks antara genre dan judul anime dengan bantuan fungsi `todense()`, anime _Wild Arms: Twillight Venom_ memiliki nilai matriks sebesar 0.323 pada genre _fantasy_ dan 0.324 pada genre _adventure_. Hal tersebut dapat menunjukkan bahwa kemungkinan genre yang dimiliki oleh anime tersebut mirip dengan genre _fantasy_ dan _adventure_. 
<br>
**Tabel 6: Hasil TF-IDF dan fungsi _todense_ menghasilkan pemetaan antara judul anime dan genre anime**

|  Judul/Genre 	| fantasy  	| adventure  	|
|---	|---	|---	|
| Wild Arms: Twilight Venom  	| 0.323314  	| 0.324773	  	|
|  Donburi Kazoku 	|  0.000000 	| 0.000000  	|

Setelah dapat memetakan tendensi kemiripan antara judul dan genre, maka untuk dapat memetakan antara satu judul anime dengan judul lainnya dan menilai kemiripannya, diperlukan metode _cosine similarity_. Metode ini menghitung dua sudut antara dua objek. Metode ini membandingkan kedua objek tersebut pada skala normal dan menemukan _dot product_ antara dua objek tersebut [4]. Metode _cosine similarity_ dapat diilustrasikan pada gambar di bawah ini. Dari gambar tersebut, dapat dilihat pada ketika dua objek tersebut memiliki kosinus sudut yang kecil, maka diasumsikan bahwa objek tersebut memiliki kemiripan, dan berlaku sebaliknya.  Metode ini memiliki kelebihan yaitu akurasi tinggi dan mudah diimplementasikan.

![image](https://github.com/b111y/anime-recommendation-list/assets/84972036/7e2fcba0-63de-4faf-9ec6-384132d3d4ae)

**Gambar 4: Ilustrasi _Cosine simlarity_**

Fungsi _cosine similarity_ dapat dilakukan dengan cara menghitung dari matriks TF-IDF dengan kode `cosine_similarity(tfidf_matrix)`. Dalam kasus ini, setelah menjalankan fungsi tersebut, maka akan muncul matriks judul referensi dan judul pembanding. Nilai kosinus yang muncul di dalam matriks berkisar antara 0 hingga 1. Semakin tinggi nilai kosinus, maka semakin kecil sudutnya, yang menjadikan bahwa judul referensi dan judul pembanding memiliki tendensi yang sama dalam hal genre. Berikut cuplikan tabel hasil implementasi fungsi _cosine similarity_.
<br>
**Tabel 7: Matriks kosinus _cosine similarity_**

|  Judul 	| Hanitarou Desu  	| Hiiro no Kakera: Totsugeki! Tonari no Ikemenzu  	|
|---	|---	|---	|
| Hanamaru Youchien  	|  0.836710	  	| 0.104588	  	|
|  Amanchu! 	|  0.813499	 	| 0.101686  	|

<br>

Dari tabel di atas, dapat disimpulkan bahwa Anime _Hanamaru Youchien_, _Hanitarou Desu_ dan _Amanchu!_ memiliki kemiripan yang tinggi dalam hal genre. Percobaan selanjutnya adalah memasukkan judul anime referensi untuk mencari Top-5 rekomendasi anime dalam hal genre. Dalam kasus ini, anime berjudul _Kogepan_ yang memiliki genre _Comedy_ akan menjadi judul anime referensi dengan informasi anime tersebut di tabel berikut,
<br>
**Tabel 8: Judul anime referensi yang akan dicarikan top-5 rekomendasi anime berdasarkan genre**

| Judul  	| Genre  	|
|---	|---	|
| Kogepan          	|   Comedy 	|

<br>

Setelah itu, Top-5 rekomendasi anime _Kogepan_ menghasilkan tabel di bawah ini,

**Tabel 9: Hasil top-5 rekomendasi anime berjudul _Kogepan_**

| Judul  	| Genre  	|
|---	|---	|
| Yagami-kun no Katei no Jijou          	|   Comedy 	|
| Osomatsu-kun          	|   Comedy 	|
| Sword Art Online II: Sword Art Offline II	          	|   Comedy 	|
| Tensai? Dr. Hamax          	|   Comedy 	|
| Osomatsu-kun (1988): Appare! Chibita no Onitai...          	|   Comedy 	|

<br>

Dapat dilihat dari tabel di atas, bahwa rekomendasi anime _Kogepan_ menghasilkan 5 judul anime dengan genre yang sama, yaitu  _Comedy_. Jadi, jika pengguna menyukai anime _Kogepan_, sistem rekomendasi akan merekomendasikan film di atas karena kesamaan genre.

## Evaluation
Percobaan menggunakan model CBF yang menggunakan metode _cosine similarity_ memiliki nilai presisi yang dapat dituliskan pada rumus di bawah ini,

$\text{Presisi} = \frac{Jumlah-item-rekomendasi-relevan}{Jumlah-semua-item-yang-direkomendasikan}$

<br>

Dengan rumus tersebut, maka dihasilkan bahwa presisi dalam menentukan rekomendasi anime _Kogepan_ berdasarkan genre adalah 5/5 atau 100%. Maka dari itu, pada kasus ini, penggunaan metode CBF efektif dan selektif dalam menentukan rekomendasi judul anime yang diinginkan oleh pengguna.

Dari percobaan ini maka dapat disimpulkan beberapa hal yaitu:
- Rekomendasi anime _Kogepan_ yang memiliki genre _Comedy_ menghasilkan 5 judul rekomendasi anime yang memiliki genre _Comedy_ yaitu _Yagami-kun no Katei no Jijou, Osomatsu-kun, Sword Art Online II: Sword Art Offline II,Tensai? Dr. Hamax,_ dan _Osomatsu-kun (1988): Appare! Chibita no Onitai..._.
- Perhitungan dengan algoritma CBF dan _cosine similarity_ memperoleh presisi sebesar 100.00% karena semua anime yang direkomendasikan memiliki genre yang sama. Maka dari itu, metode ini dapat dinilai efekti dalam merekomendasikan sesuatu yang memiliki hal serupa.

## Referensi
- [1] [Wikipedia - Ensiklopedia Bebas - Animasi](https://id.wikipedia.org/wiki/Animasi)
- [2] [Napier, S. (2011). Manga and anime: entertainment, big business, and art in Japan. In Routledge handbook of Japanese culture and society (pp. 226-237). Routledge.](https://books.google.co.id/books?hl=id&lr=&id=0cBYffHp5L4C&oi=fnd&pg=PA226&dq=manga+and+anime+%22big+business%22&ots=VL1yttTe-w&sig=Gf7zHra0GerfiSF8rAZ14xXl8io&redir_esc=y#v=onepage&q=manga%20and%20anime%20%22big%20business%22&f=false)
- [3] [Thorat, P. B., Goudar, R. M., & Barve, S. (2015). Survey on collaborative filtering, content-based filtering and hybrid recommendation system. International Journal of Computer Applications, 110(4), 31-36.](https://d1wqtxts1xzle7.cloudfront.net/59762468/10.1.1.695.642820190617-91457-z4s1rf-libre.pdf?1560756140=&response-content-disposition=inline%3B+filename%3DSurvey_on_Collaborative_Filtering_Conten.pdf&Expires=1689907791&Signature=RcQt28Rooly-CujETlxs5seOX-yqDMkudDpd1Ei1Oji8QSr0~AosZv-gpsNKwqQxy~XoFLM2skS-w6WS77o326EQS3exdUzZxWagtWj7uuszgYyCIg9~CLMCyiuCVdsie41soI4j3var8x6n8CpkyhJ0hlDd-cj-prLRCIOlMaxsxblDrfpvOdqd~ocLb4O~DJChoeT1DSlxxbbywIzoxzGtOQztbyllK7Re86~kWGmLPNe1dErtOx3I2XCPbMdwr2DHegMfneptqHQmcT3a3mEtYLz5-217-zjCsq5A54y2w3DWYCCG7zilWu5vjTRIW~APaJbMmAHUxROJmo1WzA__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA)
- [4] [Singh, R. H., Maurya, S., Tripathi, T., Narula, T., & Srivastav, G. (2020). Movie recommendation system using cosine similarity and KNN. International Journal of Engineering and Advanced Technology, 9(5), 556-559.](http://www.edu.dmomeni98.ir/papers/Movie%20Recommendation%20System%20using%20cosine%20similarity%20and%20knn_2020.pdf)
