# Lab #2,22110085, Nguyen Truong, INSE330380E_01FIE
# Task 1: Transfer files between computers  
**Question 1**: 
Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 

**Answer 1**:
<br>
## Step 1: Install requirement for transfering files

apt update
<br>
apt install openssl: For ensure authenticity and integrity
<br>
apt install netcat-traditional: For transfering file
<br>
## Step 2: Creating files 
For the first virtual computer: <br> 
Create plain_text file for transmitting: 
```sh
echo "This is a plain text file." > plain_text.txt
```
Create a hmac file name fileMac.hmac with secret key : 25892589
```sh
openssl dgst -sha256 -mac HMAC -macopt key:25892589 -out fileMac.hmac plain_text.txt
```
## Step 3: Transmitting files 
After creating two neccessary files, i started to transmitting to another computer:
For the second virtual computer: <br>
Checking the ip of this computer: 
```sh
ifconfig
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/f5ab19341d48bbc2e3ee6e20ef3942060015507e/images/image1.png"/>
<br>
start listening on port 3034 and receiving plain_text.txt:

```sh
nc -l -p 3034 > files.txt
```
start listening on port 3034 and receiving fileMac.hmac 

```sh
nc -l -p 3034 > fileMac.hmac
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/b12130ae8cf5e9d51e70e7db03426f4e7a5c0198/images/image%202.png"/>
On the first virtual computer: <br>
Start sending two files via port and ip address

```sh
cat plaint_text.txt | nc -w 3 172.17.0.3 3034
```

```sh
cat fileMac.hmac | nc -w 3 172.17.0.3 3034
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/2ed7bba6613bf06ce59f3d131ad356a9d32c4793/images/image%203.png"/>
On the second virtual computer we see that 2 two files after receiving:
Checking the contain of files.txt from plain_text.txt 
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/adf6377d98eb12af7bdd6bbf67fa357b7fdf305d/images/image%204.png"/>

## Step 4: Checking the hmac Code

Checking the hmac code value from hmac file and one from using openSSh with key 25892589 and content in files.txt<br>
I used diff for comparing the string value of received fileMac.hmac and one from using openSSh with key 25892589 and content in files.txt

```sh
 diff fileMac.hmac  <(openssl dgst -sha256 -mac HMAC -macopt key:25892589 files.txt)
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/adf6377d98eb12af7bdd6bbf67fa357b7fdf305d/images/image%204.png"/>

Conclusion: Eventhough the string hmac value of two file is the same but there still different when comparing by using diff 
# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:
## Step 1: Prepare RSA keys.
In the sender computer:
### Creating symmetric key
```
openssl rand -base64 32 > secret.key
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/3ae7a726d7258a787c1a60e458a204c731489c0c/images/sender_secret_key_checking.png"/>

Encrypt the plain_text.txt with content ("This is a plain text.") by using the secret key 
```
openssl enc -aes-256-cbc -salt -pbkdf2 -in plain_text.txt -out plain_text.enc -pass file:./secret.key
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/9cc10eee57a04de7fafa9659ddaae4b15ceb0f75/images/encryptedText.png"/>


### In the receiver computer 
we using openSSL to generate public key and private key
```
openssl genrsa -out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem
```

### Checking the private and public key: 
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/ac498df9da650532a7a1a90181adb3ac5f189c54/images/image%206.png"/>

### Sending public key from receiver computer to sender computer
```
cat public_key.pem | nc 172.17.0.2 3302
```
In sender computer listening and checking the public key:
```
nc -l -p 3302 > receiver_public.pem
```
```
 cat receiver_public.pem
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/9cc10eee57a04de7fafa9659ddaae4b15ceb0f75/images/receiver_public_key.png"/>

### Encrypt the Symmetric Key with the Receiver's Public Key
```
openssl pkeyutl -encrypt -inkey receiver_public.pem -pubin -in secret.key -out secret.key.enc
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/9cc10eee57a04de7fafa9659ddaae4b15ceb0f75/images/encrypt_secret_key.png"/>

