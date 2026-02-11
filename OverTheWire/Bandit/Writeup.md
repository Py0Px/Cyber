# OverTheWire Bandit – Writeup

This is my walkthrough of the **OverTheWire Bandit** wargame, showing the exact commands used and the logic behind each step.

---

## Connecting to the Server

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
Password: bandit0
```

# Level 0 ---> Level 1
```
"The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game."
>>ls //a command to list files from current directory, you can add -la to list all files including hidden ones.
 readme
>>cat readme
  Congratulations on your first steps into the bandit game!!
  Please make sure you have read the rules at https://overthewire.org/rules/
  If you are following a course, workshop, walkthrough or other educational activity,
  please inform the instructor about the rules as well and encourage them to
  contribute to the OverTheWire community so we can keep these games free!

 The password you are looking for is: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

>>exit //to disconnect from the lab and reconnect using the ssh command with bandi1 username and the password provided!
```
# Level 1 ---> Level 2
```
"The password for the next level is stored in a file called - located in the home directory"
>>ls
   -
>>cat ./-
 263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```
# Level 2 ---> Level 3
```
"The password for the next level is stored in a file called --spaces in this filename-- located in the home directory"
>>ls
 --spaces in this filename--

>>cat ./--spaces\ in\ this\ filename--
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```
# Level 3 ---> Level 4
```
"The password for the next level is stored in a hidden file in the inhere directory."
>>ls
inhere

>>cd inhere/

>>ls -la
...Hiding-From-You

>>cat ./...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```
# Level 4 ---> Level 5
```
"The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command."
>> ls -la
inhere

>>file ./-file07
./-file07: ASCII text

cat ./-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```

# Level 5 ---> Level 6
```
"The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:
human-readable
1033 bytes in size
not executable"

>>find . -size 1033c -readable
./maybehere07/.file2

>>cat ./maybehere07/./file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

# Level 6 --> Level 7
```
"The password for the next level is stored somewhere on the server and has all of the following properties:
owned by user bandit7
owned by group bandit6
33 bytes in size"

>>find / -size 33c -group bandit6 -user bandit7 2>/dev/null
 /var/lib/dpkg/info/bandit7.password

>>cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

# Level 7 ---> Level 8
```
"The password for the next level is stored in the file data.txt next to the word millionth"

>> cat data.txt | grep "millionth"
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

# Level 8 ---> Level 9
```
"The password for the next level is stored in the file data.txt and is the only line of text that occurs only once"
>>cat data.txt | sort | uniq --count // sort all of the data in the file and count repeted lines
1 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

# Level 9 ---> Level 10
```
"The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters."
>>cat data.txt | strings | grep "==="
========== the
========== password
E========== is
5========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

# Level 10 ---> Level 11
```
"The password for the next level is stored in the file data.txt, which contains base64 encoded data"
>>cat data.txt | base64 -d
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

# Level 11 ---> Level 12
```
"The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions"
>>cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m' //ROT13 Cipher
 The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```
# Level 12 ---> Level 13
```
The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)

//first create workplace with premissions
bandit12@bandit:~$ cd /tmp
bandit12@bandit:/tmp$ mkdir my_workplace
bandit12@bandit:/tmp$ cd my_workplace/

//copy the data.txt file into workplace

bandit12@bandit:/tmp/my_workplace$ cp /home/bandit12/data.txt .
bandit12@bandit:/tmp/my_workplace$ ls
data.txt
bandit12@bandit:/tmp/my_workplace$ file data.txt
data.txt: ASCII text

//they help us by saying the data.txt file is a hexdump so we use xxd:

bandit12@bandit:/tmp/my_workplace$ xxd -h
Usage:
       xxd [options] [infile [outfile]]
    or
       xxd -r [-s [-]offset] [-c cols] [-ps] [infile [outfile]]
Options:
    -r          reverse operation: convert (or patch) hexdump into binary.

bandit12@bandit:/tmp/my_workplace$ xxd -r data.txt > stage1
bandit12@bandit:/tmp/my_workplace$ ls
data.txt  stage1
bandit12@bandit:/tmp/my_workplace$ file stage1
stage1: gzip compressed data, was "data2.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 572

//now we can see it is gzip compressed data so we use gzip -d (change name to stage1.gz first):

bandit12@bandit:/tmp/my_workplace$ mv stage1 stage1.gz
bandit12@bandit:/tmp/my_workplace$ gzip -h
Usage: gzip [OPTION]... [FILE]...
Compress or uncompress FILEs (by default, compress FILES in-place).
  -d, --decompress  decompress

bandit12@bandit:/tmp/my_workplace$ gzip -d stage1.gz
bandit12@bandit:/tmp/my_workplace$ ls
data.txt  stage1

bandit12@bandit:/tmp/my_workplace$ file stage1
stage1: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/my_workplace$ mv stage1 stage1.bz
bandit12@bandit:/tmp/my_workplace$ bunzip2 stage1.bz
bandit12@bandit:/tmp/my_workplace$ file stage1
stage1: gzip compressed data, was "data4.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 20480
//back to gzip again:
bandit12@bandit:/tmp/my_workplace$ mv stage1 stage1.gz
bandit12@bandit:/tmp/my_workplace$ gzip -d stage1.gz

bandit12@bandit:/tmp/my_workplace$ file stage1
stage1: POSIX tar archive (GNU)
//now we see it is tar archive, so we use tar -xf (extract with force):
bandit12@bandit:/tmp/my_workspace$ tar -xf stage1
bandit12@bandit:/tmp/my_workspace$ ls
data5.bin  data.txt  stage1

//we got a new file called data5.bin:
bandit12@bandit:/tmp/my_workspace$ file data5.bin
data5.bin: POSIX tar archive (GNU)

//another tar archive:
bandit12@bandit:/tmp/my_workspace$ tar -xf data5.bin
bandit12@bandit:/tmp/my_workspace$ ls
data5.bin  data6.bin  data.txt  stage1
bandit12@bandit:/tmp/my_workspace$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k

//new file data6.bin is bzip2:
bandit12@bandit:/tmp/my_workspace$ mv data6.bin data6.bz
bandit12@bandit:/tmp/my_workspace$ bunzip2 data6.bz
bandit12@bandit:/tmp/my_workspace$ ls
data5.bin  data6  data.txt  stage1
//another data6:

bandit12@bandit:/tmp/my_workspace$ file data6
data6: POSIX tar archive (GNU)
bandit12@bandit:/tmp/my_workspace$ tar -xf data6
bandit12@bandit:/tmp/my_workspace$ ls
data5.bin  data6  data8.bin  data.txt  stage1

//new file data8.bin:
bandit12@bandit:/tmp/my_workspace$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 49

bandit12@bandit:/tmp/my_workspace$ mv data8.bin data8.gz
bandit12@bandit:/tmp/my_workspace$ gzip -d data8.gz
bandit12@bandit:/tmp/my_workspace$ ls
data5.bin  data6  data8  data.txt  stage1
bandit12@bandit:/tmp/my_workspace$ file data8
data8: ASCII text

//and finaly ASCII text file :D
bandit12@bandit:/tmp/my_workspace$ cat data8
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```
# Level 13 ---> Level 14
```
The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Look at the commands that logged you into previous bandit levels, and find out how to use the key for this level.



```

# Level 14 ---> Level 15
```
```

# Level 15 ---> Level 16
```
```

# Level 16 ---> Level 17
```
```












































