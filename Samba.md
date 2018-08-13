
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

# Steps in creating a Samba Share
Install Samba server and the client
``yum install samba``
``yum install samba-client``

Start samba service and make it start automatically on bootup
``systemctl start smb``
``systemctl enable smb``

Create a directory for sharing
``mkdir /samba_share``
Change its group owner to the group in which the user you want to share to is in
``chgrp testgrp /samba_share``
And give full access
``chmod 775 /samba_share``

Next edit the config file which is located in `/etc/samba/smb.conf`
Add this to the end of the file
```
[myshare]
comment = My samba share for the grp
path = /samba_share
guest ok = yes
```

To access Home directories through Samba
edit `/etc/samba/smb.conf`
Check that the `/etc/samba/smb.conf` contains the following lines
```
[homes]
  comment = Home Directories
  valid users = %S, %D%w%S
  browseable = No
  read only = No
  inherit acls = Yes
 ```
 If its not that add it in.
 
 Next turn on SELinux Boolean samba_enable_home_dirs(make the setting persistent)
 ```
 setsebool -P samba_enable_home_dirs on
 ```