### Sending encrypted text and encypted key in sender computer
```
cat plain_text.enc | nc 172.17.0.3 3302
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/9cc10eee57a04de7fafa9659ddaae4b15ceb0f75/images/sending_plain_text.enc.png"/>

```
cat secret.key.enc | nc 172.17.0.3 3302
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/9cc10eee57a04de7fafa9659ddaae4b15ceb0f75/images/sending_secret_key.png"/>

### Checking files received and starting to decrypt content
Receiving encrypted plain text
```
nc -l -p 3302 > plain_text.enc
```
Checking string value of plain text
<br>
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/9cc10eee57a04de7fafa9659ddaae4b15ceb0f75/images/receiver_plaintext_enc.png"/>
<br>
Receiving encrypted secret key
```
nc -l -p 3302 > secret.key.enc
```
Checking string value of secret key
<br>
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/9cc10eee57a04de7fafa9659ddaae4b15ceb0f75/images/receiver_checking_secret_key_encryption.png"/>

### Decrypt the secret key and encrypted plain text
Decrypt the secret key
```
openssl pkeyutl -encrypt -in secret.key -inkey receiver_public.pem -pubin -out secret.key.enc
```
Checking secret key value after decrypted 
<br>
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/3ae7a726d7258a787c1a60e458a204c731489c0c/images/secret_key_decrypted.png"/>
<br>
Compare secret key to the sender from receiver, THEY ARE SIMILAR!!!
<br>
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/3ae7a726d7258a787c1a60e458a204c731489c0c/images/sender_secret_key_checking.png"/>
<br>
Decrypt the plain text
```
openssl enc -d -aes-256-cbc -pbkdf2 -in plain_text.enc -out plain_text.decrypss file:./secret.key
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/3ae7a726d7258a787c1a60e458a204c731489c0c/images/receiver_decrypt_content.png"/>
<br>
By comparing the content from sender, THEY ARE SIMILAR!!!
<br>
<img  src="https://github.com/letmehear159/IS-Lab-Crypto/blob/07badafa1fd00c4ab3b02c1242fef16d092d0444/images/sender_plain_text.png"/>

### Conclusion:
I had sent the encrypted file through 2 computer using symmetric key and RSA Asymmetric key
<br>
# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests from the other host.
**Answer 1**:
### Step 1: Install requirement for virtual computer
Running the command to install Web and SSH Server, iptables
```
apt-get install iptables
apt-get install apache2
apt-get install openssh-server
```
Usage of each requirement:
- Iptables: For managing Fire Wall
- Apache2: A Server
- OpenSSH-Server: allows to set up a secure, encrypted connection to your computer from another computer

### Start Apache2 and OpenSSH server
Start Apache2 server
```
service apache2 start 
```
Start OpenSSH server
```
service ssh start
```
<img  src="https://github.com/letmehear159/IS-Lab-Crypto/blob/b4b4a2fb92b7f008963b269fba8add03cee4dc94/images/start_server.png"/>

### Configure iptables Rules
#### Allow all incoming HTTP traffic (port 80)
```
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```
#### Allow all incoming SSH traffic (port 22)
```
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```
#### Allow all ICMP (ping) requests
```
iptables -A INPUT -p icmp -j ACCEPT
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/e15daa6dffae0f18ffaa6ee9e44dff1329320b82/images/allow_port.png"/>

#### Explain: 
- -A Input: This specifies that we are appending (-A) a rule to the INPUT chain
- -p tcp: This specifies that the rule applies to packets using the TCP protocol
- --dport: This specifies the destination port (--dport) that the rule applies to (example: 80 and 22)
- -j ACCEPT: This specifies the target of the rule. In this case, -j ACCEPT means that packets matching this rule will be accepted

