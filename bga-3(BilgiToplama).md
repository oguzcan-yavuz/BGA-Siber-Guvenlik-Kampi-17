## Tavsiyeler (İbrahim Akgül)

1) Bir işletim sisteminin mekanizmalarının nasıl çalıştığını dibine kadar bilmiyorsanız orta düzey bir güvenlikçi bile olamazsınız.

**Test sorusu:** Makineye 5 dakikada mavi ekran veya kernel panic nasıl verdirtilir?

**İşletim sistemi nedir?**

- Her işletim sistemi aslında sadece birer kernel'dan ibarettir. Kernel modüllerden oluşur. Bu modüller birbirleriyle haberleşirler.

- Windows'un shell'i explorer.exe'dir. Kullanıcı ile işletim sistemini buluşturan bir arayüz programıdır.

- Windows'un GUI arayüzü ile Linux'un GUI arayüzü arasındaki performans farkının sebebi, Windows'un GUI arayüzünün direk olarak kernel'e bağlı olmasındandır. 

		=> top (WINDOWS => PROCESS MONITOR, Linux alternate => GLANCES, ps aux): İşletim sistemi ayakta kalabilmek için sürekli bir şeyler yapar. (Proc dizininin altını iyi öğrenmek bu konuyla ilgili daha detaylı bilgi edinmemizi sağlayabilir.)

**Yüzde yüz korunmalı bir sistemi tek bir bilgisayar ile nasıl servis dışı bırakırsınız?**

- PROCESS MON: tools-> process tree (buradan şüpheli process'leri, açıklamalarını, çalıştırdığı kodları inceleriz) Çalıştırılan kodlar eğer parametre alıyorsa fuzzy attack denenerek 0day exploit'ler aranabilir. 

- Process Hacker: vmware-hostd.exe'den şüphelendik. Çift tıkladık. CommandLine'da ne çalıştırdığını inceleriz. ProgramData'da (windows'ta sanallaştırma) bir config dosyası çalıştırdığını görüyoruz. Bu config dosyasını açıp inceleriz. Bir sıkıntı yok gibi gözüküyor. File kısmının altında imzayı inceleriz. Dosyanın sertifikalanmış ve imzalanmış olması kesin güvenli olduğu anlamına gelmez ancak imzalayan firmanın kim olduğu ve programın sahibi ile aynıysa güvenli olma ihtimali yükselir. Modülleri kontrol ederiz, aynı sertifika gibi DLL'leri kontrol ederiz. Trusted yazmayan dll varsa DLL Injection yemiş olabiliriz.

## Bilgi Toplama

	=> whois turkcell.com.tr 	(nic.tr üzerinden NIC domainini kullanmak.)

	=> whois IP		(Bu işlem bize hedefe ait IP bloğunu verebilir.)

- Altdizin, subdomain vs. için => wfuzz

		=> whois -h whois.crsnic.net turkcell 	(içerisinde turkcell geçen satın alınmış domain'ler)

**- DNS Zone:** Genelde DNS adminler bir ağ kaydı girdiklerinde bir sunucuya girerler, diğer sunucular kendileri gidip kendilerini update ederler. Ancak update olma yetkisini sınırlamadıklarında biz de gidip sorduğumuzda bize de ZONE'u verecektir.

	=> host turkcell.com.tr
	=> fierce -range IP.1-254 -dnsserver 
	=> cd /usr/share/wfuzz/wordlist/general
	=> wfuzz -c --hc 404,XXX -z file,common.txt -z file,test.txt -z range,1-100 FUZZ.turkcell.com.tr/FUZ2Z/index.asp?id=FUZ3Z

## Soru-Cevap

- Sadece 80 portu açıksa nasıl içeri gireriz?

	- Eğer 80 portunda HTTP varsa, web tarafına bakmamız lazım.

- 80'den komut yolluyoruz. 21'de FTP çalışıyor. Diğer bütün portlar firewall tarafından bloklu. Nasıl RDP bağlantısı alırız?

	- 21 portunu kill edip RDP'yi 21 portunda çalıştırabiliriz veya içeride 21 portunu 3389 portuna ip forwarding ederiz.

- 80 portunun altında open-source bir program çalışıyorsa bu programı edinip analiz ederek bir açık bulabiliriz.

- Web servislerine verilen parametreleri inceleriz.
