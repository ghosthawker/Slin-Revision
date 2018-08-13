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
## System Logging
