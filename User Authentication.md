# Slin-Revision
# Chapter 10 User Authentication

In most Unix and Linux system the encrypted password are stored in `/etc/shadow` and it is only readable by root

To set Password expiry info edit `/etc/login.def`

## Pam Control Flags
Controls what should be done if authentication by individual module succeeds or fails. Each group of modules will return an overall Success or Failure to the calling application

