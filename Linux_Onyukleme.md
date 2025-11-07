# Linux Önyükleme Sürecinin 6 Adımı

Sistemlerimizi açtığımız anda tetiklenen süreçleri anlamamız bizim için çok önemli.  Bu nedenle, herkesin bir işletim sisteminin nasıl davrandığını anlamak ve ayrıca herhangi bir önyükleme hatasını çözmek için önyükleme kavramlarını gözden geçirmesi gerçekten önemlidir.  Burada Linux sistemlerinden bahsettiğimize göre, Linux’ta önyüklemenin temellerini anlayarak başlayalım.

Bir Linux sisteminin başlatılması, aygıt yazılımının başlatılması, bir önyükleyicinin çalıştırılması, bir Linux çekirdek görüntüsünün başlatılması ve başlatılması ve son olarak çeşitli başlangıç ​​komut dosyalarının ve arka plan programlarının yürütülmesi dahil olmak üzere çeşitli aşamaları ve yazılım bileşenlerini içerir.  Burada kısaca Linux’taki tüm önyükleme süreçlerini tartışacağız.

Tipik bir Linux önyükleme işleminin üst düzey aşamaları şunlardır:
    • 1. BIOS / UEFI
    • 2. MBR
    • 3. GRUB
    • 4. Kernel
    • 5. Init
    • 6. Run Levels
Şimdi bu aşamalar hakkında daha fazlasını keşfedelim –

## BIOS / UEFI

BIOS, Temel Giriş/Çıkış Sistemi anlamına gelir.  Linux önyükleme sürecindeki ilk adım, sistem bütünlük kontrollerini gerçekleştiren BIOS’tur.  BIOS, modern dünyada en yaygın bilgisayar türü olan IBM PC uyumlu bilgisayarlarda en yaygın olarak bulunan bir ürün yazılımıdır.  BIOS’un temel amacı sistem önyükleyicisini bulmaktır.

Dolayısıyla BIOS, sabit sürücüyü başlattığında sistemin nasıl başlatılacağını bulmak için önyükleme bloğunu arar.  Disk bölümünün nasıl oluşturulduğuna bağlı olarak MBR (Ana Önyükleme Kaydı) ve GPT’ye (GUID Bölümleme Tablosu) bakacaktır.

Bunun tersine, bir UEFI (Birleşik Genişletilebilir Ürün Yazılımı Arayüzü) sistemi, tüm başlangıç ​​verilerini bir .efi dosyasında saklar.  Dosya, önyükleyiciyi içeren EFI Sistem Bölümündedir.  UEFI, geleneksel BIOS ürün yazılımına göre birçok iyileştirmeyle birlikte gelir.  Ancak Linux kullandığımız için çoğumuz BIOS kullanıyoruz.
‘fdisk’ komutunu kullanarak Linux makinemizdeki bölümleri kontrol edebiliriz.

