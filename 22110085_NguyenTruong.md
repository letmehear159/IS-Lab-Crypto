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
Creating symmetric key
```
openssl rand -base64 32 > secret.key
```
checking the private key 
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/67aa58233a5aaaea61bf0a6131b496f4435fa942/images/image%208.png"/>
we using openSSL to generate public key and private key
```
openssl genrsa -out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem
```

Checking the private and public key: 
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/bd089f5a9f4ffe9e1354e61ba87c18b921f504e0/images/image%207.png"/>
<br>
Encrypt the Symmetric Key with the Receiver's Public Key
```
openssl rsautl -encrypt -inkey receiver_public.pem -pubin -in secret.key -out secret.key.enc
```

# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests from the other host.

**Answer 1**:
