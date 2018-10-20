# Password Attack
Any possibility that the user will re use the password????

### Wordlists
Generate a custom wordlist
cewl -w createWordlist.txt -m <min password length> https://www.example.com

### Offline
https://hashes.org/search.php

john the ripper
john --wordlist=/user/share/wordlists/rockyou.txt hash.txt

Hashcat << check type online - hashcat sample hash
hashcat -m\<type> -a 0 /usr/share/wordlists/rockyou.txt hash.txt

### Online
HTTP post form

hydra -L <wordlist> -P<password list> <IP> http-post-form "<file path>:username=^USER^&password=^PASS^&Login=Login:<fail message>"

1. easy password? e.g. password, 123456
2. Company or site name or its variations? e.g. use the software name as password
3. Name=Password and other hints
4. Simple sequences
5. Basic words
