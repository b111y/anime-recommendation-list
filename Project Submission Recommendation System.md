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
- Menghilangkan tanda baca, tautan, maupun simbol yang tidak perlu (karakter selain pembentuk kata) dengan tujuan untuk mengurangi _noise_ pada model dengan kode `data['Text Tweet'].apply(lambda x: re.sub(r'http\S+', '', x))` dan `data['Text Tweet'].apply(lambda x: re.sub(r'[^a-zA-Z0-9\s]', '', x))`

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

-  
- Melakukan visualisasi data menggunakan pie chart dan WordCloud untuk melihat banyaknya _dataset_ yang memiliki sentimen baik positif maupun negatif. Untuk visualisasi _pie chart_ dapat menggunakan kode `plt.pie()`. Didapatkan hasil bahwa untuk _dataset_ tersebut memiliki 50% sentimen positif dan 50% sentimen negatif sesuai gambar di bawah ini,

<br>

![image](https://github.com/b111y/politic-sentiment-analysis/assets/84972036/b3fb8ad0-1d31-47df-80e0-bfd4d6e3ebab)


<br>


- Melakukan pembagian _dataset_ menjadi dua yaitu data uji dan data latih dengan data uji sebesar 20% untuk masing-masing tipe sentimen. Pembagian _dataset_ menggunakan kode `train_test_split()` dan diperoleh bahwa data pelatihan dan data uji sejumlah berikut:
<br>

| Aspek  |  Jumlah Data Pelatihan | Jumlah Data Uji | Jumlah    |
|-----   |------------------------|----------------	|-----------|
|  Positif 	|  360 	| 90  	| 450 |
|  Negatif 	|  360 	| 90   | 450 |

<br>

- Mengaplikasikan _Term Frequency-Inverse Document Frequency (TF-IDF)_ `TfidfVectorizer()` yang akan menilai dan melakukan tokenisasi dan digunakan untuk mengetahui frekuensi suatu kata muncul di dalam dokumen pada data yang sudah dibagi menjadi data uji dan data pelatihan. Setelah mengimplementasikan TF-IDF, dilakukan pengecekan dataframe setelah dilakukan ekstraksi dengan metode TF-IDF yang tergambar pada gambar dibawah ini,

<br>

![image](https://github.com/b111y/politic-sentiment-analysis/assets/84972036/82d7ff61-85a1-42e1-8d2a-b4af1fae3809)

<br>

- Melakukan EDA dengan bantuan visualisasi WordCloud dan bar-chart untuk kata-kata yang sering muncul pada data yang telah dibagi menjadi data pelatihan dan data uji. Visualisasi WordCloud dapat dilakukan dengan kode `WordCloud()` dan untuk bar-chart dapat menggunakan kode  `plt.bar`. Hasil menunjukkan bahwa 5 kata tertinggi yang sering muncul adalah **ahy, ahokdjarot, yang, aniessandi**, dan **ahok** sesuai pada gambar di bawah ini,

![image](https://github.com/b111y/politic-sentiment-analysis/assets/84972036/e7f016e6-60d5-4bb1-a5d9-aa80651ce33f)

![image](https://github.com/b111y/politic-sentiment-analysis/assets/84972036/05bb01f9-49f6-489d-b073-20c3e0eccaef)

## Modeling
Pada tahap _modeling_, data yang telah dipreparasi akan diuji dengan metode _Naive Bayes_ dan metode _confusion matrix_. Metode algoritma _Naive Bayes_ metode yang dapat memprediksi kelas/kategori probabilitas keanggotaan, seperti probabilitas bahwa sampel yang diberikan milik kelas/kategori tertentu [3]. Metode ini didasarkan pada teorema Bayes yang mengasumsikan bahwa peluang dari 2 kejadian terjadi saling memengaruhi. Maka dari itu, metode _Naive Bayes_ mampu mengasumsikan probabilitas ketika user sudah mengetahui probabilitas tertentu lainnya. Metode ini terkenal mudah dan sederhana. Di sisi lain, walaupun metode ini dapat mengasumsikan probabilitas ketika user sudah dapat mengetahui probabilitas tertentu lainnya, jika probabilitas kondisionalnya nol maka prediksi akan bernilai nol juga. Algoritma _Naive Bayes_ akan menghasilkan akurasi yang dapat dihitung dari jumlah data yang diklasifikan dengan benar dibagi dengan jumlah semua data yang diklasifikasikan atau dapat ditulis pada rumus berikut:
<br>
$\text{Accuracy} = \frac{correctly-classified-items}{all-classified-items}$
<br>

_Confusion matrix_ merupakan metode evaluasi model dalam melakukan klasifikasi yang terdiri dari ringkasan tabel jumlah perdiksi yang benar dan salah dengan 4 matriks nilai, yaitu _True Positive (TP), True Negative (TN), False Positive (FP)_, dan _False Negative (FN)_. Suatu model _confusion matrix_ dapat dikatakan bagus jika memiliki nilai _True Positive_ dan _True Negative_ yang tinggi.

Terdapat beberapa aspek parameter untuk _confusion matrix_, yaitu presisi, _recall_, dan F1. Presisi adalah pembagian antara TP dengan (TP+FP) atau perbandingan hasil yang positif secara benar dengan semua data yang dikategorikan sebagai positif, atau dapat ditulis pada rumus berikut:
<br>
$\text{Presisi} = \frac{TP}{TP+FP}$
<br>

Sedangkan _recall_ adalah pembagian antara TP dengan (TP+FN) atau pembagian hasil yang positif secara benar dengan penjumlahan semua hal yang seharusnya dikategorikan positif, atau dapat diekspresikan dengan rumus berikut:
<br>
$\text{Recall} = \frac{TP}{TP+FN}$
<br>


Terakhir, skor F1 diperoleh dari perkalian dari perbandingan (presisi x _recall_) dengan (presisi + _recall) yang hasilnya dikali 2, atau diekspresikan pada rumus berikut:
<br>
$\text{F1} = \frac{2 \* Precision \* Recall}{Precision+Recall}$    
<br>

Dari aspek-aspek di atas, maka masing-masing nilainya akan dibandingkan untuk mengukur ketepatan antarmodel. Jika nilai mendekati 100% maka dapat dikatakan model tersebut semakin baik.

## Evaluation
Percobaan menggunakan model _Naive Bayes_, nilai akurasi diukur dengan rumus pada bab Modeling. Setelah dijalankan, maka didapatkan akurasi sebesar 75.56%. Dari hasil ini dapat diindikasikan bahwa ada kemungkinan salah klasifikasi pada _tweet_ karena adanya kemungkinan untuk _False Positive_ maupun _False Negative_. Maka dari itu, _confusion matrix_ dapat membantu untuk evaluasi model dan melihat nilai pengukuran lain (yaitu presisi, _recall_, dan skor F1).

Implementasi model _confusion matrix_ yang menghasilkan matriks 2x2, sebagai berikut dan nilai presisi, _recall_, dan skor F1. Dari hasil matriks 2x2, hasil yang diperoleh untuk TP dan TN memiliki nilai yang relatif tinggi. Hal ini membuktikan bahwa model ini sudah lumayan bagus untuk melakukan klasifikasi sentimen. Selain itu, didapatkan juga nilai presisi, _recall_, dan skor F1. Nilai presisi pada model ini adalah 76.02%, atau sedikit lebih tinggi dengan akurasi _Naive Bayes_. Sedangkan diperoleh nilai _recall_ adalah 75.56% atau sama dengan akurasi pada _Naive Bayes_. Terakhir,diperoleh F1 sebesar 75.45% yang hasilnya hampir mirip dengan _recall_ dan akurasi _Naive Bayes_.

Hasil percobaan tersebut dapat disimpulkan pada tabel di bawah ini,

|  Aspek 	|  Metode 	| Hasil (%)  	|
|---	|---	|---	|
|  Akurasi 	|  _Naive Bayes_ 	|  75.56 	|
|  Presisi 	|  _Confusion Matrix_ 	|  76.02 	|
|  _Recall_ 	|  _Confusion Matrix_ 	|  75.56 	|
|  F1 	|  _Confusion Matrix_ 	|  75.45 	|

Dari percobaan ini maka dapat disimpulkan beberapa hal yaitu:
- Preparasi data dilakukan dengan cara menghilangkan tanda baca, simbol, dan tautan untuk menghindari _noise_ serta TF-IDF
- Pemodelan dilakukan dengan metode _Naive Bayes_ yang memperoleh akurasi 75.56% dan _confusion matrix_ yang memperoleh nilai presisi sebesar 76.02%, nilai _recall_ sebesar 75.56%, dan F1 sebesar 75.45%.
- Kedua metode tersebut memiliki akurasi yang relatif tinggi dan hasil yang serupa.
  
## Referensi
- [1] [Wikipedia - Ensiklopedia Bebas - Animasi](https://id.wikipedia.org/wiki/Animasi)
- [2] [Napier, S. (2011). Manga and anime: entertainment, big business, and art in Japan. In Routledge handbook of Japanese culture and society (pp. 226-237). Routledge.](https://books.google.co.id/books?hl=id&lr=&id=0cBYffHp5L4C&oi=fnd&pg=PA226&dq=manga+and+anime+%22big+business%22&ots=VL1yttTe-w&sig=Gf7zHra0GerfiSF8rAZ14xXl8io&redir_esc=y#v=onepage&q=manga%20and%20anime%20%22big%20business%22&f=false)
- [3] [Leung, K. M. (2007). Naive bayesian classifier. Polytechnic University Department of Computer Science/Finance and Risk Engineering, 2007, 123-156.](https://cse.engineering.nyu.edu/~mleung/FRE7851/f07/naiveBayesianClassifier.pdf)
