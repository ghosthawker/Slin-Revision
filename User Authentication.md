# Slin-Revision
# Chapter 10 User Authentication

In most Unix and Linux system the encrypted password are stored in `/etc/shadow` and it is only readable by root

To set Password expiry info edit `/etc/login.def`

## Pam Control Flags
Controls what should be done if authentication by individual module succeeds or fails. Each group of modules will return an overall Success or Failure to the calling application

```bash
# requiste
If a module fails, overall Failure returned immediately. Remaining modules in group not executed. If module succeeds, remaining module are executed. TLDR Modules must succeed for overall result to be Success

# required
If module succeeds or fails, remaining modules in group executed. TLDR Module must succeed for overall result to be Success

# sufficient
If module succeeeds, overall Success returned immediately. Remaining modules in stack not executed. If module failed, remaining modules executed. TLDR Module does not need to succeed for overall result to be Success

# optional
Does not matter if module succeeds or fails. Used for task like optional logging

#include
Include all lines of files specified in arguments
```

## PAM_PERMIT to allow access without password
Edit `/etc/pam.d/login` add it on top
```
auth sufficient pam_permit.so
```
Switch to virtual terminal (**Ctrl-Alt-F2**) login to any user you don't need password

## Using PAM_LISTFILE to control access to vsftpd service
Edit `/etc/pam.d/vsftpd`
```
auth required pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
```
Then add user to `/etc/vsftpd/ftpusers`

This user can no longer ftp to your server

## PAM_TALLY to track login attempts
Firstly view `/etc/pam.d/login` you should notice it includes `system-auth`

Edit `/etc/pam.d/system-auth`

Add the following line as the 2nd line in auth group
```
auth required pam_env.so
auth required pam_tally2.so deny=3 unlock_time=100 (ADD THIS LINE TO BLOCK AFTER 3 FAILED LOGING BLOCK FOR 100SECONDS)
```
Add the following line as the 2nd line in account group
```
account required pam_unix.so
account required pam_tally2.so (ADD THIS LINE)
```
Run `pam_tally` to view failed attempts

To reset use `pam_tally2 --user username --reset`

## PAM_TIME to restrict access to services based on time
`man pam_time` & `man time.conf` Both are important

To use pam_time for any service just add
```
account    required    pam_time.so
```
In this case we will do for vsftpd so edit 
`/etc/pam.d/vsftpd` and add it in.

To restrict a user to use vsftpd on Mon edit `/etc/security/time.conf`
```
vsftpd;*;alvin;!Mo0000-2400
```

## Allocating admin task for normal users 
Use `visudo`

Add this to allow a user(In this case it's simon)who can use `systemctl` to start `httpd`

```
simon  ALL=/usr/bin/systemctl * httpd
```
This will allow them to use it

To allow them to edit the config file use
```
simon  ALL=/usr/bin/systemctl * httpd, /bin/vi /etc/httpd/*
```
More info on [visudo](https://www.garron.me/en/linux/visudo-command-sudoers-file-sudo-default-editor.html)
