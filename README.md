# Segmentasi dan Pola Nilai Rumah di California: Analisis Eksploratif dengan PCA dan K-Means Clustering

Repositori ini berisi *notebook* kode analisis (`.ipynb`) dan laporan lengkap (`.pdf`) untuk **Tugas Individu dan Tes Tengah Semester (TTS)** mata kuliah **Analisis Data Eksploratif** (Program Studi Teknik Informatika, FTI UKSW)[cite: 2, 3].

---

## 🎯 Mengapa Penelitian Ini Dilakukan?

Analisis ini dilakukan untuk membedah faktor-faktor utama yang memengaruhi harga properti sekaligus memetakan karakteristik segmen hunian di California[cite: 2, 5]. Dataset sensus perumahan California dipilih karena merepresentasikan tantangan data dunia nyata yang komprehensif[cite: 5]:

1. **Mengatasi Multikolinearitas & Kompleksitas Variabel:** Dataset memiliki banyak variabel numerik yang saling berkorelasi sangat kuat (sehingga ideal untuk reduksi dimensi dengan **PCA**) serta variabel kategorik untuk pengujian asosiasi dan beda kelompok (**Chi-Square** dan **ANOVA**)[cite: 5].
2. **Menangani Masalah Kualitas Data Nyata:** Data mengandung *missing value*, distribusi miring ekstrem (*skewed*), *outlier*, serta penyensoran nilai batas atas (*censoring*) yang menuntut *pipeline data preparation* yang tervalidasi secara empiris[cite: 5].
3. **Skala Sampel yang Stabil:** Dengan lebih dari 20.000 blok sensus, hasil uji statistik, reduksi dimensi, dan pengelompokan (*clustering*) memiliki stabilitas yang tinggi[cite: 5].
4. **Relevansi Praktis & Strategis:** Memahami pola harga rumah dan beban keterjangkauan hunian memberikan dasar keputusan yang konkret bagi pengembang properti, lembaga pembiayaan, maupun pembuat kebijakan perumahan sebelum melangkah ke pemodelan prediktif[cite: 5, 17].

---

## 📂 Sumber Dataset

* **Nama Dataset:** California Housing Prices (Data Sensus Penduduk California 1990 — Pace & Barry, 1997)[cite: 2, 5]
* **Dimensi:** 20.640 blok sensus (*block group*) dan 10 variabel (9 numerik, 1 kategorik)[cite: 5]
* **Dataset: ([california housing](https://github.com/ageron/handson-ml2/tree/master/datasets/housing))*[cite: 2, 5]

---

## 📊 Hasil Utama Analisis

### 1. Validasi Pipeline Data Preparation
* **Akurasi Imputasi:** Pengisian 207 *missing value* pada `total_bedrooms` menggunakan rasio terhadap `households` terbukti paling akurat melalui simulasi *data masking* 5% (**MAE = 33,3** dibanding **268,1** pada median global)[cite: 6, 7].
* **Pembersihan & Transformasi:** Sebanyak 83 baris anomali rasio hunian dihapus dan 1.763 *outlier* ekstrem di-*cap* pada batas $Q_3 + 3 \times \text{IQR}$, menghasilkan **20.557 baris bersih** tanpa mengubah rata-rata harga target secara signifikan (206.856 USD menjadi 206.970 USD)[cite: 8, 9].

### 2. Faktor Penentu Nilai Rumah (Univariat & Bivariat)
* **Pendapatan adalah Kunci, Bukan Ukuran Blok:** `median_income` menjadi prediktor tunggal terkuat terhadap nilai rumah ($r = 0,690$; $R^2$ validasi silang 5-fold = **0,451**), di mana setiap kenaikan 1 unit pendapatan (10.000 USD) berasosiasi dengan kenaikan harga rumah sekitar **41.900 USD**[cite: 11, 12, 15]. Sebaliknya, variabel ukuran blok (`total_rooms`, `population`, `households`) hampir tidak berkorelasi dengan harga ($r < 0,16$)[cite: 11, 19].
* **Kesenjangan Harga Pesisir vs. Daratan:** Lokasi memiliki asosiasi kuat terhadap kelas harga (Cramér's $V = 0,408$; Welch ANOVA $F = 2.090,6$; Kruskal-Wallis $H = 6.632,1$)[cite: 12, 13]. Rata-rata nilai rumah di wilayah **Pesisir** lebih mahal **120.643 USD** dibandingkan wilayah **Daratan (`INLAND`)** (Cohen's $d = 1,20$)[cite: 13].

### 3. Reduksi Dimensi dengan PCA
* Sembilan variabel numerik terstandarisasi berhasil diringkas menjadi **3 komponen utama optimal** dengan total varians kumulatif **84,6%**:
  * **PC1 (43,7%):** Ukuran blok sensus (`total_rooms`, `households`, `total_bedrooms`, `population`)[cite: 14].
  * **PC2 (28,5%):** Keluasan & kemapanan hunian (`bedrooms_per_room`, `rooms_per_household`, `median_income`)[cite: 14].
  * **PC3 (12,4%):** Kepadatan penghuni (`population_per_household`)[cite: 14].

### 4. Segmentasi Blok Sensus (K-Means Clustering, $k = 5$)
Berdasarkan metode *Elbow*, *Silhouette Score* (0,190), dan *Davies-Bouldin Index* (1,421), terbentuk **5 segmen hunian** dengan profil dan arah rekomendasi berikut[cite: 16, 17, 18]:

| Klaster | Nama Segmen | Proporsi | Median Nilai Rumah | Penghuni / RT | Rasio Harga / Pendapatan | Arah Rekomendasi |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| **0** | **Hunian padat berpendapatan rendah**[cite: 16] | 15,2%[cite: 16] | 136.729 USD[cite: 16] | 3,96[cite: 16] | **6,2x**[cite: 16] | Prioritas utama kebijakan sosial & subsidi perumahan terjangkau[cite: 18] |
| **1** | **Rumah tangga kecil menengah-bawah**
