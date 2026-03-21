# OverTheWire Bandit – Writeup

My walkthrough of the **OverTheWire Bandit** wargame, showing the exact commands used and the logic behind each step.

> 🔗 [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)

---

## Connecting to the Server

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
# Password: bandit0
```

---

## Level 0 → 1

**Goal:** The password is stored in a file called `readme` in the home directory.

**Commands:**
```bash
ls          # list files in current directory (-la to include hidden files)
cat readme
```

**Key concept:** `ls` lists directory contents. `cat` prints file content to the terminal.

**Password:** `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

---

## Level 1 → 2

**Goal:** The password is stored in a file called `-` in the home directory.

**Commands:**
```bash
ls
cat ./-
```

**Key concept:** A filename of `-` is interpreted by the shell as stdin. Prefixing with `./` tells the shell it's a file path, not a flag.

**Password:** `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

---

## Level 2 → 3

**Goal:** The password is stored in a file called `spaces in this filename` in the home directory.

**Commands:**
```bash
ls
cat ./spaces\ in\ this\ filename
```

**Key concept:** Spaces in filenames must be escaped with `\` or wrapped in quotes: `cat "spaces in this filename"`.

**Password:** `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

---

## Level 3 → 4

**Goal:** The password is stored in a hidden file inside the `inhere` directory.

**Commands:**
```bash
ls
cd inhere/
ls -la          # -a flag shows hidden files (prefixed with .)
cat ./...Hiding-From-You
```

**Key concept:** Files starting with `.` are hidden. Use `ls -la` to reveal them.

**Password:** `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

---

## Level 4 → 5

**Goal:** The password is stored in the only human-readable file in the `inhere` directory.

**Commands:**
```bash
ls -la inhere
file ./-file07       # check file type
cat ./-file07
```

**Key concept:** The `file` command identifies file types. Human-readable means ASCII text — binary files will show as "data".

**Password:** `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

---

## Level 5 → 6

**Goal:** The password is in a file under `inhere` that is human-readable, 1033 bytes in size, and not executable.

**Commands:**
```bash
find . -size 1033c -readable
cat ./maybehere07/.file2
```

**Key concept:** `find` is powerful for filtering files by size (`-size 1033c` = 1033 bytes), permissions, and readability.

**Password:** `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

---

## Level 6 → 7

**Goal:** The password is stored somewhere on the server, owned by user `bandit7`, group `bandit6`, and is 33 bytes in size.

**Commands:**
```bash
find / -size 33c -group bandit6 -user bandit7 2>/dev/null
cat /var/lib/dpkg/info/bandit7.password
```

**Key concept:** `2>/dev/null` suppresses permission-denied errors, making output clean. Always use this when running `find` from `/`.

**Password:** `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

---

## Level 7 → 8

**Goal:** The password is in `data.txt` next to the word `millionth`.

**Commands:**
```bash
grep "millionth" data.txt
```

**Key concept:** `grep` searches for patterns in files. Pipe `cat data.txt | grep "millionth"` also works.

**Password:** `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

---

## Level 8 → 9

**Goal:** The password is the only line in `data.txt` that appears exactly once.

**Commands:**
```bash
sort data.txt | uniq --count
# Look for the line with count = 1
```

**Key concept:** `sort` groups identical lines together. `uniq -c` counts consecutive duplicates — the unique line will have a count of 1.

**Password:** `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

---

## Level 9 → 10

**Goal:** The password is in `data.txt` among human-readable strings, preceded by several `=` characters.

**Commands:**
```bash
strings data.txt | grep "==="
```

**Key concept:** `strings` extracts printable character sequences from binary files. Combine with `grep` to filter.

**Password:** `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

---

## Level 10 → 11

**Goal:** The password is in `data.txt`, encoded in Base64.

**Commands:**
```bash
base64 -d data.txt
```

**Key concept:** Base64 is an encoding scheme (not encryption) commonly used to represent binary data as text. Always easy to spot and decode.

**Password:** `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

---

## Level 11 → 12

**Goal:** The password is in `data.txt`, with all letters rotated by 13 positions (ROT13).

**Commands:**
```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

**Key concept:** ROT13 is a simple Caesar cipher. `tr` translates characters — mapping A-Z to N-ZA-M shifts each letter by 13.

**Password:** `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

---

## Level 12 → 13

**Goal:** The password is in `data.txt`, which is a hexdump of a repeatedly compressed file.

