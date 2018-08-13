# Slin-Revision
# Chapter 7 System Monitoring

First thing in System Monitoring you will always need to use `netstat -tunpl` to check what ports are open on the system
You can also use another machine to nmap the system.

## Packet Sniffers
Either `tcpdump` or `wireshark`
To run `tcpdump` 
```
tcpdump -i eno16777736(replace eno16777736 with your network interface)
```
Ping your other system and see the capture packets

To only view ping packets or ICMP packets
```
tcpdump icmp -i eno16777736
```
To only view TCP
```
tcpdump tcp -i eno16777736
```
To capture fix amount of packets and store the data to a file

In this case 2000 packets
```
tcpdump -c 2000 -w /path/to/file
```

For more info on [tcpdump](https://bencane.com/2014/10/13/quick-and-practical-reference-for-tcpdump/).

## System Logging
Failed login files are located in `/var/log/secure`

To edit system logging files edit the files located in `/etc/rsyslog.conf`

In the `/etc/rsyslog.conf` in one of the lines there should be a line call `authpriv.* /var/log/secure`
edit it and add `authpriv.warning /var/log/securewarning`

```
authpriv.*  /var/log/secure
authpriv.warning /var/log/securewarning
```
What that essentially does is that when a authpriv message with priority warning and higher will be logged to the file call `/var/log/securewarning`

After editing the file always remember to restart the service
`systemctl restart rsyslog`

Use the logger command to create logged entry
```
logger -p authpriv.warning "This is a test warning"
```
```
logger -p authpriv.alert "This is a test alert"
```
```
logger -p authpriv.info "This is a test info msg"
```

## Remote Logging

On Server

edit `/etc/rsyslog.conf`

Remove the comment sign from these 2 lines
```
$ModLoad imudp
$UDPServerRun 514
```

Afterwhich restart rsyslog daemon and adjust the firewall to allow incoming connection for the rsyslog port

On Client

edit `/etc/rsyslog.conf` and change to the serverIP
```
authpriv.warning @serverip
```
Restart rsyslog daemon
Run the logger
Both warning should now appear in client and server

## Resolving IP addresses in Apache Logs

To look for Apache access log files
```
ls /var/log/httpd
```
Use `logresolve` command to resolve the IP addresses contained in the log file and Store it to a output file
```
logresolve < /var/log/httpd/<site_access_log> > /tmp/apachelog
```

## Webalizer
Need to install GeoIP first and then webalizer
`yum install GeoIP`

`yum install webalizer`

## Finding file baes on specific criteria

`find . -perm 644` Used for finding files with `-rw-r--r--`

`find / -nouser` Used for finding files that belong  to no user

`find / -mtime -1` Used for finding files that has been modified in the last day

`find / -mmin -60` Used for fiding files that have been modified in the last hour

## Listing open files(lsof)

`lsof +D /tmp` list open files in `/tmp` directory

`lsof -n -i` list files open by network-related process

## Getting usage statistics
To get disk space utilization
`df -T`
OR
`df -Th`

To see how big each user's home directory is
```
du -sh /home/*
```
Use `iostat` to see CPU and disk utilization
```
iostat
```
```
iostat -N (to view logical volume disk utilization)
```
```
iostat -m (to view in megabytes instead of kilobytes)
```

Run the `sar` command to see a summary of CPU activity.

To view available reports and see a report of CPU activity of a previous day. The log files are created on a daily basis, with the filename corresponding to the date.
```
ls -l /var/log/sa
```
```
sar -f /var/log/sa/filename | less
```

To view network statistics on each network interface
```
sar -n DEV
```

## Setting process limits

To set process limits edit the file `/etc/security/limits.conf`

To set to restrict a user to only one login append the file

```
user hard maxlogins 1
```
Use `man limits.conf` to see possible configurations

## Process Accounting
This is mainly done by the program called psacct
`yum install psacct`
Start the service

Use the command `ac -dp` to check the daily connect times for each user

Use the command `lastcomm user` to view the most recent command use by the user
