# 🔍 Arama Algoritmaları 

## 🎯 Temel Problem

**Arama nedir?** Çok sayıda benzer nesne içinden **belirli özelliklere sahip olanları** tespit etme işlemidir.

### Günlük Hayattan Örnekler

| Problem Türü | Pratik Kullanım |
|--------------|-----------------|
| 🔤 **Pattern Matching** | Google'da kelime arama, metin editörlerinde "Bul" özelliği |
| 📊 **Veri Tabanı Sorgusu** | Öğrenci bilgi sisteminde öğrenci bulma, envanter kontrolü |

---

## 🏗 Temel Yapı Taşları

### Element Sınıfı Tasarımı

```java
class Element {
    int searchKey;        // Anahtar alan (örn: 2019280045)
    String name;          // İsim
    String surname;       // Soyisim
    String department;    // Bölüm
}
```

### Veri Koleksiyonu Oluşturma

```java
final int MAXELEM = 1000;
Element[] database = new Element[MAXELEM];
```

**📌 Hedef:** `database[i].searchKey == arananAnahtar` şartını sağlayan elemanı bulmak

---

## 📂 Bölüm 1: Sırasız Koleksiyonda Arama

### Yaklaşım

Hiçbir düzen olmadığında tek çare: **baştan sona her şeyi kontrol et!**

```
Öğrenci Listesi (Sırasız):
┌─────────┬──────────┐
│  47823  │  Mehmet  │
│  12456  │  Ayşe    │
│  89012  │  Can     │ ← Aranan: 89012
│  34567  │  Zeynep  │
│  90123  │  Ali     │
└─────────┴──────────┘
```

### 🔧 Standart İmplementasyon

```java
/**
 * Sırasız dizide basit doğrusal arama
 * @param db Arama yapılacak dizi
 * @param freeIdx İlk boş indeks (dolu eleman sayısı)
 * @param searchKey Aranan anahtar değer
 * @return Bulunan elemanın indeksi, bulunamazsa -1
 */
int search(Element[] db, int freeIdx, int searchKey) {
    int index = 0;
    
    // Her elemanı kontrol et
    while ((index < freeIdx) && (db[index].key != searchKey))
        index++;
    
    // Sonuna geldik ama bulamadık
    if (index == freeIdx)
        return -1;
    
    return index;  // Bulundu!
}
```

### 📈 Performans İncelemesi

**Problem Boyutu:** n = veri koleksiyonundaki eleman sayısı  
**Varsayım:** Tüm elemanlar eşit sıklıkta aranır

```
Senaryo 1 - En Kötü Durum:
[1][2][3][4][5]...[998][999][1000]
                              ↑
                    Aranan burada veya yok
                    Toplam: 1000 karşılaştırma
```

```
Senaryo 2 - Ortalama Durum:
[1][2][3]...[497][498][499][500]...[1000]
                       ↑
              Ortalama burada bulunur
              Toplam: ~500 karşılaştırma
```

| Durum | Karşılaştırma | Açıklama |
|-------|---------------|----------|
| 🔴 **Worst Case** | n | Eleman sonunda veya hiç yok |
| 🟡 **Average Case** | n/2 | İstatistiksel ortalama |

---

### ⚡ Optimizasyon: Bekçi Yöntemi (Sentinel)

**Ana Fikir:** Dizinin sonuna arama değerini kopyala → döngü kontrolü daha basit!

```java
int search(Element[] db, int freeIdx, int searchKey) {
    int index = 0;
    
    // Bekçiyi yerleştir (her zaman bulacağımızı garanti eder)
    db[freeIdx].key = searchKey;
    
    // Artık sadece anahtar kontrolü yeterli
    while (db[index].key != searchKey)
        index++;
    
    // Bekçide mi bulduk? (yani gerçekte yok)
    if (index == freeIdx)
        return -1;
    
    return index;
}
```

**🎯 Avantaj:** Döngüde 2 koşul yerine 1 koşul → %30-40 daha hızlı!

```
Normal:          while (i < n && arr[i] != key)
Bekçi ile:       while (arr[i] != key)
                 (Çok daha basit!)
```

---

## 📊 Bölüm 2: Sıralı Koleksiyonda Doğrusal Arama

### Yeni Avantaj: Erken Çıkış!

Dizi sıralıysa ve aranan değerden büyük bir sayıya denk geldiysek → **durabilirz!**

```
Sıralı Dizi: [3, 7, 12, 18, 25, 34, 41, 56]
Aranan: 20

Adım 1: 3  < 20 ✓ devam
Adım 2: 7  < 20 ✓ devam
Adım 3: 12 < 20 ✓ devam
Adım 4: 18 < 20 ✓ devam
Adım 5: 25 > 20 ✗ DUR! (20 burada olamaz)
         ↑
    Burada durabiliyoruz!
```