```
root@ubuntu:~$ sudo fdisk -l

Disk /dev/sda: 92 GiB, 98784247808 bytes, 192937984 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: DA785EC6-AB02-4D8F-9623-B1977C5CCCD5

Device       Start      End  Sectors  Size Type
/dev/sda1     2048     4095     2048    1M BIOS boot
/dev/sda2     4096  3149823  3145728  1.5G Linux filesystem
/dev/sda3  3149824 67106815 63956992 30.5G Linux filesystem

Disk /dev/sdb: 84 GiB, 90194313216 bytes, 176160768 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 30.5 GiB, 32744931328 bytes, 63954944 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

Özetle, önyükleyici işletim sistemini yükleyen küçük bir programdır. Temel olarak Linux çekirdeği ile üç ana eylem gerçekleştirir:
    • Locate on the disk
    • Insert into memory and
    • Execute with the supplied options.

## Master Boot Record (MBR)

MBR, sabit sürücünün ilk sektöründe, ilk 512 baytta bulunur. Diskte bir yere başka bir program yüklemek için kod içerir ve bu da aslında önyükleyicimizi yükler. Ayrıca üç bileşene ayrılmıştır – ilk 446 baytta birincil önyükleyici bilgisi, sonraki 64 baytta bölüm tablosu bilgisi ve son 2 baytta MBR doğrulama kontrolü.

GRUB (veya önceki nesil sistemlerde LILO) hakkında bilgi içerir. Bu nedenle, daha basit bir ifadeyle, MBR, GRUB önyükleyicisini yükler ve yürütür.

Not: LILO veya Linux Loader, geçmişte çoğu Linux dağıtımı için varsayılan önyükleyiciydi. Bugün, birçok dağıtım varsayılan önyükleyici olarak GRUB’u kullanıyor, ancak LILO ve varyantı ELILO hala yaygın olarak kullanılıyor.

Burada modern Linux dağıtımlarından bahsettiğimiz için, GRUB önyükleyicisinin ne olduğunu anlayalım.

## GRUB

GRUB, Grand Unified Bootloader anlamına gelir.

Artık önyükleyicinin birincil amacının çekirdeği yüklemek olduğunu bildiğimize göre, çekirdeği nasıl bulduğunu anlamak da bizim için aynı derecede önemlidir. Bulmak için çekirdeğimize veya önyükleme parametrelerimize bakmamız gerekecek. Şimdi şu parametrelere bakalım:
    • initrd – (initial ramdisk), Linux başlatma işleminin bir parçası olarak kullanılmak üzere belleğe geçici bir kök dosya sistemi yüklemek için kullanılan bir şemadır.
    • BOOT_IMAGE – Çekirdek görüntüsünün bulunduğu yer burasıdır.
    • root – Kök dosya sisteminin konumu; çekirdek init’i bulmak için bu konumun içinde arama yapar. Genellikle UUID’si veya /dev/sda1 gibi cihaz adıyla temsil edilir.
    • ro – Dosya sistemini salt okunur mod olarak bağlayan standart bir parametredir.
    • quiet – Bu parametre, önyükleme sırasında arka planda devam eden ekran mesajlarını görmememiz için eklenir.
    • splash – Bu parametre giriş ekranını görüntüler.

GRUB bir giriş ekranı görüntüler, birkaç saniye bekler ve grub yapılandırma dosyasında belirtildiği gibi varsayılan çekirdek görüntüsünü (veya el ile seçileni) yükler.

GRUB ve LILO sistemleri arasındaki büyük farklardan biri, GRUB’un birden fazla işletim sisteminin önyüklenmesini desteklemesi, LILO’nun ise yalnızca bir işletim sistemini önyükleyebilmesidir. GRUB, eski LILO’dan farklı olarak dosya sistemleri hakkında da bilgi sahibidir.

Ana GRUB yapılandırma dosyası ‘/boot/grub/grub.cfg’ içinde bulunabilir. Basit bir yapılandırma dosyası şuna benzeyebilir:

```
root@ubuntu:~$ sudo nano /boot/grub/grub.cfg

# /boot/grub/grub.cfg
#
# GRUB configuration file

# Timeout for menu
set timeout=5

# Default boot entry
set default=0

