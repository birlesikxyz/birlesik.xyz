# LINUX OS AÇILIRKEN AŞAĞIDAKİ ADIMLAR İZLENİR

Hemen hemen her yazılım mühendisi daha önce Linux kullanmıştır ancak herkes Linux’da önyükleme işleminin nasıl çalıştığını bilmiyor olabilir, bu nedenle konuyu açıklamanın faydalı olacağını düşünüyorum.

Bir Linux işletim sistemi açılırken temel olacak aşağıdaki adımları izler.

1-Gücü açtığımızda, BIOS (Temel Giriş/Çıkış Sistemi) veya UEFI (Birleşik Genişletilebilir Ürün Yazılımı Arabirimi) bellenimi geçici olmayan bellekten yüklenir ve POST (Kendi Kendini Testte Güç) yürütür.

2-BIOS/UEFI, CPU, RAM ve depolama dahil olmak üzere sisteme bağlı aygıtları algılar.

3- İşletim sistemini başlatmak için bir önyükleme aygıtı seçilir, bu sabit sürücü, ağ sunucusu veya CD- ROM olabilir.

4-BIOS/UEFI, işletim sistemini veya çekirdek işlevlerini seçmek için bir menü sağlayan önyükleyiciyi (GRUB) çalıştırır.

5-Çekirdek hazır olduktan sonra kullanıcı alanına geçilir. Çekirdek, işlemleri ve hizmetleri yöneten kalan tüm donanımları araştıran, dosya sistemlerini bağlayan ve bir masaüstü ortamı çalıştıran ilk kullanıcı alanı işlemi olarak systemd’yi başlatır.

6-Systemd varsayılanı etkinleştirir sistem ön yüklendiğinde varsayılan olarak hedef birim diğer analiz birimleri de yürütülür. 

7-Sistem bir dizi başlangıç komut dosyası çalıştırır ve ortamı yapılandırır.

8-Kullanıcılara bir oturum açma penceresi sunulur ve sistem artık giriş için hazırdır.