## Testing access from another vm
#### Install Curl and Ping Tools
```
apt-get install curl inetutils-ping
```
I use ifconfig to check the ipaddress of the web server:
```
ifconfig
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/ad5f3647048ad327762bb638b8991a93803a6195/images/ip_address_server.png"/>

### Check HTTP Access
By knowing the ip address of the web server I run this comment to test access:
```
curl http://172.17.0.2
```
This command returns fully HTML CSS of the webserver
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/ad5f3647048ad327762bb638b8991a93803a6195/images/full_html_css.png"/>
To help it be more clear and easier to read running this command 
```
curl -I http://172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/aac2e0da1613f6d8184c381e82111404efd6f66e/images/first_http_header.png"/>

Explain:
 - -I helps the webserver only return HTTP header

### Check ICMP (Ping)
Use ping command to check the ICMP Connection to the server 
```
ping 172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/746e4b2fff2bee8342ac0c4ce6bdba41b0ea1003/images/ping.png"/>

I sent package successfully so the connect is good.

### Check SSH Access
I need to create a user for SSH Server
I created a user name nhatkyvienphuon
```
adduser nhatkyvienphuon
```
Then continue adding neccessary information.
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/da71995527ed298bc95f3b4eb3ec7147741d5066/images/add_user.png"/>
After finishing switch to the other VM To test SSH Acess
Running this code to test where this VM can SSH into SSH Server:
```
ssh nhatkyvienphuon@172.17.0.2
```

Explain: 
- nhatkyvienphuon: The user on SSH server
- 172.17.0.2: IP address of SSH Server
Typing password and the result is welcome statement to Ubuntu
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/da71995527ed298bc95f3b4eb3ec7147741d5066/images/login_success.png"/>

From the image, i have successfully SSH into SSH Server

## Block HTTP, ICMP, and SSH Requests
To delete Accept From the previous task, running code:
```
iptables -D INPUT 1
iptables -D INPUT 2
iptables -D INPUT 3
```
Explain: 
- 1,2,3 is the line number i got by running this code ```iptables -L -n --line-numbers```
#### Block all incoming HTTP traffic (port 80)
```
iptables -A INPUT -p tcp --dport 80 -j DROP
```
#### Block all incoming SSH traffic (port 22)
```
iptables -A INPUT -p tcp --dport 22 -j DROP
```

#### Block all ICMP (ping) requests
```
iptables -A INPUT -p icmp -j DROP
```

### Testing Access from another VM
#### Check HTTP access
```
curl -I http://172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/a5332e731ef0f769be594b7873795870803fdf71/images/fail_http_header.png"/>
==> Failed to connect

#### Check ICMP (Ping)
```
ping 172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/a5332e731ef0f769be594b7873795870803fdf71/images/ping_failed.png"/>
==> Failed to connect

#### Check SSH access
```
ssh nhatkyvienphuon@172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/a5332e731ef0f769be594b7873795870803fdf71/images/fail_ssh.png"/>
==> Failed to connect

## Unblock HTTP, ICMP, SSH requests:
#### Checking the line number in IPtables 
```
iptables -L -n --line-numbers
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/268f190aba51177b22c6c8947854be536c1bd0f8/images/checking_line_number.png"/>

#### Deleting blocks requests
```
iptables -D INPUT 1
iptables -D INPUT 2
iptables -D INPUT 3
```
### Checking HTTP, ICMP, SSH requests
#### Check HTTP access
```
curl -I http://172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/aac2e0da1613f6d8184c381e82111404efd6f66e/images/second_http_header_final.png"/>

#### Check HTTP access
```
ping 172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/268f190aba51177b22c6c8947854be536c1bd0f8/images/second_ping.png"/>

#### SSH access
```
ssh nhatkyvienphuon@172.17.0.2
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/268f190aba51177b22c6c8947854be536c1bd0f8/images/second_ssh.png"/>

### Conclusion: I have successfully created the web server and ssh server, testing connect to server, block/unblock the requests.