# Entry titles
menuentry "Ubuntu" {
    linux   /vmlinuz root=/dev/sda1 ro quiet splash
    initrd  /initrd.img
}
```

Yukarıdaki çıktıdan, menuentry bölümünün belirli bir önyükleme seçeneğini temsil ettiğini görebiliriz. Bu durumda, girişin başlığı “Ubuntu”dur. Linux satırı, çekirdek görüntü dosyasını (vmlinuz) ve kök aygıtı (/dev/sda1) belirtir. Initrd satırı, önyükleme işlemi sırasında ek sürücüler ve modüller yüklemek için kullanılan ilk RAM disk dosyasını (initrd.img) belirtir.

Bu nedenle, basit bir ifadeyle, GRUB yalnızca Kernel ve initrd görüntülerini yükler ve yürütür.

Sisteminizdeki grub sürümünü aşağıdaki komut ile kontrol edebilirsiniz. Ubuntu 23.04 sisteminde çıktı şöyle görünüyor:

```
$ grub-install --version
grub-install (GRUB) 2.06-2ubuntu16
$
```

Grub’un yaygın olarak kullanılan versiyonları ya 0.97 (eski) ya da grub 2’dir, grub 2 en yenisidir ve yaygın olarak benimsenmiştir.

## Kernel

Artık önyükleyicimiz gerekli parametreleri geçtiğine göre, önyükleme eylemlerinin geri kalanının nasıl gerçekleştiğini anlamalıyız:

Initrd vs Initramfs

Initrd (ilk RAM diski) ve initramfs (ilk RAM dosya sistemi), önyükleme işlemi sırasında belleğe yüklenen geçici dosya sistemleri olarak kullanılır. Initrd, sıkıştırılmış bir dosya sistemi görüntüsü kullanan daha eski bir yöntemdir, initramfs ise önyükleme işlemini yönetmede daha fazla esneklik ve verimlilik için bir cpio arşivi kullanan daha yeni bir yaklaşımdır.

Root dosya sisteminin bağlanması

Kök dosya sistemini bağlamak, ana dosya sistemini işletim sistemi tarafından erişilebilir kılmak için önyükleme işleminde çok önemli bir adımdır. Artık çekirdek, bir kök aygıt oluşturmak ve kök bölümü bağlamak için ihtiyaç duyduğu tüm modüllere sahiptir.

Daha fazla ilerlemeden önce, kök bölüm aslında salt okunur modda monte edilir, böylece ‘fsck’ güvenli bir şekilde çalışabilir ve sistem bütünlüğünü kontrol edebilir. Daha sonra, kök dosya sistemini okuma-yazma modunda yeniden bağlar. Ardından çekirdek, init programını bulur ve çalıştırır.

Kernel executes the /sbin/init file or program. Since init is the first program to be executed by kernel, it has the process id (PID/ Process ID) of 1 always. We can see the PID of the init file by executing the following command – ps -ef | grep init.

```		
root@ubuntu:/sbin$ ps -ef | grep init
root         1     0  0 May07 ?        00:01:43 /sbin/init maybe-ubiquity
```

## Init

Init, “/etc/inittab” dosyasından varsayılan init seviyesini kontrol eder ve tanımlar ve çalışma seviyesi için gereken tüm uygun programları yükler. Aşağıdaki koşu seviyelerini listeleyin:
    • 0 – halt
    • 1 – single-user mode
    • 2 – Multiuser, without NFS
    • 3 – Full multiuser mode
    • 4 – unused
    • 5 – X11
    • 6 – reboot

Sorunsuz bir çalışma için genellikle varsayılan çalışma seviyesini 0 ile 6 arasında ayarlarız.
Init sistemi /sbin/init programı aracılığıyla başlatılır. Gerçek başlangıç sistemi ikili dosyasına bir sembolik bağlantı olabilir. Ubuntu sistemimde systemd ikili dosyasına işaret ediyor

```
$ ls -la /sbin/init 
lrwxrwxrwx 1 root root 20 Mar 20 19:47 /sbin/init -> /lib/systemd/systemd
$
```

Linux’ta aslında üç farklı başlangıç sistemi vardır:

SysV init (System V)

Başlangıç komut dosyalarına dayalı olarak işlemleri sistematik bir sırayla başlatır ve durdurur. Makinenin durumu çalışma seviyeleri ile gösterilir ve her çalışma seviyesi farklı davranır. Şimdi Systemd ile değiştirildi.

Upstart

Upstart, işler ve olaylar fikrini kullanır ve olaylara yanıt olarak belirli eylemleri gerçekleştiren işleri başlatarak çalışır. Bu birim eski Ubuntu kurulumlarında bulunur.

Systemd

Bu, init için yeni standarttır ve daha popüler bir standarttır. systemd ile etkileşim kurmak için kullanılan komut, sistemi ve systemd altında çalışan tüm hizmetleri yönetmek için kullanılabilen systemctl’dir. İçeride Systemd hizmetleri birimler olarak tanımlanır ve hizmetlerin yanı sıra montaj noktaları, cihazlar ve soketler gibi birçok başka birim türü vardır.

Systemd, Linux sisteminin önyüklendiği duruma veya hedefe karar vermek için /etc/systemd/system/default.target veya /lib/systemd/system/default.target dosyasını kullanır.
Linux sisteminizde hangi init sisteminin çalıştığını kontrol etmek için aşağıdaki komutu kullanın:

```
$ sudo stat /proc/1/exe
  File: /proc/1/exe -> /usr/lib/systemd/systemd
  Size: 0               Blocks: 0          IO Block: 1024   symbolic link
Device: 0,22    Inode: 16337       Links: 1
Access: (0777/lrwxrwxrwx)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2023-06-24 16:30:01.644109024 +0530
Modify: 2023-06-06 12:56:01.788704998 +0530
Change: 2023-06-06 12:56:01.788704998 +0530
 Birth: -
