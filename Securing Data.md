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

## Create a self-signed certificate for Apache web server
Most certs are located in  `/etc/pki/tls/certs`

So to generate the cert we need to cd to `/etc/pki/tls/certs`

To generate a private key for Apache Web server and set the password
```
make httpd.key
```
Must check whether the file containing the private key is readable only by root
```
ls -l httpd.key
```
Create a self-signed cert just enter any value for the country etc
```
make httpd.crt
```
After creating the key move it to a private directory
```
mv /etc/pki/tls/certs/httpd.key /etc/pki/tls/private
```
If SSL Module is not installed
```
yum install mod_ssl
```
To configure Apache to use the certs edit the file `/etc/httpd/conf.d/ssl.conf`
Look for the lines and change it to your cert and key
```
SSLCertificateFile /etc/pki/tls/certs/httpd.crt 
SSLCertificateKeyFile /etc/pki/tls/private/httpd.key 
```

To make it a no passphrase key. Firstly cd to the private directory and use the command
```
openssl rsa httpd.key -out httpd_nopass.key
```
Afterwhich just replace the key again in `/etc/httpd/conf.d/ssl.conf`

## Set up a private Certificate Authority(CA)
Firstly you need to check whether you got these 2 directory if you dont have them create it
```
/etc/pki/CA/private	(this will store the private key of your private CA)
/etc/pki/CA/certs		(this will store the certificate of your private CA)
```
Make sure that the `the /etc/pki/CA/private ` directory is only accessible only by root

Now edit the file in `/etc/pki/tls/openssl.cnf`
```
dir = /etc/pki/CA
certificate = $dir/certs/slin-ca.crt
private_key = $dir/private/slin-ca.key
```
**DO NOTE THAT THE ABOVE CERT AND KEY NEEDS TO BE CREATED**

So now we will create them
```
touch /etc/pki/CA/index.txt
echo 01 > /etc/pki/CA/serial
```
cd to `/etc/pki/CA`

Generate a 2048 but private key using triple des
```
openssl genrsa -des3 2048 > private/slin-ca.key
```
chmod the key so that it is only accessible by root
```
chmod 600 /etc/pki/CA/private/slin-ca.key
```
Generate a self-singed certificate for your private CA
```
openssl req -new -x509 -days 365 -key private/slin-ca.key > certs/slin-ca.crt
```
When ask for values just set
```
Country Name (2 letter code) : SG
State or Province Name : Singapore
Locality Name (eg. city) : Singapore
Organization Name (eg. company) : SP
Organizational Unit Name (eg. section) : CA
Common Name : <Enter your server’s hostname, eg server>
Email Address : <Leave Blank>
```
After creating just copy it to the web server so it can be view by public
`cp /etc/pki/CA/certs/slin-ca.crt /var/www/html/pub`
Go to web browser `http://localhost/pub/slin-ca.crt` and view the cert

## Sign Apache Web Server certificate using private CA
Firstly we need to generate the private key in `/etc/pki/tls` so cd to it

Next create a private key in this case we are creating a 1024 bits key
```
openssl genrsa 1024 > private/httpd2.key
```
Make sure the private key is only accessible by root

Now generate the certificate signing request
```
openssl req -new -key private/httpd2.key -out certs/httpd2.csr
```
Enter the following when generating the certificate signing request
```
Country Name (2 letter code) : SG
State or Province Name : Singapore
Locality Name (eg. city) : Singapore
Organization Name (eg. company) : SP
Organizational Unit Name (eg. section) : Web Server
Common Name : <Enter your server’s hostname, eg server>
Email Address : <Leave Blank>
A challenge password : <Leave Blank>
An optional company name : <Leave Blank>
```
Use the CA private key to sign the CSR.
```
openssl ca -in certs/httpd2.csr -out certs/httpd2.crt
```
Now configure apache to use the certificate and private key edit `/etc/httpd/conf.d/ssl.conf `

Modify the part called `SSLCertificateFile` & `SSLCertificateKeyFile`
```
SSLCertificateFile /etc/pki/tls/certs/httpd2.crt 
SSLCertificateKeyFile /etc/pki/tls/private/httpd2.key 
```
After which just restart your apache server
## SSH with key-based authentication
On client generate a pair of private/public RSA key for SSH
```
ssh-keygen -t rsa
(* When prompt for output file name , take the default. When prompt for pass phrase , you may or may not press Enter.)
```
To check the key
`ls /home/user/.ssh`

Now just copy the key over to the server `authorized_key` folder
`scp /home/student/.ssh/id_rsa.pub serverIP:/home/student/.ssh/authorized_keys`
## Virtual Network Computing through SSH Tunnel
On server install `vnc-server`
```
yum install vnc-server
```
Edit /etc/systemd/system/vncserver@.service and modify the following line to replace <USER> with student to connect.
```
ExecStart=/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i"
PIDFile=/home/<USER>/.vnc/%H%i.pid
```
Now create a VNC password
`vncpasswd`

After which just restart the daemons
```
systemctl daemon-reload
systemctl start vncserver@:1
```
On client
install vnc package

`yum install vnc`
Connect to the server using VNC
```
vncviewer server_IP:1
```
Connect to the VNC Server through a SSH tunnel by running the following 
```
vncviewer –via serverIP localhost:1
```
## Using SSH Tunnel to do Local Port Forwarding
To block TCP ssh forwarding edit `/etc/ssh/sshd_config` and set the following value to no
```
AllowTCPForwarding no
```
