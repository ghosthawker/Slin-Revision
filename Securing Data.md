# Slin-Revision
# Chapter 6 Securing Data

To generate random number
```
hexdump /dev/random
```

Use OpenSSL library to generate 8 random bytes(in BASE64 to get printable characters)
```
openssl rand -base64 8
```

Create a MD5 fingerprint on a file
```
md5sum /file/uwant > /output/file
```

To encrypt file with triple des
```
openssl des3 -base 64 -in /input/file -out /output/file
```
Next enter the password

To decrypt the file
```
openssl des3 -d -base64 -in /file/todecrypt/
```

## Asymmetric encryption
To generate public/private key it is important to note you will have to log into the user to be able to generate the key

Once loging into the user use the command
```
gpg --gen-key
```
Accept all the default values then for the name use their real name. 

To list key the command to use is
```
gpg --list-keys
```
```
gpg --list-secret-keys
```
Normally you will need to export the key so use
```
gpg -a --export <NameOfKeyEgAliceTan> > /output/file
```
To import from another user
```
gpg --import /output/file
```

Now for another user to encrypt the file using other people public key use
```
gpg --recipient <KeyName> -a -o /output/ciphertext -e /file/toencrypt
```
The `/output/ciphertext` refers to once encryption has been done it will be outputed to the file `output/ciphertext`

To decrypt 
```
gpg -o OutputText -a -d /output/ciphertext
```
The `OutputText` refers to when u decrypt the content will be store in OutputText

To learn more about [Signing a file](https://www.thegeekstuff.com/2013/04/gnupg-digital-signatures/).
