# Network Troubleshooting

## (1) Diagnose Interfaces

### 1.1 ifconfig
- View all interfaces
```
$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.26.81  netmask 255.255.240.0  broadcast 172.31.31.255
        inet6 fe80::15:62ff:fe53:36ea  prefixlen 64  scopeid 0x20<link>
        ether 02:15:62:53:36:ea  txqueuelen 1000  (Ethernet)
        RX packets 22066  bytes 31804832 (30.3 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 6572  bytes 399724 (390.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 8  bytes 648 (648.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8  bytes 648 (648.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

### 1.2 nestat
```
$ netstat -i
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
eth0      9001    24180      0      0 0          8185      0      0      0 BMRU
lo       65536       40      0      0 0            40      0      0      0 LRU
```

```
$ netstat -an
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0    244 172.31.26.81:22         222.127.39.194:54921    ESTABLISHED
tcp6       0      0 :::111                  :::*                    LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:111             0.0.0.0:*                          
udp        0      0 0.0.0.0:748             0.0.0.0:*                          
udp        0      0 127.0.0.1:323           0.0.0.0:*                          
udp6       0      0 fe80::15:62ff:fe53::546 :::*                               
udp6       0      0 :::111                  :::*                               
udp6       0      0 :::748                  :::*                               
udp6       0      0 ::1:323                 :::*      
...
```

### 1.3 iptables

## (2) Diagnose Network Connectivity

### 2.1 ping
- Test network connectivity
```
$ ping -c 4 example.com
PING example.com (54.192.151.75) 56(84) bytes of data.
64 bytes from server-54-192-151-75.sin2.r.cloudfront.net (54.192.151.75): icmp_seq=1 ttl=242 time=0.457 ms
64 bytes from server-54-192-151-75.sin2.r.cloudfront.net (54.192.151.75): icmp_seq=2 ttl=242 time=0.465 ms
64 bytes from server-54-192-151-75.sin2.r.cloudfront.net (54.192.151.75): icmp_seq=3 ttl=242 time=0.502 ms
64 bytes from server-54-192-151-75.sin2.r.cloudfront.net (54.192.151.75): icmp_seq=4 ttl=242 time=0.512 ms

--- example.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3028ms
rtt min/avg/max/mdev = 0.457/0.484/0.512/0.023 ms
```

### 2.2 telnet
- Test connectivity to a port
```
$ telnet example.com  80
Trying 54.192.151.75...
Connected to example.com.
Escape character is '^]'.
```

### traceroute
```
$ traceroute www.example.com
traceroute to www.example.com (54.192.151.77), 30 hops max, 60 byte packets
 1  ec2-175-41-128-142.ap-southeast-1.compute.amazonaws.com (175.41.128.142)  14.037 ms ec2-175-41-128-144.ap-southeast-1.compute.amazonaws.com (175.41.128.144)  12.352 ms ec2-175-41-128-138.ap-southeast-1.compute.amazonaws.com (175.41.128.138)  19.538 ms
 2  100.66.8.208 (100.66.8.208)  12.563 ms 100.66.8.82 (100.66.8.82)  20.569 ms 100.66.8.254 (100.66.8.254)  16.744 ms
 3  100.66.10.166 (100.66.10.166)  20.193 ms 100.66.10.162 (100.66.10.162)  19.349 ms 100.66.11.76 (100.66.11.76)  18.643 ms
 4  100.66.6.99 (100.66.6.99)  19.827 ms 100.66.7.109 (100.66.7.109)  17.502 ms 100.66.6.11 (100.66.6.11)  9.182 ms
 5  100.66.4.37 (100.66.4.37)  22.113 ms 100.66.4.71 (100.66.4.71)  11.601 ms 100.66.4.161 (100.66.4.161)  18.759 ms
 6  100.65.10.193 (100.65.10.193)  0.817 ms 100.65.8.161 (100.65.8.161)  0.367 ms 100.65.11.65 (100.65.11.65)  0.886 ms
 7  203.83.223.22 (203.83.223.22)  1.445 ms 52.93.10.72 (52.93.10.72)  2.509 ms 203.83.223.16 (203.83.223.16)  1.657 ms
 8  52.93.8.70 (52.93.8.70)  1.032 ms 52.93.8.26 (52.93.8.26)  0.962 ms 52.93.8.114 (52.93.8.114)  0.846 ms
 9  52.93.11.171 (52.93.11.171)  0.384 ms 52.93.11.157 (52.93.11.157)  0.365 ms 52.93.11.169 (52.93.11.169)  0.403 ms
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  server-54-192-151-77.sin2.r.cloudfront.net (54.192.151.77)  0.388 ms  0.359 ms  0.368 ms
```

## (3) Test Web Sites

### 3.1 curl
```
$ curl -I https://www.example.com
HTTP/2 200 
content-type: text/html; charset=utf-8
server: nginx
p3p: CP="NOI ADM DEV PSAi COM NAV OUR OTRo STP IND DEM"
x-powered-by: PHP/5.6.37
x-viewer-country: INTL
x-ua-device: desktop
cache-control: max-age=60
date: Sat, 20 Apr 2019 05:55:28 GMT
x-varnish: 1029479530 1029458792
via: 1.1 varnish, 1.1 3a6d09c229b46334ae8150e9562036de.cloudfront.net (CloudFront)
x-age: 959
vary: Accept-Encoding,User-Agent,Cloudfront-Forwarded-Proto
age: 17
x-cache: Hit from cloudfront
x-amz-cf-id: 1lsexiNYWsYan_Bbm4Qg0UhWzVa1aLfp8Tydm45Otg-109cBN8jDLA==
```

### 3.2 wget
```
$ wget -N https://www.example.com
--2019-04-20 06:32:11--  https://www.example.com/
Resolving www.example.com (www.example.com)... 54.192.151.77, 54.192.151.44, 54.192.151.47, ...
Connecting to www.example.com (www.example.com)|54.192.151.77|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Last-modified header missing -- time-stamps turned off.
--2019-04-20 06:32:11--  https://www.example.com/
Reusing existing connection to www.example.com:443.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: 'index.html'

    [ <=>                                                                                                                                    ] 211,569     --.-K/s   in 0.005s  

