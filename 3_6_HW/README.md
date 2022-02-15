# 1.
vagrant@vagrant:~$ telnet stackoverflow.com 80
Trying 151.101.129.69...
Connected to stackoverflow.com.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: stackoverflow.com

HTTP/1.1 301 Moved Permanently
cache-control: no-cache, no-store, must-revalidate
location: https://stackoverflow.com/questions
x-request-guid: b2df769d-44fd-4878-92c4-46761dfe6f78
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Accept-Ranges: bytes
Date: Mon, 14 Feb 2022 16:37:33 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-bma1626-BMA
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1644856653.457962,VS0,VE101
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=d3bbb19e-d721-e5b3-5b5b-ca3f64a4d235; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly

Connection closed by foreign host.

HTTP код ответа 301 - страница куда-то переехала.
Только не понял почему 301, сама ссылка достуна. Дело в порте 443?

# 2.
HTTP код 200

Самый долгий:
https://stackoverflow.com/
либо 
collect (Если не учитывать первый).

[Скриншот консоли](https://github.com/almasamrin/devops-netology/blob/main/3_6_HW/3_6_2.jpg)

# 3.
37.99.46.___ - из https://whoer.net/ (адрес скрыл)

# 4.
whois 37.99.46.___
Провайдер: 2Day Telecom LLP
AS - AS21299

# 5.
Даже с камандой sudo получаю только *.
Сделаю вывод по примеру из презентации.
Через 13 разных маршрутизатора. Но не понял почему в примере по 3 IP в некоторых строках.
AS: AS51077, AS28917, AS15169.

# 6.
Вижу маршрутизаторы:
10.0.2.2, 192.168.0.1, 80.241.35.10, 172.16.242.33, 172.16.242.66, 172.16.242.2, 172.16.242.9, 172.16.242.81, 80.241.35.190, 85.29.131.28, 85.29.131.29, 195.208.208.232, 108.170.250.99, 142.251.49.24, 72.14.238.168, 142.250.56.217
AS: AS15169, AS21299, AS35168, AS??? (видимо скрыт)
Наибольшая задержка Wrst 579.7 ms: 
16. AS15169  142.250.56.217     0.1%   768   77.2  74.8  70.8 579.7  19.1

# 7.
dns.google DNS list: k.root-servers.net., l.root-servers.net., m.root-servers.net., a.root-servers.net., b.root-servers.net., c.root-servers.net., d.root-servers.net., e.root-servers.net., f.root-servers.net., g.root-servers.net., h.root-servers.net., i.root-servers.net., j.root-servers.net., j.root-servers.net., i.root-servers.net., h.root-servers.net., g.root-servers.net., f.root-servers.net., e.root-servers.net., d.root-servers.net., c.root-servers.net., b.root-servers.net., a.root-servers.net., m.root-servers.net., l.root-servers.net., k.root-servers.net., ns-tld4.charlestonroadregistry.com., ns-tld3.charlestonroadregistry.com., ns-tld1.charlestonroadregistry.com., ns-tld2.charlestonroadregistry.com., ns-tld5.charlestonroadregistry.com., ns1.zdns.google., ns4.zdns.google., ns3.zdns.google., ns2.zdns.google.

Записи A: 8.8.8.8, 8.8.4.4

# 8.
```
dig -x 8.8.8.8
;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.   14400   IN      PTR     dns.google.
```

```
dig -x 8.8.4.4
;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.   14400   IN      PTR     dns.google.
```
