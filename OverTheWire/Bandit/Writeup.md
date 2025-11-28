
This Is My Writeup For The OverTheWire Bandit Wargame

ssh bandit0@bandit.labs.overthewire.org -p 2220 // connect to the server using ssh (password is bandit0)

##Bandit0 ---> Bandit 1


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

##Bandit1 ---> Bandit2
"The password for the next level is stored in a file called - located in the home directory"
>>ls
  -
>>cat ./-
263JGJPfgU6LtdEvgfWU1XP5yac29mFx

##Bandit2 ---> Bandit3
"The password for the next level is stored in a file called --spaces in this filename-- located in the home directory"
>>ls
--spaces in this filename--

>>cat ./--spaces\ in\ this\ filename--
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx

##Bandit3 ---> Bandit4
"The password for the next level is stored in a hidden file in the inhere directory."

>>ls
inhere

>>cd inhere/

>>ls -la
...Hiding-From-You

>>cat ./...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

#Bandit4 ---> Bandit5
"The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command."
>> ls -la
inhere

>>file ./-file07
./-file07: ASCII text

cat ./-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw


#Bandit5 ---> Bandit6
"The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:
human-readable
1033 bytes in size
not executable"

>>find . -size 1033c -readable
./maybehere07/.file2

>>cat ./maybehere07/./file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

#Bandit6 --> Bandit7
"The password for the next level is stored somewhere on the server and has all of the following properties:
owned by user bandit7
owned by group bandit6
33 bytes in size"

>>find / -size 33c -group bandit6 -user bandit7 2>/dev/null
 /var/lib/dpkg/info/bandit7.password

>>cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

#Bandit7 ---> Bandit8
"The password for the next level is stored in the file data.txt next to the word millionth"

>> cat data.txt | grep "millionth"
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

#Bandit8 ---> Bandit9
"The password for the next level is stored in the file data.txt and is the only line of text that occurs only once"
>>cat data.txt | sort | uniq --count
1 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

#Bandit9 ---> Bandit10
"The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters."
>>cat data.txt | strings | grep "==="
========== the
========== password
E========== is
5========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

#Bandit10 ---> Bandit11
"The password for the next level is stored in the file data.txt, which contains base64 encoded data"
>>cat data.txt | base64 -d
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

#Bandit11 ---> Bandit12
"The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions"
>>cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m' //ROT13 Cipher 













































