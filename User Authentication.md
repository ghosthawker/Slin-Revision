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
