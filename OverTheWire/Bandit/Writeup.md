This Is My Writeup For The OverTheWire Bandit Wargame

ssh bandit0@bandit.labs.overthewire.org -p 2220 // connect to the server using ssh (password is bandit0)

#Bandit0 ---> Bandit 1
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

#Bandit1 ---> Bandit2
"The password for the next level is stored in a file called - located in the home directory"
>>ls
  -
>>cat ./-
  263JGJPfgU6LtdEvgfWU1XP5yac29mFx

#Bandit2 ---> Bandit3
"The password for the next level is stored in a file called --spaces in this filename-- located in the home directory"
>>ls
  --spaces in this filename--
>>cat ./--spaces\ in\ this\ filename--
  MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx

#Bandit3 ---> Bandit4


  
