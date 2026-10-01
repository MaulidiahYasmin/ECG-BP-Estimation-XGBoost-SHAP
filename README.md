# ECG-BP-Estimation-XGBoost Regression-SHAP
ECG-based blood pressure estimation using ECGDeli, XGBoost Regression, and SHAP

Alur Kerja 

1. **PENGUMPULAN DATA**
Penelitian diawali dengan mengumpulkan dataset observasi klinis yang berisi sinyal ECG serta data observasi pasien yang diperlukan sebagai variabel pendukung.
2. **EKSTRAKSI FITUR**
Sinyal ECG yang telah diperoleh kemudian diproses menggunakan ECGDeli untuk mendapatkan fitur morfologi ECG, seperti amplitudo, durasi, dan interval gelombang. Fitur tersebut kemudian dikombinasikan dengan variabel observasi yang tersedia.
3. **PRA-PEMROSESAN DATA**
Fitur yang telah diperoleh dilakukan pembersihan dan normalisasi agar data lebih siap digunakan dalam proses pemodelan dan memiliki skala yang sesuai.
4. **PELATIHAN MODEL AWAL**
Data yang telah diproses digunakan untuk melatih model awal XGBoost regression. Model awal ini digunakan sebagai dasar untuk mengetahui kontribusi masing-masing fitur terhadap hasil estimasi.
5. **ANALISIS SHAP**
Model awal kemudian dianalisis menggunakan SHAP (SHapley Additive exPlanations) untuk mengetahui seberapa besar kontribusi masing-masing fitur terhadap prediksi model.
6. **SELEKSI FITUR**
Berdasarkan hasil analisis SHAP, fitur dipilih menggunakan nilai mean absolute SHAP. Fitur dengan kontribusi yang lebih besar dipertahankan untuk digunakan pada model berikutnya.
7. **PELATIHAN MODEL AKHIR**
Fitur yang telah terpilih kemudian digunakan untuk melatih kembali model XGBoost regression sehingga diperoleh model akhir dengan fitur hasil seleksi SHAP.
8. **EVALUASI MODEL**
Model akhir kemudian dievaluasi menggunakan MAE, RMSE, dan R² untuk mengetahui tingkat kesalahan dan kemampuan model dalam mengestimasi tekanan darah.
