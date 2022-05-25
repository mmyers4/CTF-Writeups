# Exatlon Write-up
This is a Hack The Box write up from a reverse engineering capture the flag.

### Starting information:

This CTF has a difficulty rating of easy and for the purpose of this CTF I will be using Kali Linux to perform the reverse engineering.

Firstly, I begin this CTF with a zip file that contains a file for reverse engineering. The main goal is to reverse engineer the file and find the flag for submission. Additionally, the flags for HackTheBox are in the format of HTB{flag}. To start with some basic information, I wanted to unzip the file and determine the file type of what we are given:

```text
┌──(kali㉿REbox)-[~/Reversing1]
└─$ file exatlon_v1 
exatlon_v1: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), statically linked, no section header

```


### Further analysis:

Next I can see that the file type is an ELF 64-bit LSB executable. After noticing the file-type I attempted to run the executable to see what kind of output it is giving me. Once executed, I was prompted with a title and a request for the Exatlon Password, wherein I entered the letter "m" and the program produced the following output:

![running](https://github.com/mmyers4/HTB-Writeup-1/blob/main/IMGS/Screenshot%20(725).png)

I noticed that the program was returning a frowning face for the "m" password attempt, therefore I decided to continue with some further analysis within Ghidra. After importing the executable into Ghidra this is what the program returned in the Import Results Summary:

![import](https://github.com/mmyers4/HTB-Writeup-1/blob/main/IMGS/Screenshot%20(731).png)

After analyzing the results, there was not much data to work from. I decided to open the file in the code browser, and run an analysis on it to see if there was anything that I could find within the code. I was not able to find very much at first, therefore I decided to perform a search for strings within the code browser of Ghidra. After performing a search for strings and scrolling through the results I noticed an interesting line that tells me the executable was packed with UPX. Finally after some research into UPX, I figured out that I could unpack the executable in the command line to help with even further analysis. The following text is the command that was run to unpack the executable file along with the output:

```text
┌──(kali㉿REbox)-[~/Reversing1]
└─$ upx -d exatlon_v1                  
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2020
UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
   2202568 <-    709524   32.21%   linux/amd64   exatlon_v1

Unpacked 1 file.
```

I then decided to import the newly unpacked executable back into Ghidra to see if I could discover anything new. After importing the newly decompressed program into Ghidra, I noticed that there was quite a bit more data within the Import Results Summary. Therefore I decided to open the project in the code browser and run the analyzer again with the Aggressive Instruction Finder enabled. This allowed me to look into the decompiled code and continue analyzing this file. Furthermore, I decided to look for a main function within the program tree and found a main function in one of the files:

![found](https://github.com/mmyers4/HTB-Writeups/blob/main/IMGS/Screenshot%20(728).png)

After looking into the main function, there are a few interesting things I have noticed. For example, I have very little to no experience with C++ but the decompiled code seems to resemble C++. Furthermore, I found an interesting collection of numbers and a call to an Exatlon function after prompting the user for a password, therefore, I took note of this function call and the numbers which are listed below:

```text
std::operator<<((basic_ostream *)std::cout,"[+] Enter Exatlon Password  : ");
std::operator>>((basic_istream *)std::cin,(basic_string *)local_58);
exatlon(local_38);
bVar1 = std::operator==(local_38, "1152 1344 1056 1968 1728 816 1648 784 1584 816 1728 1520 1840 1664 784  1632 1856 1520 1728 816 1632 1856 1520 784 1760 1840 1824 816 1584 1856  784 1776 1760 528 528 2000 ");
```

I began some dynamic analysis by running the program in Radare and debugging it with breakpoints that stuck out to me within the Ghidra analysis. Noticeably, within the assembly, it looks like at the hex address of 0x00404d24, the exatlon function is being called. Therefore, I set a breakpoint in Radare at the next address following that function call: 0x00404d29

```text
┌──(kali㉿REbox)-[~/Reversing1]
└─$ r2 exatlon_v1    
WARNING: No calling convention defined for this file, analysis may be inaccurate.
[0x00404990]> db 0x00404d29
[0x00404990]> db
0x00404d29 - 0x00404d2a 1 --x sw break enabled valid cmd="" cond="" name="0x00404d29" module=""
[0x00404990]> doo
Process with PID 2857 started...
= attach 2857 2857
File dbg:///home/kali/Reversing1/exatlon_v1  reopened in read-write mode
2857
[0x00404990]> dc

███████╗██╗  ██╗ █████╗ ████████╗██╗      ██████╗ ███╗   ██╗       ██╗   ██╗ ██╗
██╔════╝╚██╗██╔╝██╔══██╗╚══██╔══╝██║     ██╔═══██╗████╗  ██║       ██║   ██║███║
█████╗   ╚███╔╝ ███████║   ██║   ██║     ██║   ██║██╔██╗ ██║       ██║   ██║╚██║
██╔══╝   ██╔██╗ ██╔══██║   ██║   ██║     ██║   ██║██║╚██╗██║       ╚██╗ ██╔╝ ██║
███████╗██╔╝ ██╗██║  ██║   ██║   ███████╗╚██████╔╝██║ ╚████║███████╗╚████╔╝  ██║
╚══════╝╚═╝  ╚═╝╚═╝  ╚═╝   ╚═╝   ╚══════╝ ╚═════╝ ╚═╝  ╚═══╝╚══════╝ ╚═══╝   ╚═╝


[+] Enter Exatlon Password  : A
hit breakpoint at: 0x404d29
```

After hitting the breakpoint, I wanted to continue to analyze the different registry addresses to see what was going on. Interestingly, There were a few noticable results when looking into the CPU registry. For instance, there were some references to ASCII which led me to believe the flag was encoded with some basic type of ASCII related code. Additionally, in the CPU registrys, r10 caught my eye with a four digit number that looked similar to the numbers in that long string of the main function. Therefore, with this knowledge I attempted to rerun the program with the input of 'H' as I knew the flag began with 'HTB'.
