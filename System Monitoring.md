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