**Commands:**
```bash
# Set up a working directory
cd /tmp
mkdir my_workplace && cd my_workplace
cp /home/bandit12/data.txt .

# Reverse the hexdump to binary
xxd -r data.txt > stage1

# Iteratively check file type and decompress
file stage1                  # gzip
mv stage1 stage1.gz
gzip -d stage1.gz

file stage1                  # bzip2
mv stage1 stage1.bz
bunzip2 stage1.bz

file stage1                  # gzip again
mv stage1 stage1.gz
gzip -d stage1.gz

file stage1                  # tar
tar -xf stage1               # extracts data5.bin

file data5.bin               # tar
tar -xf data5.bin            # extracts data6.bin

file data6.bin               # bzip2
mv data6.bin data6.bz
bunzip2 data6.bz

file data6                   # tar
tar -xf data6                # extracts data8.bin

file data8.bin               # gzip
mv data8.bin data8.gz
gzip -d data8.gz

file data8                   # ASCII text - done!
cat data8
```

**Key concept:** `xxd -r` reverses a hexdump back to binary. The `file` command is essential here to identify what compression format to use at each step. Tools used: `gzip`, `bzip2`, `tar`, `xxd`.

**Password:** `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

---

## Level 13 → 14

**Goal:** No password this time — you get a private SSH key to log in as `bandit14`. The password is at `/etc/bandit_pass/bandit14`.

**Commands:**
```bash
ls
# sshkey.private

ssh -i sshkey.private bandit14@localhost -p 2220
cat /etc/bandit_pass/bandit14
```

**Key concept:** SSH supports key-based authentication with `-i <keyfile>`. Private keys are more secure than passwords.

**Password:** *MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS*

---

## Level 14 → 15

**Goal:** *send current level password through net cat to get next password*

**Commands:**
```bash
nc localhost 30000
```

**Key concept:** Using netcat to communicate with a listening service

**Password:** 8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo

---

## Level 15 → 16

**Goal:** *submit the password of the current level to **port 30001 on localhost** using SSL/TLS encryption.*

**Commands:**
```bash
openssl s_client localhost:30001 #connect to port using ssl encryption 
# note: -crlf flag optional, only needed if server doesn't respond to input
```

**Key concept:** Using OpenSSL to connect to a TLS/SSL encrypted service

**Password:** kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

---

## Level 16 → 17
**Goal:** *Find the port between 31000-32000 that accepts SSL and responds to the password, then retrieve the RSA private key.*

**Commands:**
```bash
nmap -sC -sV -p31000-32000 localhost  # scan ports in range, detect services and versions
echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31790 -ign_eof  # send password via SSL
```

**Key concept:** Using `nmap` to discover open ports and identify which one accepts SSL, then using OpenSSL to submit the password and retrieve an RSA private key for the next level.

**Enumeration result — target port:**
```bash
31790/tcp open  ssl/unknown
| ssl-cert: Subject: commonName=SnakeOil
| Not valid before: 2024-06-10T03:59:50
|_Not valid after:  2034-06-08T03:59:50
|_ssl-date: TLS randomness does not represent time
| fingerprint-strings:
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, LPDString, RTSPRequest, SIPOptions:
|_    Wrong! Please enter the correct current password.
```

**Notes:**
- Port 31790 was the correct one — only SSL port that wasn't just an echo service. The nmap output already reveals it's prompting for a password
- `-connect` explicitly specifies the host:port instead of passing it as a positional argument, which is more reliable and avoids ambiguity
- `-ign_eof` tells OpenSSL to **ignore end of file** — without it, once the piped input is fully sent the connection closes immediately before the server has time to respond with the key
- The response is an RSA private key, not a password — save it to use for SSH into bandit17
- Use `openssl s_client -help 2>&1 | less` to explore flags when manpages are lacking

**Password:** EReVavePLFHtFlFsjn3hyzMlvSuSAcRD

---
## Level 17 → 18
**Goal:** The password for the next level is the only line changed between the 2 files: "passwords.old" and "passwords.new"

**Commands:**
```bash
diff passwords.old passwords.new #difference between the files
```
**Output**:
```bash
42c42  #line 42 was changed in both files
< BMIOFKM7CRSLI97voLp3TD80NAq5exxk  #old line that was replaced
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO  #new line which is the correct password
```

**Password:** x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO

---
## Level 18 → 19
**Goal:** Get the password as soon as we establish a connection to the server

**Commands:**
```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat ~/readme" #tell ssh to execute the cat command on the file as soon as we connect to the server
```
**Output**: ```
```bash
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8  #The password for next level
```

**Key concept:** Passing a command directly to `ssh` executes it immediately on the remote server before the shell fully loads — useful when `.bashrc` is configured to terminate the session.

**Password:** cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8

---
## Level 19 → 20
**Goal:** Use the setuid binary in the home directory to read `/etc/bandit_pass/bandit20`