### 💡 Performans Kazancı

| Senaryo | Sırasız | Sıralı | Kazanç |
|---------|---------|--------|--------|
| **Var olan eleman** | n/2 | n/2 | Aynı |
| **Olmayan eleman** | n | n/2 | **%50 daha hızlı!** |

**Neden?** Çünkü olmayan elemanı aramaya başlangıçta, ortada veya sonda olabilir:
- Sırasız → Her zaman sonuna kadar bakmalıyız
- Sıralı → Ortalama ortada durabiliyoruz

---

## 🚀 Bölüm 3: Binary Search (İkili Arama)

### 🧠 Temel Mantık: Sürekli Ortadan Böl!

**Divide and Conquer Prensibi:**
1. Problemi küçük parçalara böl
2. Her bölmede problem boyutu yarıya iner
3. Logaritmik hız kazancı sağla

### 📖 Telefon Rehberi Örneği

```
"Yılmaz" arıyorsunuz:

1. Rehberi ortadan açın → "M" harfi
   → Yılmaz > M → Sağ yarıya bak

2. Sağ yarıyı ortadan açın → "S" harfi
   → Yılmaz > S → Tekrar sağa

3. O yarıyı ortadan açın → "Y" harfi
   → Bulundu! ✓

3 adımda 1000 isim arasından buldunuz!
```

### 🔢 Algoritma Adımları

```
Başlangıç:
[2][5][9][12][15][19][23][27][31][35]
 ↑                              ↑
left=0                      right=9

Aranan: 23

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Adım 1:
middle = (0+9)/2 = 4
[2][5][9][12][15][19][23][27][31][35]
            ↑
        15 < 23 → SAĞ YARIDA ARA
        
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Adım 2:
left=5, right=9
middle = (5+9)/2 = 7
[19][23][27][31][35]
        ↑
    27 > 23 → SOL YARIDA ARA
    
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Adım 3:
left=5, right=6
middle = (5+6)/2 = 5
[19][23]
    ↑
23 == 23 → BULUNDU! ✅
```

### 💻 Kod İmplementasyonu

```java
/**
 * Sıralı dizide ikili arama
 * @param db Sıralı dizi
 * @param freeIdx İlk boş indeks
 * @param searchKey Aranan değer
 * @return Bulunan indeks veya -1
 */
int searchBin(Element[] db, int freeIdx, int searchKey) {
    int left = 0;
    int right = freeIdx - 1;
    int middle = (left + right) / 2;
    
    // Sol ve sağ çakışana kadar devam
    while ((left < right) && (db[middle].key != searchKey)) {
        
        if (searchKey < db[middle].key)
            right = middle - 1;  // Sol yarıda ara
        else
            left = middle + 1;   // Sağ yarıda ara
        
        // Yeni orta noktayı hesapla
        middle = (left + right) / 2;
    }
    
    // Kontrol: Gerçekten bulduk mu?
    if (db[middle].key == searchKey)
        return middle;
    
    return -1;  // Bulunamadı
}
```

### 📊 Performans Analizi

**Matematiksel Kanıt:**
- Her adımda problem boyutu: n → n/2 → n/4 → n/8 → ...
- k adım sonra: n / 2^k = 1
- Çözüm: k = log₂(n)

```
Eleman Sayısı vs İşlem Sayısı:

n = 10       → log₂(10)  ≈ 3-4 adım
n = 100      → log₂(100) ≈ 6-7 adım
n = 1,000    → log₂(1000) ≈ 10 adım
n = 1,000,000 → log₂(1M) ≈ 20 adım (!!)
```

**🎯 Sonuç:** Hem worst case hem average case **O(log n)**

### 🆚 Doğrusal vs İkili Arama

```
1 milyon elemanlı dizide arama:

Doğrusal Arama:
█████████████████████████████████ 500,000 işlem

İkili Arama:
█ 20 işlem

🚀 25,000 kat daha hızlı!
```

---

## 🎯 Bölüm 4: İnterpolasyon Araması

### 💡 Akıllı Tahmin Yöntemi

**İkili arama:** Her zaman tam ortaya bakar  
**İnterpolasyon:** Değere göre **akıllıca tahmin** eder

### 📐 Formül Farkı

```java
// İKİLİ ARAMA - Basit ortalama
middle = left + 0.5 * (right - left);

// İNTERPOLASYON - Oransal tahmin
estimate = (key - a[left].key) / (a[right].key - a[left].key);
middle = left + estimate * (right - left);
```

### 🎲 Çalışma Prensibi

```
Dizi: [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
Aranan: 85

İkili Arama:
middle = (0 + 9) / 2 = 4
→ 50'ye bakar (çok geride!)

İnterpolasyon:
estimate = (85 - 10) / (100 - 10) = 0.833
middle = 0 + 0.833 * 9 ≈ 7
→ 80'e bakar (çok yakın!)
```