2019-04-20 06:32:11 (42.7 MB/s) - 'index.html' saved [211569]
```

## (4) Diagnose DNS Issues

### 4.1 nslookup
- DNS Test if domain resolves to an IP address
```
$ nslookup example.com
Server:		172.31.0.2
Address:	172.31.0.2#53

Non-authoritative answer:
Name:	example.com
Address: 54.255.163.116
```

### 4.2 dig
```
$ dig example.com

; <<>> DiG 9.9.4-RedHat-9.9.4-73.amzn2.1.1 <<>> example.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 54328
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;example.com.			IN	A

;; ANSWER SECTION:
example.com.		60	IN	A	54.192.151.77
example.com.		60	IN	A	54.192.151.44
example.com.		60	IN	A	54.192.151.47
example.com.		60	IN	A	54.192.151.75

;; Query time: 14 msec
;; SERVER: 172.31.0.2#53(172.31.0.2)
;; WHEN: Sat Apr 20 06:07:21 UTC 2019
;; MSG SIZE  rcvd: 104
```

### 4.3 host
```
$ host -a example.com
Trying "example.com"
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25118
;; flags: qr rd ra; QUERY: 1, ANSWER: 15, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;example.com.			IN	ANY

;; ANSWER SECTION:
example.com.		60	IN	TXT	"v=spf1 include:_spf.google.com ~all"
example.com.		60	IN	MX	10 aspmx2.googlemail.com.
example.com.		60	IN	MX	10 aspmx3.googlemail.com.
example.com.		60	IN	MX	1 aspmx.l.google.com.
example.com.		60	IN	MX	5 alt1.aspmx.l.google.com.
example.com.		60	IN	MX	5 alt2.aspmx.l.google.com.
example.com.		60	IN	SOA	ns-706.awsdns-24.net. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
example.com.		60	IN	NS	ns-706.awsdns-24.net.
example.com.		60	IN	NS	ns-1339.awsdns-39.org.
example.com.		60	IN	NS	ns-1946.awsdns-51.co.uk.
example.com.		60	IN	NS	ns-47.awsdns-05.com.
example.com.		60	IN	A	54.192.151.75
example.com.		60	IN	A	54.192.151.77
example.com.		60	IN	A	54.192.151.44
example.com.		60	IN	A	54.192.151.47

Received 468 bytes from 172.31.0.2#53 in 10 ms
```

## (5) Diagnose Network Congestion

### 5.1 mtr
- detect network connection (best, worst, average roundtrip times)
```
$ mtr example.com
                                                                     My traceroute  [v0.92]
ip-172-31-26-81.ap-southeast-1.compute.internal (172.31.26.81)                                                                         2019-04-20T06:39:12+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                                                                                       Packets               Pings
 Host                                                                                                                Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. ???
 2. ???
 3. ???
 4. ???
 5. ???
 6. 100.65.8.129                                                                                                      0.0%    28    0.5   1.6   0.4  16.1   3.3
 7. 203.83.223.22                                                                                                     0.0%    28    0.8   1.2   0.7   4.2   0.7
 8. 52.93.8.92                                                                                                        0.0%    28    1.0   1.6   0.8   6.4   1.3
 9. 52.93.11.163                                                                                                      0.0%    28    1.2   1.2   0.5   9.2   1.8
10. ???
11. ???
12. ???
13. ???
14. ???
15. server-54-192-151-44.sin2.r.cloudfront.net                                                                        0.0%    27    0.5   0.5   0.4   0.8   0.1

```

### 5.2 netcat
- Test Network Bandwidth
```
$  netcat
```

## (6) Diagnosing Packet Flows

### 6.1 tcpdump
- View packet flow to see basic TCP 3 way handshake
```
$ telnet
```