**Commands:**
```bash
./bandit20-do  # run without args to see usage
./bandit20-do cat /etc/bandit_pass/bandit20    # read password as bandit20
```
**Output**:
```bash
Run a command as another user.
  Example: ./bandit20-do whoami

0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

**Key concept:** `bandit20-do` has the **setuid bit** set and is owned by `bandit20`. Any command passed to it runs _as bandit20_, letting you read files owned by that user even though you're logged in as `bandit19`. This is exactly how tools like `sudo` work under the hood.

**Notes:**
- Always run an unknown setuid binary without arguments first to understand its usage
- The binary acts like a mini `sudo` — it elevates privilege for a single command In a real pen test, a misconfigured setuid binary like this would be an immediate privilege escalation path.

**Password:** 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO

---
## Level 20 → 21 

**Goal:**  Use the setuid binary `suconnect` to get the next password by running a netcat listener with the current password.

**Commands:**
```bash
echo "PREVIOUS PASSWORD" | nc -l -p 1234 & ./suconnect 1234 # start background listener and suconnect to the listener
```

**Output**:
```bash
[2] 40
Connection received on 127.0.0.1 54354
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
bandit20@bandit:~$ EeoULMCra2q0dSkYj561DX7s1CpBuOBt # Next levels password
```
**Key concept:** Background processes (`&`), netcat listeners, setuid binaries, localhost networking.

**Notes:**
- `suconnect` reads the password from your listener and verifies it — if correct, sends back Level 21's password
- `&` backgrounds the nc process so you can use the same terminal
- Port number can be anything unused (1234, 4444, etc.)

**Password:** EeoULMCra2q0dSkYj561DX7s1CpBuOBt

---
## Level 21 → 22
**Goal:** Find the password by examining cron jobs running as bandit22.

**Commands:**
```bash
bandit21@bandit:~$ cd /etc/cron.d
bandit21@bandit:/etc/cron.d$ ls
---
behemoth4_cleanup  cronjob_bandit22  cronjob_bandit24  leviathan5_cleanup    otw-tmp-dir
clean_tmp          cronjob_bandit23  e2scrub_all       manpage3_resetpw_job  sysstat
---
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
---
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cron
cronjob_bandit22.sh  cronjob_bandit24.sh  cronjob_leviathan5
cronjob_bandit23.sh  cronjob_behemoth4    crontab
---
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
---
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
---
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

**Key concept:** Cron jobs, file permissions, `/etc/cron.d` enumeration

**Notes:** 
- Cron runs the script every minute as bandit22, writing its password to a world-readable `/tmp` file
- `chmod 644` in the script is what makes the file readable by us
- Always check `/etc/cron.d` and read the actual scripts — don't stop at just seeing the cron entry
- The filename in `/tmp` is a hash — you'd never guess it, you have to read the script to find it

**Password:** tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q

---
## Level 22 → 23

**Goal:** Read a cron job script to figure out where another user's password is being stored, then retrieve it.

**Commands:**
```bash
cat /etc/cron.d/cronjob_bandit23          # see what script runs as bandit23
cat /usr/bin/cronjob_bandit23.sh          # read the script
echo I am user bandit23 | md5sum | cut -d ' ' -f 1   # compute the target filename
cat /tmp/<hash>                           # read the password
```

**Key concept:** Cron jobs run scripts automatically as specific users. By reading the script and simulating what it does (substituting `bandit23` for `whoami`), you can predict where it writes the password and retrieve it.

**Notes:** The trick is recognizing that `whoami` returns the user the script runs _as_ (bandit23), not who you are (bandit22). You manually replicate the md5 hash calculation to find the temp file.

**Password:** 0Zf11ioIjMVN551jX3CmStKLYqjk54Ga

---
## Level 23 → 24

**Goal:** **Goal:** Write a script that bandit24's cron job will execute (it runs all scripts in `/var/spool/bandit24/foo` owned by bandit23), using it to exfiltrate bandit24's password.

**Commands:**
```bash
bandit23@bandit:~$ cd /etc/cron.d
bandit23@bandit:/etc/cron.d$ ls -la | grep "bandit"
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit22
-rw-r--r--   1 root root   122 Oct 14 09:26 cronjob_bandit23
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit24
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
---output
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null

bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh
---output
#!/bin/bash
shopt -s nullglob
myname=$(whoami)

cd /var/spool/"$myname"/foo || exit
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
```

**Key concept:** 

**Notes:** 

**Password:**

---
## Level X → X
**Goal:** **

**Commands:**
```bash
command #description
```

**Key concept:** 

**Enumeration result:**
```
paste enumeration output here
```

**Notes:**

**Password:**

---

## Level X → X
**Goal:** **

**Commands:**
```bash
command #description
```

**Key concept:** 

**Enumeration result:**
```
paste enumeration output here
```

**Notes:**

**Password:**

---
## Level X → X
**Goal:** **

**Commands:**
```bash
command #description
```

**Key concept:** 

**Enumeration result:**
```
paste enumeration output here
```

**Notes:**

**Password:**

---
## Level X → X
**Goal:** **

**Commands:**
```bash
command #description
```

**Key concept:** 

**Enumeration result:**
```
paste enumeration output here
```

**Notes:**

**Password:**

---
---
---
---
---

*Writeup by [Py0Px](https://github.com/Py0Px) | Vault: Cyber*
