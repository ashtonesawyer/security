# String Processing
## cut
`cut -d '<delimiter>' -f <# location>`

Example:
```
 $ head -n3 log
104.245.97.236 - - [29/Sep/2015:21:15:18 -0400] "GET /xmlrpc.php HTTP/1.1" 404 162 "-" "-"
91.196.50.33 - - [29/Sep/2015:21:22:48 -0400] "GET http://testp3.pospr.waw.pl/testproxy.php HTTP/1.1" 404 136 "-" "Mozilla/5.0 (Windows NT 5.1; rv:32.0) Gecko/20100101 Firefox/31.0"
216.218.206.66 - - [30/Sep/2015:00:38:26 -0400] "GET / HTTP/1.1" 502 166 "-" "-"

 $ head -n3 log | cut -d ' ' -f1
104.245.97.236
91.196.50.33
216.218.206.66
 
 $ head -n3 log | cut -d ' ' -f4
[29/Sep/2015:21:15:18
[29/Sep/2015:21:22:48
[30/Sep/2015:00:38:26
```

## uniq
Example:
```
 $ cat log
104.245.97.236 - - [29/Sep/2015:21:15:18 -0400] "GET /xmlrpc.php HTTP/1.1" 404 162 "-" "-"
91.196.50.33 - - [29/Sep/2015:21:22:48 -0400] "GET http://testp3.pospr.waw.pl/testproxy.php HTTP/1.1" 404 136 "-" "Mozilla/5.0 (Windows NT 5.1; rv:32.0) Gecko/20100101 Firefox/31.0"
216.218.206.66 - - [30/Sep/2015:00:38:26 -0400] "GET / HTTP/1.1" 502 166 "-" "-"
...

 $ cat log | cut -d '"' -f3
 404 162
 404 136
 502 166
...

 $ cat log | cut -d '"' -f3 | cut -d ' ' -f2 | uniq -c
      2 404
      1 502
      1 404
      1 400
...

 $ cat log | cut -d '"' -f3 | cut -d ' ' -f2 | sort | uniq -c
     19 200
      2 301
     38 400
     21 404
     19 502
```

## awk
Example:
```
 $ tail -n8 log
1286536321.103   3806 192.168.0.68 TCP_MISS/200 507 POST http://rcv-srv37.inplay.tubemogul.co...eiver/services - DIRECT/174.129.41.128 application/xml
1286536321.383   3850 192.168.0.68 TCP_MISS/200 507 POST http://rcv-srv37.inplay.tubemogul.co...eiver/services - DIRECT/174.129.41.128 application/xml
1286536321.455    756 192.168.0.68 TCP_MISS/200 507 POST http://rcv-srv37.inplay.tubemogul.co...eiver/services - DIRECT/174.129.41.128 application/xml
1286536321.636   1366 192.168.0.188 TCP_MISS/200 12851 GET http://www.bing.com/search? - DIRECT/122.160.242.147 text/html
1286536322.767   3878 192.168.0.68 TCP_MISS/200 507 POST http://rcv-srv37.inplay.tubemogul.co...eiver/services - DIRECT/174.129.41.128 application/xml
1286536331.040      5 192.168.0.227 TCP_MISS/503 855 GET http://s2.youtube.com/s? - NONE/- text/html
1286536333.436     17 192.168.0.188 TCP_MISS/503 861 GET http://api.bing.com/qsml.aspx? - NONE/- text/html
1286536351.746  41762 192.168.0.227 TCP_MISS/200 5340945 GET http://v15.lscache3.c.youtube.com/videoplayback? - DIRECT/122.160.120.150 video/x-flv

 $ tail -n8 log | cut -d ' ' -f4
3806
3850

1366
3878


192.168.0.227

 $ tail -n3 log | awk '{print $1}'
1286536331.040
1286536333.436
1286536351.746

 $ tail -n8 log | awk '{print $2}'
3806
3850
756
1366
3878
5
17
41762

 $ tail -n8 log | awk '{print $3}'
192.168.0.68
192.168.0.68
192.168.0.68
192.168.0.188
192.168.0.68
192.168.0.227
192.168.0.188
192.168.0.227
```

To add a set of numbers:
```
awk '{s+=$1} END {printf "%.0f\n", s}'
```
