`sudo apt update`

`sudo apt upgrade -y`

`sudo hostnamectl set-hostname mail.asixjge.cat`

`sudo nano /etc/hosts`

``` /etc/hosts
127.0.0.1       localhost
127.0.1.1       josse-VirtualBox
192.168.0.55 mail.asixjge.cat mail

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
`sudo apt install bind9 -y`

`sudo nano /etc/bind/db.asixjge.cat`

``` /etc/bind/db.asixjge.cat
$TTL    86400
@       IN      SOA     mail.asixjge.cat. admin.asixjge.cat. (
                          2024042501 ; Serial
                          3600      ; Refresh
                          1800      ; Retry
                          604800    ; Expire
                          86400     ; Minimum TTL
)
@       IN      NS      mail.asixjge.cat.
@       IN      MX      10 mail.asixjge.cat.
mail    IN      A       192.168.0.55
www     IN      A       192.168.0.55
```

`sudo nano /etc/bind/named.conf.local`

```  /etc/bind/named.conf.local
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "asixjge.cat" {
    type master;
    file "/etc/bind/db.asixjge.cat";
};


```

`sudo systemctl restart bind9`
`sudo named-checkzone asixjge.cat /etc/bind/db.asixjge.cat` 
<span style="color:gray;">zone asixjge.cat/IN: loaded serial 2024042501
<br>
OK
</span>
`dig mail.asixjge.cat @<span style="color:gray;">; <<>> DiG 9.18.30-0ubuntu0.20.04.2-Ubuntu <br><<>> mail.asixjge.cat @localhost<br>
;; global options: +cmd <br>
;; Got answer: <br>
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 14228 <br>
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
<br><br>
;; OPT PSEUDOSECTION:<br>
; EDNS: version: 0, flags:; udp: 1232<br>
; COOKIE: 6f01f13f9628f0e00100000068092f7a63dbd190224d4133 (good)<br>
;; QUESTION SECTION:<br>
;mail.asixjge.cat.		IN	A<br>

;; ANSWER SECTION:<br>
mail.asixjge.cat.	86400	IN	A	192.168.0.55<br>
<br><br>
;; Query time: 0 msec<br>
;; SERVER: 127.0.0.1#53(localhost) (UDP)<br>
;; WHEN: Wed Apr 23 20:20:42 CEST 2025<br>
;; MSG SIZE  rcvd: 89<br>
</span>localhost` 

### Instalar postfix
`sudo apt install postfix -y`
sitio de internet

`sudo nano /etc/postfix/main.cf`
``` /etc/postfix/main.cf
smtpd_banner = mail.asixjge.cat ESMTP
myhostname = mail.asixjge.cat
mydomain = asixjge.cat
myorigin = $mydomain
inet_interfaces = all
inet_protocols = all
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
relayhost =
mynetworks = 127.0.0.0/8, 192.168.0.0/24
mailbox_size_limit = 0
home_mailbox = Maildir/
recipient_delimiter = +
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
```

`sudo systemctl restart postfix`

`telnet localhost 25`

<span style="color:gray;">Trying 127.0.0.1... <br>
Connected to localhost.<br>
Escape character is '^]'.<br>
220 mail.asixjge.cat ESMTP
</span>

`EHLO asixjge.cat`
<span style="color:gray;">250-mail.asixjge.cat <br>
250-PIPELINING<br>
250-SIZE 10240000<br>
250-VRFY<br>
250-ETRN<br>
250-STARTTLS<br>
250-ENHANCEDSTATUSCODES<br>
250-8BITMIME<br>
250-DSN<br>
250-SMTPUTF8<br>
250 CHUNKING<br>
</span>

### Instalar dovecot IMAP/POP3
`sudo apt install dovecot-core dovecot-imapd dovecot-pop3d -y`

`sudo nano /etc/dovecot/conf.d/10-mail.conf`
``` /etc/dovecot/conf.d/10-mail.conf
mail_location = maildir:~/Maildir
```
`sudo nano /etc/dovecot/conf.d/10-auth.conf`

```/etc/dovecot/conf.d/10-auth.conf
disable_plaintext_auth = no
auth_mechanisms = plain login
```
`sudo nano /etc/dovecot/conf.d/10-ssl.conf`
```/etc/dovecot/conf.d/10-ssl.con
ssl = no
```

`sudo systemctl restart dovecot`

`adduser a21josperjim`

`telnet localhost 143`
<span style="color:gray;">Trying 127.0.0.1...<br>
Connected to localhost.<br>
Escape character is '^]'. <br>
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN AUTH=LOGIN] <br>
* Dovecot (Ubuntu) ready.
</span>
`a LOGIN a21josperjim contraseña`

<span style="color:gray;">a OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE] Logged in
</span>

`a LIST "" "*"`
<span style="color:gray;">* LIST (\HasNoChildren) "." INBOX <br>
a OK List completed (0.001 + 0.000 secs).
</span>

 `a LOGOUT`
<span style="color:gray;">* BYE Logging out<br>
a OK Logout completed (0.001 + 0.000 secs).<br>
Connection closed by foreign host.
</span>

`sudo apt install mailutils`

`echo "Cuerpo del correo" | mail -s "Asunto de prueba" a21josperjim@asixjge.cat`

`sudo ls /home/a21josperjim/Maildir/new` 
<span style="color:gray;">1745436927.V805Ia0164M691176.mail.asixjge.cat</span>
 `sudo cat /home/a21josperjim/Maildir/new/1745436927.V805Ia0164M691176.mail.asixjge.cat`

Return-Path: <josse@mail.asixjge.cat>
X-Original-To: a21josperjim@asixjge.cat
Delivered-To: a21josperjim@asixjge.cat
Received: by mail.asixjge.cat (Postfix, from userid 1000)
	id A66C61217ED; Wed, 23 Apr 2025 21:35:27 +0200 (CEST)
Subject: Asunto de prueba
To: <a21josperjim@asixjge.cat>
X-Mailer: mail (GNU Mailutils 3.7)
Message-Id: <20250423193527.A66C61217ED@mail.asixjge.cat>
Date: Wed, 23 Apr 2025 21:35:27 +0200 (CEST)
From: josse <josse@mail.asixjge.cat>

Cuerpo del correo

![[Pasted image 20250423214421.png]]