### ✅ Ne Zaman Kullanılır?

**Uygun:** Anahtarlar **yaklaşık eşit aralıklarla** dağılmış

```
İyi Örnek (Eşit Dağılım):
[100, 200, 300, 400, 500, 600, 700, 800]
  ↑    ↑    ↑    ↑    ↑    ↑    ↑    ↑
  100'er 100'er artıyor - TAHMİN ÇALIŞIR ✓
```

**Uygunsuz:** Düzensiz dağılım

```
Kötü Örnek (Düzensiz):
[1, 2, 3, 4, 5, 999, 1000, 1001, 1002]
 ↑  ↑  ↑  ↑  ↑  ↑↑↑   Büyük sıçrama!
 TAHMİN YANILIR ✗
```

---

## 📋 Genel Karşılaştırma Tablosu

| Algoritma | Ön Koşul | Worst Case | Average Case | Uygun Veri Boyutu |
|-----------|----------|------------|--------------|-------------------|
| **Doğrusal (Sırasız)** | Yok | O(n) | O(n/2) | n < 100 |
| **Doğrusal (Sıralı)** | Sıralı | O(n) | O(n/2)* | n < 100 |
| **İkili Arama** | Sıralı | O(log n) | O(log n) | n > 100 ✅ |
| **İnterpolasyon** | Sıralı + Eşit Dağılım | O(n) | O(log log n) | Çok büyük + uniform |

*Sıralı dizide bulunamayan elemanlar için daha iyi

---

## 🎓 Pratik Kullanım Kılavuzu

### Karar Verme Şeması

```
┌─────────────────────────────┐
│ Verin sıralı mı?            │
└──────────┬──────────────────┘
           │
    ┌──────┴──────┐
    │ HAYIR       │ EVET
    ▼             ▼
┌─────────┐  ┌──────────────────┐
│Doğrusal │  │ Eleman sayısı?   │
│ Arama   │  └────────┬─────────┘
└─────────┘           │
                ┌─────┴──────┐
            < 100         > 100
                │             │
                ▼             ▼
           ┌─────────┐  ┌──────────────┐
           │Doğrusal │  │Veriler düzenli│
           │         │  │dağılmış mı?   │
           └─────────┘  └───────┬───────┘
                              │
                        ┌─────┴────┐
                    HAYIR        EVET
                        │           │
                        ▼           ▼
                  ┌─────────┐ ┌──────────────┐
                  │İkili    │ │İnterpolasyon │
                  │Arama ✅ │ │Araması       │
                  └─────────┘ └──────────────┘
```

### 💼 Gerçek Dünya Örnekleri

| Senaryo | Önerilen Algoritma | Neden? |
|---------|-------------------|--------|
| 📱 Telefon rehberi | İkili Arama | Sıralı + büyük veri |
| 📚 Kütüphane kataloğu | İkili Arama | Alfabetik sıra |
| 🎮 Oyun skorları (sırasız) | Doğrusal | Küçük + sırasız |
| 📊 Hisse senedi fiyatları | İnterpolasyon | Zamana göre düzenli |
| 🔢 Sıralı numaralar | İnterpolasyon | Eşit aralıklı |

---

## 🔑 Anahtar Kavramlar Sözlüğü

| Terim | Almanca | Açıklama |
|-------|---------|----------|
| **Search Key** | Suchschlüssel | Aranacak benzersiz değer (örn: TC no) |
| **Sentinel** | Markieren | Döngüyü basitleştiren yardımcı değer |
| **Divide & Conquer** | Teile-und-Herrsche | Problemi bölerek çözme stratejisi |
| **Time Complexity** | Laufzeit | Algoritmanın hız performansı |
| **Binary Search** | Binäre Suche | Yarılayarak arama |
| **Interpolation** | Interpolationssuche | Oransal tahminle arama |

---

## 🎯 Önemli Hatırlatmalar

### ⚠ Binary Search İçin Kritik Noktalar

1. **Dizi mutlaka sıralı olmalı!**
2. Middle hesabında overflow riski: `(left + right) / 2` yerine `left + (right - left) / 2`
3. Boundary koşullarına dikkat: `left <= right` mı `left < right` mi?

### 🔧 Bekçi Tekniği İpuçları

```java
// ⚠ Dikkat: Bekçi için bir boş alan gerekli!
Element[] database = new Element[MAXELEM + 1];  // +1 önemli!
```

### 📈 Büyük-O Notasyonu Hatırlatma

```
O(1)        - Sabit zaman (en hızlı)
O(log n)    - Logaritmik (çok hızlı)
O(n)        - Doğrusal (normal)
O(n log n)  - Linearitmik (yavaş)
O(n²)       - Karesel (çok yavaş)
```
Emrullah Enis Çetinkaya
LICENSE:MIT