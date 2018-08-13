
# Slin-Revision
# Chapter 5 Samba

## Ports Use for Samba
``PORT 445``

## Samba Passwords
For linux user to create passwords for user eg John use the command
``smbpasswd -a john``
To change password use
``smbpasswd john``

Samba password file is stored in
``/var/lib/samba/private``

To list out Samba users
``pdbedit -wL``

## Samba-only user
This people can only access `Samba Share` and they can't login to Linux
To create this linux account that is used for Samba Share we must create the user and password

To create user
``useradd -s /sbin/nologin sambauser``

To create password
``smbpasswd -a sambauser``

Samba log files are located in 
``/var/log/samba``

The ``testparm`` command can be used to test the Samba config file syntax

User access to Samba share

To edit the config file in samba the file is located in 
``/etc/samba/smb.conf``

For Anonymous users

```
public = yes
guest ok = yes
```

Specific users

```
valid users = john mary
invalid users = hacker
```

**DO NOTE THAT SAMBA SHARE BY DEFAULT IS READ-ONLY**
To allow changes to Samba shares
```
writable = yes
read only = no
```
To give write access
```
read only = yes
write list = john
```
To make samba share hidden from browsing
```
browsable = no
```

