# ECG-BP-Estimation-XGBoost Regression-SHAP
ECG-based blood pressure estimation using ECGDeli, XGBoost Regression, and SHAP

Latar Belakang
Pembahasan hasil berfokus pada performa XGBoost regression dalam mengestimasi tekanan darah sistolik (SBP) dan diastolik (DBP). Sinyal ECG dari Shimmer ECG sensor diekstraksi menggunakan ECGdeli dan dikombinasikan dengan data demografis. Model awal dilatih menggunakan fitur tersebut, kemudian SHAP digunakan untuk menganalisis kontribusi dan menyeleksi fitur berdasarkan mean absolute SHAP. Fitur terpilih digunakan untuk melatih model akhir. Performa model sebelum dan sesudah seleksi dibandingkan menggunakan MAE, RMSE, dan R² untuk mengetahui pengaruh seleksi fitur SHAP terhadap hasil estimasi tekanan darah

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

PENGUMPULAN DATA
Menggunakan datasheets kaggle part 1 dan part 12

EKSTRAKSI FITUR 
%% ECGDeli Processing Example
% Proses satu record ECG menggunakan ECGDeli
% Dataset : Cuff-Less Blood Pressure Estimation
% Sampling frequency : 125 Hz
% ECG : single lead

clear;
clc;

%% 1. Menambahkan ECGDeli
addpath(genpath('D:\Skripsi\ECGdeli-master (1)\ECGdeli-master'));

%% 2. Load satu record dataset
data = load('D:\Skripsi\Dataset\part_1.mat\part_1.mat');
p = data.p;

record = p{1};

PPG = record(1,:);
ABP = record(2,:);
ECG = record(3,:);

fs = 125;

%% 3. Deteksi QRS
FPT = QRS_Detection(ECG, fs, 'm');

%% 4. Deteksi gelombang P
FPT = P_Detection(ECG, fs, FPT);

%% 5. Deteksi gelombang T
FPT = T_Detection(ECG, fs, FPT);

%% 6. Menampilkan hasil FPT
disp('Ukuran FPT:');
disp(size(FPT));

%% 7. Ekstraksi 7 fitur interval ECG
sample_to_ms = 1000 / fs;

P_duration = sample_to_ms .* (FPT(:,3) - FPT(:,1));
QRS_duration = sample_to_ms .* (FPT(:,8) - FPT(:,4));
T_duration = sample_to_ms .* (FPT(:,12) - FPT(:,10));

PQ_interval = sample_to_ms .* (FPT(:,4) - FPT(:,3));
PR_interval = sample_to_ms .* (FPT(:,4) - FPT(:,2));
QT_interval = sample_to_ms .* (FPT(:,12) - FPT(:,4));

RR_interval = sample_to_ms .* diff(FPT(:,6));

%% 8. Menampilkan beberapa hasil fitur
features = [
    P_duration(1:end-1), ...
    QRS_duration(1:end-1), ...
    T_duration(1:end-1), ...
    PQ_interval(1:end-1), ...
    PR_interval(1:end-1), ...
    QT_interval(1:end-1), ...
    RR_interval
    ];

disp('7 fitur ECG:');
disp(features(1:min(5,size(features,1)),:));

%% 9. Visualisasi ECG dan R peak
figure;

plot(ECG);
hold on;

r_peaks = FPT(:,6);
r_peaks = r_peaks(r_peaks > 0);

plot(r_peaks, ECG(r_peaks), 'ro');

xlabel('Sample');
ylabel('Amplitude');
title('Deteksi R-Peak menggunakan ECGDeli');
legend('ECG','R-Peak');
grid on;
