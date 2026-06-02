# AirGuard
# 🌬️ AirGuard
### Bulanık Mantık Tabanlı Akıllı Hava Kalitesi İzleme Sistemi

> **BIM 434 — Bulanık Mantık Dönem Projesi | İstanbul Sabahattin Zaim Üniversitesi**

---

## 📌 Proje Hakkında

AirGuard, kapalı ortamlarda hava kalitesini gerçek zamanlı olarak izleyen, Mamdani bulanık mantık tabanlı bir sistemdir. İnsanlar bulundukları ortama zamanla alıştıklarından hava kalitesinin bozulduğunu fark edemeyebilirler. AirGuard bu problemi çözmek için geliştirilmiştir.

Sistem; sıcaklık, nem ve gaz seviyesini aynı anda ölçerek bulanık mantık ile değerlendirir ve kullanıcıyı hem fiziksel (LCD, LED, buzzer) hem de dijital (dashboard) çıktılarla bilgilendirir.

---

## 🎯 Özellikler

-  3 sensörden gerçek zamanlı veri okuma (DHT22 + MQ-135)
-  Mamdani bulanık çıkarım motoru (23 kural)
-  Hava kalitesi skoru üretimi (0–100)
-  LCD ekranda anlık gösterim
-  LED ile alarm seviyesi gösterimi (Mavi/Sarı/Kırmızı)
-  Buzzer ile sesli uyarı
-  Canlı dashboard (Tkinter + Matplotlib)
-  CSV veri kaydı
-  Otomatik grafik üretimi (8 akademik grafik)

---

## 🔧 Donanım

| Bileşen | Açıklama | Pin |
|---|---|---|
| Arduino Uno | Ana mikrodenetleyici | — |
| DHT22 | Sıcaklık + Nem sensörü | D3 |
| MQ-135 | Gaz / CO₂ sensörü | A0 |
| 16x2 LCD + I2C | Ekran (adres: 0x27) | A4/A5 |
| Mavi LED | İyi modu göstergesi | D9 |
| Sarı LED | Orta modu göstergesi | D10 |
| Kırmızı LED | Tehlike modu göstergesi | D11 |
| Aktif Buzzer | Sesli alarm | D8 |

---

##  Yazılım Gereksinimleri

```bash
pip install scikit-fuzzy numpy matplotlib pyserial pandas
```

**requirements.txt:**
```
scikit-fuzzy==0.4.2
numpy>=1.21.0
matplotlib>=3.5.0
pyserial>=3.5
pandas>=1.3.0
```

---

##  Dosya Yapısı

```
AirGuard/
│
├── arduino/
│   └── hava_kalitesi.ino      # Arduino kodu
│
├── python/
│   ├── hava_kalitesi.py       # Ana bulanık mantık + seri haberleşme
│   ├── dashboard.py           # Canlı dashboard arayüzü
│   ├── grafikler.py           # Akademik grafik üretici
│   ├── kural_atesleme.py      # Kural ateşleme grafiği
│   └── mimari.py              # Sistem mimarisi grafiği
│
├── veri/
│   └── hava_kalitesi_veri.csv # Sentetik test veri seti (300 kayıt)
│
├── grafikler/
│   ├── grafik1_uyelik_fonksiyonlari.png
│   ├── grafik2_test_senaryolari.png
│   ├── grafik3_yuzey.png
│   ├── grafik4_parametre_analizi.png
│   ├── grafik5_defuzzification.png
│   ├── grafik6_kural_atesleme.png
│   ├── grafik7_sistem_mimarisi.png
│   └── grafik8_gercek_veriler.png
│
└── README.md
```

---

## 🚀 Kurulum ve Çalıştırma

### 1. Arduino Kurulumu

Arduino IDE'yi aç, gerekli kütüphaneleri kur:
- `DHT sensor library` (Adafruit)
- `LiquidCrystal I2C` (Frank de Brabander)

`arduino/hava_kalitesi.ino` dosyasını aç ve Arduino'ya yükle.

### 2. Python Kurulumu

```bash
pip install -r requirements.txt
```

### 3. Sistemi Başlat

Arduino IDE'yi **tamamen kapat**, sonra:

```bash
# Ana sistem (seri haberleşme + bulanık mantık)
python python/hava_kalitesi.py

# Dashboard arayüzü
python python/dashboard.py

# Grafikleri üret
python python/grafikler.py
```

> ⚠️ **Önemli:** Arduino IDE açıkken Python COM portuna bağlanamaz. Arduino kodunu yükledikten sonra IDE'yi kapatın.

---

##  Sistem Mimarisi

```
DHT22 + MQ-135
      ↓
  Arduino Uno
  (Sensör okuma + Seri port)
      ↓ S:24.5,N:58.0,G:285
  Python
  (Mamdani Bulanık Mantık)
  - Fuzzification
  - 23 kural ateşleme
  - Defuzzification (Centroid)
      ↓ K:68.5,A:0
  Arduino Uno
  (LCD + LED + Buzzer)
  + Dashboard (PC)
```

---

##  Bulanık Mantık Sistemi

### Giriş Değişkenleri

| Değişken | Aralık | Etiketler | Fonksiyon |
|---|---|---|---|
| Sıcaklık | 0–40°C | Düşük, Orta, Yüksek | Üçgensel |
| Nem | 0–100% | Düşük, Orta, Yüksek | Üçgensel |
| Gaz | 0–1023 | Temiz, Orta, Kirli | Üçgensel |

### Çıkış Değişkeni

| Değişken | Aralık | Etiketler | Alarm |
|---|---|---|---|
| Kalite Skoru | 0–100 | Kötü, Orta, İyi | TEHLİKE / ORTA / İYİ |

### Çıkarım Yöntemi
- **Yöntem:** Mamdani
- **Defuzzification:** Centroid (COG)
- **Kural sayısı:** 23

---

##  Test Sonuçları

| Senaryo | Sıcaklık | Nem | Gaz | Beklenen | Sistem |
|---|---|---|---|---|---|
| Temiz Hava | 20°C | %40 | 100 | İyi | %88 ✅ |
| Normal Ortam | 24°C | %55 | 280 | İyi | %67 ✅ |
| Nemli Ortam | 27°C | %85 | 300 | Orta | %35 ✅ |
| Kirli Hava | 30°C | %70 | 700 | Kötü | %27 ✅ |
| Maks Kirlilik | 35°C | %95 | 1000 | Kötü | %13 ✅ |
| Soğuk Temiz | 5°C | %20 | 50 | İyi | %88 ✅ |
| Sıcak Nemli | 38°C | %90 | 400 | Kötü | %34 ✅ |

7 senaryonun tamamında beklenen alarm seviyesi doğru üretildi.


## 👤 Geliştirici

**Merve** — İstanbul Sabahattin Zaim Üniversitesi  
BIM 434 Bulanık Mantık | 2026

---

## 📄 Lisans

Bu proje akademik amaçlı geliştirilmiştir.
