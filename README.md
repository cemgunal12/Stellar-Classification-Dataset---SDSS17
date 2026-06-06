# 🌌 Stellar Classification — Ablation Study

Bu proje, **SDSS (Sloan Digital Sky Survey)** veri seti üzerinde yıldız sınıflandırması yapan bir **ablation study** (kademeli iyileştirme analizi) çalışmasıdır. Her veri işleme adımının model performansına etkisi tek tek ölçülmüştür.

---

## 📋 İçindekiler

- [Proje Hakkında](#-proje-hakkında)
- [Veri Seti](#-veri-seti)
- [Aşamalar](#-aşamalar)
- [Kullanılan Modeller](#-kullanılan-modeller)
- [Kurulum](#-kurulum)
- [Bulgular](#-bulgular)
- [Sonuç](#-sonuç)

---

## 🔭 Proje Hakkında

Bu çalışmanın amacı, tek seferde mükemmel bir pipeline oluşturmak değil; **her iyileştirme adımını ayrı ayrı test etmek** ve hangi adımın model performansına ne kadar katkı sağladığını somut olarak görmektir.

Her aşamada **8 farklı model** aynı anda eğitilmekte ve sonuçlar karşılaştırmalı bir tabloda toplanmaktadır. Temel metrik olarak **macro-F1** kullanılmıştır — çünkü sınıflar dengesizdir ve her sınıf eşit ağırlıkla değerlendirilmelidir.

---

## 🗂️ Veri Seti

**Kaynak:** SDSS (Sloan Digital Sky Survey) Stellar Classification Dataset

Her satır bir gök cismine karşılık gelir ve üç sınıftan birine aittir:

| Sınıf | Açıklama |
|-------|----------|
| `STAR` | Yıldız |
| `GALAXY` | Galaksi |
| `QSO` | Quasar (Kuasar) |

> ⚠️ Veri seti **dengesizdir.** Bu nedenle doğruluk (accuracy) yerine **macro-F1** temel metrik olarak kullanılmıştır.

---

## 🔬 Aşamalar

| Aşama | Değişiklik | Amaç |
|-------|-----------|-------|
| **V0** | Ham veri, hiçbir işlem yok | Kötü başlangıç noktası oluşturmak |
| **V1** | `-9999` sentinel değerleri ve ID/metadata kolonları temizlendi | Veri kirliliğinin etkisini görmek |
| **V2** | Renk indeksleri eklendi (`u-g`, `g-r`, `r-i`, `i-z`) | Feature engineering katkısını ölçmek |
| **V3** | Pipeline içinde scaling + stratified split + cross-validation | Data leakage'ı önlemek, güvenilir skorlar elde etmek |
| **V4** | `class_weight='balanced'` + macro-F1 | Sınıf dengesizliğini gidermek |
| **V5** | Tüm iyileştirmeler birleştirildi | En iyi sonucu elde etmek |

---

## 🤖 Kullanılan Modeller

### Klasik ML
- Decision Tree
- Random Forest
- KNN (K-Nearest Neighbors)

### Gradient Boosting
- CatBoost
- XGBoost

### Derin Öğrenme (TensorFlow/Keras)
- MLP (Multilayer Perceptron)
- 1D CNN
- LSTM

---

## ⚙️ Kurulum

### Gereksinimler

```bash
pip install numpy pandas matplotlib scikit-learn
pip install catboost xgboost
pip install tensorflow
pip install statsmodels
```

### Çalıştırma

1. `star_classification.csv` dosyasını Google Drive'a yükleyin
2. Notebook'u Google Colab'da açın
3. Hücreleri sırayla çalıştırın

---

## 📊 Bulgular

- **Veri temizliği (V1)** en büyük etkiyi sıralı modellerde (CNN, LSTM) gösterdi — macro-F1'i ~0.35–0.45 artırdı. Ağaç tabanlı modellerde ise V0'daki yüksek skor, ID kolonlarını "ezberlemekten" kaynaklanıyordu, dolayısıyla küçük bir düşüş aslında daha dürüst bir sonuçtu.
- **Renk indeksleri (V2)** mesafe ve sıra bağımsız modellere katkı sağlarken CNN/LSTM'e zarar verdi — feature engineering'in modelin yapısıyla etkileşime girdiğini ortaya koydu.
- **Leakage-free pipeline ve stratification (V3)** skoru çok değiştirmedi ancak sonuçları **güvenilir** hale getirdi.
- **Sınıf dengeleme (V4)** azınlık sınıfı QSO zaten ayrılabilir olduğundan neredeyse nötr kaldı.
- **Hiperparametre tuning** yalnızca küçük bir kazanım sağladı — dominant faktörün model karmaşıklığı değil **veri kalitesi** olduğunu doğruladı.
- En iyi klasik modeller **~0.97–0.98 macro-F1** değerine ulaştı. Derin öğrenme modelleri ise tablo verisi için uygun bir mimari olmadığından sürekli geride kaldı.

---

## ✅ Sonuç

> **Model karmaşıklığı değil, veri kalitesi sonuçları belirledi.**

Bu çalışma, veri biliminde temiz ve doğru hazırlanmış verinin, gelişmiş model mimarilerinden çok daha etkili olduğunu somut olarak göstermektedir. `Redshift` özelliğinin performans üzerinde büyük bir ağırlığı olduğu ve hafif bir label leakage riski taşıdığı da tespit edilmiştir.

---

## 🛠️ Teknolojiler

![Python](https://img.shields.io/badge/Python-3.x-blue)
![Scikit-Learn](https://img.shields.io/badge/scikit--learn-ML-orange)
![TensorFlow](https://img.shields.io/badge/TensorFlow-DL-red)
![XGBoost](https://img.shields.io/badge/XGBoost-Boosting-green)
![CatBoost](https://img.shields.io/badge/CatBoost-Boosting-yellow)
