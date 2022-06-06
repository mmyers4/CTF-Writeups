This CTF gives us a binary file and I wanted to begin my analysis within the command line by checking the file type and attempting to execute the program. As we can see from the output below, this is an ELF 64 bit executable and we can add the execute permission with chmod to run the program.

```text
┌──(kali㉿REbox)-[~/Reversing1]
└─$ file impossible_password.bin 
impossible_password.bin: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=ba116ba1912a8c3779ddeb579404e2fdf34b1568, stripped
                                                                                                                                                                                                                  
┌──(kali㉿REbox)-[~/Reversing1]
└─$ ./impossible_password.bin   
zsh: permission denied: ./impossible_password.bin
                                                                                                                                                                                                                  
┌──(kali㉿REbox)-[~/Reversing1]
└─$ chmod +x impossible_password.bin                                                                                                                                                                        126 ⨯
                                                                                                                                                                                                                  
┌──(kali㉿REbox)-[~/Reversing1]
└─$ ./impossible_password.bin       
* aaaaaaaaaa
[aaaaaaaaaa]
```

This output gave me the general idea that the program is asking for some type of input and now the goal is to figure out the input that it wants and find the flag within this binary.
