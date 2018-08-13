# Slin-Revision

## Chapter 4 Firewall/Access Control
In linux ``iptables`` is commonly used and ``firewalld`` runs on top of iptables

**Firewall Zones** List of ``Firewall Zones`` includes
``drop``, ``block``, ``public``, ``external``, ``dmz``, ``work``, ``home``, ``internal`` and ``trusted``

```bash
 # drop
 Incoming network packets are dropped, there is no reply. Only outgoing network connections are possible.
 
 # block
 Incoming network connections are rejected with an icmp-host-prohibited message for IPv4 and icmp6-adm-prohibited for IPv6. Only network connections initiated within this system are possible.
 
 # public
 For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.
 
 # external
 For use on external networks with masquerading enabled especially for routers. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted. Masquerading - addresses of a private network are mapped to and hidden behind a public IP address. This is a form of address translation (NAT).
 
 # dmz
 For computers in your demilitarized zone that are publicly-accessible with limited access to your internal network. Only selected incoming connections are accepted.
 
 # work
 For use in work areas. You mostly trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.
 
 # home
 For use in home areas. You mostly trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.
 
 # internal
 For use on internal networks. You mostly trust the other computers on the networks to not harm your computer. Only selected incoming connections are accepted.
 
 #trusted
 All network connections are accepted.
 ```
 
 ## Predefined configurations of each firewall zones are specified in 
 ```
 /usr/lib/firewalld/zones
 ```
 This files are not to be modified
 ## Predefined Serverices are specified in 
 ```
 /usr/lib/firewalld/services
 ```
 
 ## Runtime or Permanent
 ```
 Runtime
 ```
 
 OR 
 
 ```
 Permanent
 ```
 When in `RUNTIME` mode when the firewall is reloaded, the changes will be lost. When in ``Permanent`` mode the change is not applied immediately and would require a firewall to be reloaded. However the change will still be there even thou the firewall is reloaded
 
 When running any command with ``firewall-cmd`` command all changes are runtime. To be able to make it permanent run it with 
 ```firewall-cmd --permanent --zone=public --add-service=tftp```

To reload firewall in command line use
```firewall-cmd --reload```

# Firewall Man pages
```man firewall-cmd```
Help on the main firewall-cmd command

```man firewalld.zone```
Help on the zone xml config files and also contains the rich rules syntax

```man firewalld.service```
Help on the service xml config files

```firewalld.icmptype```
Help on the icmp xml config files

# Rich Rules
Rich rules are stored in 
``` /etc/firewalld/zones```

# All Configurations to be listed can either be found in
```cat /etc/firewalld/zones/public.xml```
OR 
```firewall-cmd --list-all```

To learn more about [FirewallD](https://www.tecmint.com/firewalld-rules-for-centos-7/)
