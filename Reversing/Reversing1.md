# HTB-Writeup-Reversing-1
This is a Hack The Box write up from a reverse engineering capture the flag.

### Starting information:

This CTF has a difficulty rating of easy and for the purpose of this CTF I will be using Kali Linux to perform the reverse engineering.

Firstly, I begin this CTF with a zip file that contains a file for reverse engineering. The main goal is to reverse engineer the file and find the flag for submission. I can unzip the file and check the file-type by running the two following commands which will produce this output:

![beginning](https://github.com/mmyers4/HTB-Writeup-1/blob/main/IMGS/Screenshot%20(723).png)

### Further analysis:

Next I can see that the file type is an ELF 64-bit LSB executable. After noticing the file-type I attempted to run the executable to see what kind of output it is giving me. Once executed, I was prompted with a title and a request for the Exatlon Password, wherein I entered the letter "m" and the program produced the following output:

![running](https://github.com/mmyers4/HTB-Writeup-1/blob/main/IMGS/Screenshot%20(725).png)

I noticed that the program was returning a frowning face for the "m" password attempt, therefore I decided to continue with some further analysis within Ghidra. After importing the executable into Ghidra this is what the program returned in the Import Results Summary:

![import](https://github.com/mmyers4/HTB-Writeup-1/blob/main/IMGS/Screenshot%20(731).png)

After analyzing the results, there was not much data to work from. I decided to open the file in the code browser, and run an analysis on it to see if there was anything that I could find within the code. I was not able to find very much at first, therefore I decided to perform a search for strings within the code browser of Ghidra. After performing a search for strings and scrolling through the results I noticed an interesting line that tells me the executable was packed with UPX. Finally after some research into UPX, I figured out that I could unpack the executable in the command line to help with even further analysis. The following image is the command that was run to unpack the executable file:

![import](https://github.com/mmyers4/HTB-Writeup-1/blob/main/IMGS/Screenshot%20(726).png)

I then decided to import the newly unpacked executable back into Ghidra to see if I could discover anything new.