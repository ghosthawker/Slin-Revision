# Slin-Revision
# Chapter 9 Email

## Configuring PostFix to listen to external network interfaces
Check whether postfix is install else just install and start it

Use `netstat -tunap | grep master` to see which port is listening to by default it is listening to loopback interface `127.0.00.1` on Port 25

Edit `/etc/postfix/main.cf` and change the inet_interfaces
```
inet_interfaces = all  
# and comment out 
#inet_interfaces = localhost
```
Restart postifx and use netstat again it should be `0.0.0.0` now meaning it listen on all network interfaces

To send mail just use `mail <WhoEverYouWanSendTo>`

To check mail queue use `mailq`

To see mail just use `mail`

Might need to use `mutt` But highly doubt so

## Sending emails between system

If you wan send to client just edit `/etc/postfix/main.cf` in client and change `inet_interfaces = all` then restart the service

Now on server just `mail student@client.example.com`

## Configuring the SMTP Server to relay mails for internal network
Edit `/etc/postfix/main.cf` and change `mynetworks = <whatever they want>`

Restart postfix

## Configuring SMTP Server to receive mails for local domain
Edit `/etc/postifx/main.cf`

```
mydestination = $myhostname, localhost.$mydomain, localhost, example.com (Add whichever domain u need)
```

Configure Postfix to send mail using [GMAIL](https://www.linode.com/docs/email/postfix/configure-postfix-to-send-mail-using-gmail-and-google-apps-on-debian-or-ubuntu/)