$
```

## Run Levels

Linux önyükleme işlemindeki çalışma seviyeleri, sistemin içinde olabileceği farklı çalışma modlarını veya yapılandırmaları tanımlar.
Varsayılan başlatma düzeyi ayarına bağlı olarak, sistem programları aşağıdaki dizinlerden birinden çalıştıracaktır:
    • Run level 0 – /etc/rc.d/rc0.d/
    • Run level 1 – /etc/rc.d/rc1.d/
    • Run level 2 – /etc/rc.d/rc2.d/
    • Run level 3 – /etc/rc.d/rc3.d/
    • Run level 4 – /etc/rc.d/rc4.d/
    • Run level 5 – /etc/rc.d/rc5.d/
    • Run level 6 – /etc/rc.d/rc6.d/

Çalışma düzeyleri altında dikkate alınması gereken birkaç gösterim vardır:
    • Bu dizinler için doğrudan /etc altında sembolik bağlantılar mevcuttur. Örneğin, /etc/rc0.d, /etc/rc.d/rc0.d ile bağlantılıdır.
    • /etc/rc.d/rc*.d/ dizinlerinin içinde S ve K ile başlayan programları göreceğiz.
    • Başlatma için ‘S’ ile başlayan programlar kullanılır.
    • İşlemi sonlandırmak veya kapatmak için ‘K’ ile başlayan programlar kullanılır.
    • ‘S’ veya ‘K’ harfinden hemen sonra bir sayı görebiliriz. Bu aslında bir programın yürütüldüğü (sonlandırıldığı veya başlatıldığı) sıra numarasıdır.

İşte bizde var. Linux önyükleme işleminin arkasında olan tam olarak budur.

Çift Önyükleme Kurulumları Nasıl Çalışır?
Tipik bir çift önyükleme kurulumunda, sistem aynı bilgisayarda birden fazla işletim sistemi kurulu olacak şekilde yapılandırılmıştır ve bu da başlatma sırasında aralarında seçim yapmamıza olanak tanır.

Modern sistemlerde, UEFI (Birleşik Genişletilebilir Ürün Yazılımı Arabirimi) ve ESP (EFI Sistem Bölümü) bu süreçte önemli roller oynamaktadır. Şimdi, çift önyükleme kurulumunun nasıl çalıştığını görelim:

Daha önce tartışıldığı gibi, UEFI, geleneksel BIOS’un yerini alan modern ürün yazılımı arayüzüdür. İşletim sistemini başlatmak ve sistem ayarlarını yönetmek için standart bir yöntem sağlar. UEFI ürün yazılımı donanımı başlatır, gerekli testleri gerçekleştirir ve ardından önyükleyiciyi arar.

ESP (EFI System Partition) : ESP, işletim sistemlerini başlatmak için gerekli dosyaları tutan depolama aygıtındaki bir bölümdür. Genellikle UEFI ürün yazılımı ile uyumlu olan FAT32 dosya sistemi ile biçimlendirilir. ESP, “EFI” dizini en önemlisi olmak üzere çeşitli dizinler içerir.

Separate boot-loaders : ESP içindeki EFI dizini, yüklü her işletim sistemi için önyükleyicileri ve ilgili dosyaları depolar. Örneğin, Linux ve Windows yüklüyse, ESP her biri için ayrı dizinler içerecektir. Bu dizinlerde, Linux ve Windows için EFI önyükleyicileri bulunur.

Başlatma sırasında, UEFI üretici yazılımı ESP’yi algılar ve EFI dizininde önyükleyiciyi arar. Linux için GRUB veya Windows için Windows Önyükleme Yöneticisi gibi önyükleyici, seçilen işletim sisteminin yüklenmesinden sorumludur.

Birçok çift önyükleme yapılandırmasında varsayılan önyükleyici olan GRUB, ESP içindeki EFI dizininde bulunur. Farklı işletim sistemleri için seçenekler içeren bir önyükleme menüsü sağlar. “grub.cfg” gibi GRUB yapılandırma dosyaları genellikle ‘/boot/grub’ dizininde (Linux) depolanır.

Önyükleme menüsünden bir işletim sistemi seçtiğimizde, ilgili EFI önyükleyicisi ESP’den yüklenir. Linux için GRUB, Linux çekirdeğini yükler ve Linux işletim sistemini başlatır. Windows için, Windows işletim sistemi başlatılarak Windows Önyükleme Yöneticisi çağrılır.
 
Özetle, çift önyükleme kurulumunda, UEFI bellenimi, ESP içindeki EFI dizininde önyükleyiciyi arar. ESP, EFI önyükleyicilerinin depolandığı her kurulu işletim sistemi için ayrı dizinler içerir. GRUB veya Windows Önyükleme Yöneticisi gibi önyükleyici, kullanıcının önyükleme menüsünden yaptığı seçime göre seçilen işletim sistemini yükler.
