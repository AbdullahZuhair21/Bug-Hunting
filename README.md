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
