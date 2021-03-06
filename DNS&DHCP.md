# Slin-Revision
# Chapter 8 DNS&DHCP

## Setting up a basic cachin-only DNS Server using bind
Firstly install `bind` & `bind-utils` these 2 utilities are used to do DNS lookup mainly `host` & `dig`
```
yum install bind bind-utils
```
Now make sure that the group owner of the config file `/etc/named.conf` is "named"

Now finds the IP address of your original Local DNS Server by checking `/etc/resolv.conf`

It should contain 
```
# Generated by NetworkManager
domain localdomain
search localdomain
nameserver 192.168.5.2
```
The IP we are intrested in is `192.168.5.2`

**Do Note that everything in `/etc/named.conf` is very sensitive meaning missing of `;` will cause everything to fail so check carefully**

Now edit `/etc/named.conf` on the `options` tab
```
listen-on port 53 { any; }; (Changed the thing inside the curly brackets to any)
allow-query { localhost; 192.168.137.0/24; }; (Change the IP Address to the subnet of the server)
forwarders { 192.168.5.2; }; (Change to the DNS server which is nameserver in /etc/resolve.conf)
```

As the original local DNS Server may not be able top support DNSSEC, disable the DNSSEC validation by changing the following lines
```
dnssec-enable no;
dnssec-validation no;
```
Now just start the named service and set it to be automatically started on bootup
```
systemctl start named
systemctl enable named
```
To check error logs in named service it is located in `/var/log/messages`

You will need to change `/etc/resolv.conf` comment everything except `nameserver` and set it to your server ip

To make the changes in `/etc/resolv.conf` permanent you need to modify `/etc/NetworkManager/NetworkManager.conf`

Set dns = none
```
[main]
plugins=ifcfg-rh
dns=none
```
Now just restart NetworkManager

Now do a host you should see the result come from your server IP

## Setting up a Forward Lookup Zone

Firstly edit `/etc/named.conf` and declare the zone

Add in 
```
zone "example.com" IN{
  type master;
  file "example.com.zone"
};
```
After which create a new file ` vim /var/named/example.com.zone`

Add in the following content
```
$TTL 86400
example.com.       IN SOA server root (
                          42   ; serial
                          3H   ; refresh
                          15M  ; retry
                          1W   ; expiry
                          1D ) ; minimum
example.com.     	IN NS server
example.com.		IN MX 10 server

server			IN A 172.16.10.13 (Changed to server IP)
client			IN A 172.16.10.33 (Changed to client IP)
testpc             IN A 172.16.199 (Changed to random fake IP)
```
Now change the owner of `/var/etc/named/example.com.zone` to named

`chgrp named /var/named/example.com.zone`
Restart named service and it should be working now

## Setting up Reverse Lookup Zone
Edit `/etc/named.conf` and declare the reverse lookup zone

```
zone "10.16.172.in-addr.arpa" IN {    (For this line the ip address is the reverse of ur subnet without the last number)
  type master;
  file "172.16.10.zone"               (For this line the ip address is ur subnet without the last number)
};
```
Now create a reverse zone file called `/var/named/192.168.13.zone` (use ur subnet)
```
$TTL	86400
@	IN SOA server.example.com. root.server.example.com. (
			42	; serial
			28800	; refresh
			14400	; retry
			3600000	; expiry
			86400 )	; minimum
	IN NS	server.example.com.

33	IN PTR	client.example.com. (Changed to last octet of client)
13	IN PTR	server.example.com. (Changed to last octet of server)
199	IN PTR	testpc.example.com.
```
Changed Group owner to named and restart service

## Connecting DNS Server from client 
Just edit `/etc/resolv.conf` in client and comment all line in it
and change `namedserver <Server IP>`

## Perform a Zone transfer
Edit `/etc/named.conf` add a line in `Options` area
```
allow-transfer { 172.16.10.199; }; (Change the IP to something you want)
```
To do a zone transfer using `dig`
```
dig axfr @dns-server domain.name
```
To do a zone transfer using `host`
```
host -t axfr domain.name dns-server
```
