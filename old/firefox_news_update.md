# Firefox'un yeni paketleme tercihi 

Bz2 daha az sıkıştırma yapıyor diye biri 4 yıl önce hata raporu açmış öneri olarak da zstd önermiş ancak sıkıştırma ve arşivi açma testleri sonucunda çok fazla bir fark görülmemiş ve iş modelleri gereği iki kere arşivleme yapmaları gerekiyormuş birinci aşamada zstd daha ucuz iş gücü sağlar ancak arşivi açmada daha fazla RAM tüketiyor ve iki kere arşivleme işlemi potansiyel sorunlara sebep olur diyerek tek aşama daha makul görülmüş ve xz tercih edilmiş.

Kaynak : https://www.phoronix.com/news/Mozilla-Firefox-Linux-Tar-XZ
