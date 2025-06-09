# FecalGuard

FecalGuard: Inovasi Deteksi Dini Penyakit Ayam melalui Analisis Citra Feses Berbasis AI 🐔🤖

![TensorFlow](https://img.shields.io/badge/TensorFlow-%23FF6F00.svg?style=for-the-badge&logo=TensorFlow&logoColor=white) 
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi&logoColor=white)
![Firestore](https://img.shields.io/badge/Firestore-FFCA28?style=for-the-badge&logo=Firebase&logoColor=black)
![Kotlin](https://img.shields.io/badge/Kotlin-7F52FF?style=for-the-badge&logo=Kotlin&logoColor=white)
![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=Android&logoColor=white)

## About The Project
<br />
FecalGuard adalah aplikasi Android berbasis AI yang mendeteksi penyakit ayam dari citra feses menggunakan model CNN, dengan klasifikasi ke dalam empat kategori: Healthy, Coccidiosis, Newcastle Disease, dan Salmonella.
<br />

## Team Behind FecalGuard
| Nama                          | ID Cohort         | Institusi             | Status |  
|-------------------------------|--------------|-----------------------|--------|  
| Duma Mora Arta Sitorus         | A200XBM138   | Universitas Diponegoro | Aktif  |  
| Labiba Adinda Zahwana          | A200XBM251   | Universitas Diponegoro | Aktif  |  
| Muhammad Rizki                 | A200YBM347   | Universitas Diponegoro | Aktif  |  
| Tiara Fitra Ramadhani Siregar | A200XBM484   | Universitas Diponegoro | Aktif  |  

# Laporan Masalah Bisnis
## Pernyataan Masalah  
Peternak ayam skala kecil dan menengah menghadapi kesulitan deteksi dini penyakit karena keterbatasan alat diagnostik yang praktis dan terjangkau. Keterlambatan penanganan menyebabkan penyebaran penyakit dan angka kematian tinggi. Feses ayam mengandung informasi biologis penting sebagai indikator kesehatan, namun belum banyak dimanfaatkan teknologi untuk analisisnya. Proyek ini mengembangkan sistem AI untuk menganalisis citra feses ayam secara cepat, murah, dan akurat.

## Pertanyaan Penelitian  
- Bagaimana efektivitas model AI berbasis klasifikasi citra dalam mendeteksi dini penyakit ayam melalui citra feses?  
- Bagaimana melatih model yang dapat melakukan deteksi penyakit pada ternak ayam melalui citra feses ayam?
- Bagaimana mengembangkan aplikasi mobile untuk melakukan deteksi dini penyakit berdasarkan citra feses ayam?

## Lingkup Proyek  
- Pengumpulan dan preprocessing data citra feses ayam  
- Eksplorasi arsitektur CNN dan transfer learning  
- Pelatihan dan fine-tuning model klasifikasi  
- Pengembangan aplikasi mobile untuk upload, klasifikasi gambar untuk deteksi penyakit, memberikan edukasi solusi dan melihat riwayat deteksi
- Dokumentasi dan evaluasi berkala

## Link Dataset  

  Dataset ini diperoleh dari platform penyedia dataset [Kaggle](https://www.kaggle.com)
  
  Dataset: 
    [Chicken Disease Dataset - Kaggle](https://www.kaggle.com/datasets/allandclive/chicken-disease-1)

## Implementasi Model  
### Data Loading & Eksplorasi  
- Dataset 4 kelas penyakit dengan resolusi dominan 224x224 piksel  
- Data dibagi train:val:test = 70:15:15 secara stratifikasi


### Data Augmentation  
- Augmentasi train data (Rescale, Rotate, Width Shift, Height Shift, Shear, Zoom, Horizontal Flip)  
- Normalisasi val dan test data (Rescale)


### Model  
Eksperimen dilakukan dengan beberapa arsitektur deep learning populer untuk klasifikasi citra feses ayam, yaitu:  
- CNN
- VGG16 
- ResNet50 
- DenseNet121 
- MobileNetV2 

Berikut adalah hasil pengukuran dengan hyperparameter terbaik yang telah dipilih berdasarkan berkali-kali percobaan yang dilakukan:

| Model       | Learning Rate | Epoch | Test loss | test Acc | Test Precision |Test Recall  | Test F1-Score |
|-------------|-------------- |-------|-----------|----------|----------------|-------------|---------------|
| CNN         | 0.00001       |30     |0.2713     | 0.8984   | 0.8834         |0.8651       | 0.8733        |
| MobileNetV2 | 0.00001       |50     |0.2213     | 0.9282   | 0.9023         |0.9079       | 0.9047        |  
| DenseNet121 | 0.00001       |30     |0.2152     | 0.9282   | 0.9212         |0.9007       | 0.9100        |  
| ResNet50    | 0.0001        |25     |0.1600     | 0.9537   | 0.9505         |0.9392       | 0.9446        |  
| VGG16       | 0.0001        |25     |0.1107     | 0.9661   | 0.9632         |0.9564       | 0.9597        |  
       
Dari hasil evaluasi performa di dataset, model **VGG16** memberikan hasil terbaik dari segi akurasi, stabilitas pelatihan, serta kemampuan generalisasi ke data validasi dan test.

Model VGG16 yang digunakan adalah versi pretrained dari ImageNet, yang dipakai sebagai **base model tanpa lapisan fully connected (top layer)**. Pada base model tersebut, tambahkan beberapa layer khusus agar sesuai dengan klasifikasi 4 kelas pada proyek ini, meliputi:  
- Global Average Pooling untuk mereduksi dimensi output fitur  
- Dense layer dengan 256, 128, dan 64 neuron beraktivasi ReLU untuk ekstraksi fitur lanjut  
- Batch Normalization untuk menstabilkan dan mempercepat pelatihan  
- Dropout dengan rate 0.2 untuk mengurangi risiko overfitting  
- Dense output layer dengan aktivasi softmax sebanyak 4 neuron, sesuai jumlah kelas penyakit  

Pelatihan dilakukan dengan strategi **transfer learning**:  
- Pada tahap awal, base model VGG16 **dibekukan (freeze)** agar bobot pretrained tidak berubah, hanya layer atas yang dilatih.  
- Setelah itu, buka (unfreeze) 20 lapisan terakhir base model untuk proses **fine-tuning** dengan learning rate kecil, guna menyesuaikan model terhadap karakteristik dataset spesifik citra feses ayam.  

---

### Pelatihan & Evaluasi  
- Epochs: 25 dengan EarlyStopping  
- Metrik: akurasi, precision, recall, f1-score  
- Akurasi test: ~96.6%  
- Precision, recall, f1-score rata-rata >95%

### Penyimpanan  
- Model disimpan sebagai `vgg16_model.h5`

## Visualisasi  
<br>
- Sampel Gambar Dataset
<br>
<p align='center'><img src="https://github.com/user-attachments/assets/bc6bfd27-a9d1-4cc6-8c29-c52987587ed4" alt="Contoh gambar dataset" width="800" />
    </p>
<p align='center'>Gambar 1. Contoh Gambar Sample Citra Feses Ayam</p>
<br>
- Kurva Akurasi dan Loss Pelatihan Model VGG16
<br>
<br>
<p align='center'><img src="https://github.com/user-attachments/assets/1559583e-3666-46f3-b48d-80f74fe21977" alt="evaluasi" width="700" />
</p>
<p align='center'>Gambar 2. Visualisasi Kurva Accuracy Train dan Loss</p>
<br>
- Confusion Matrix Model VGG16
<br>
<br>
<p align='center'><img src="https://github.com/user-attachments/assets/0cc83099-894c-4fb3-8f8e-a752ae5807a0" alt="evaluasi" width="500" />
</p>
<p align='center'>Gambar 3. Convusion Matrix</p>
<br>

## Teknologi yang Digunakan
| Teknologi / Library              | Keterangan                                         |
|---------------------------------|---------------------------------------------------|
| Python                          | Bahasa pemrograman utama                           |
| TensorFlow                      | Framework deep learning untuk pelatihan model CNN |
| Keras (bagian dari TensorFlow)  | API high-level untuk membangun model neural network|
| FastAPI                 | Untuk deploy model di lingkungan Mobile   |
| Kotlin                 | Membangun aplikasi mobile berbasis Android   |
| scikit-learn                   | Untuk evaluasi performa dan pemrosesan data       |
| pandas                         | Manipulasi dan analisis data tabular               |
| numpy                          | Operasi numerik dan array                           |
| matplotlib                     | Visualisasi data dan grafik                          |
| seaborn                       | Visualisasi statistik berbasis matplotlib          |
| pillow (PIL)                  | Pengolahan gambar                                   |
| kagglehub                     | Tool untuk mengakses dataset Kaggle secara otomatis|

Selain itu, beberapa modul bawaan Python juga digunakan seperti `os`, `shutil`, `io`, `random`, dan `collections` untuk pengelolaan file, operasi sistem, dan struktur data dasar.

## Deployment
Model yang dikembangkan diintegrasikan dengan API menggunakan FastAPI dan aplikasi mobile berbasis android.

[Tonton Demo Aplikasi](https://drive.google.com/file/d/1eIXxO_glih71ivN4XaUkJhE2MM8SGSDL/view?usp=sharing) </br>
[Download Aplikasi](https://github.com/qnyara5/FecalGuard-App/blob/main/app-debug.apk)



