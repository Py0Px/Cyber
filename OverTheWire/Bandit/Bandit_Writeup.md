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

**Goal:** *(to be completed)*

**Commands:**
```bash

```

**Key concept:** 

**Password:** 

---

## Level 15 → 16

**Goal:** *(to be completed)*

**Commands:**
```bash

```

**Key concept:** 

**Password:** 

---

## Level 16 → 17

**Goal:** *(to be completed)*

**Commands:**
```bash

```

**Key concept:** 

**Password:** 

---

*Writeup by [Py0Px](https://github.com/Py0Px) | Vault: HackerMan*
