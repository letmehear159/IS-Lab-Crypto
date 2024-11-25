# Lab #2,22110085, Nguyen Truong, INSE330380E_01FIE
# Task 1: Transfer files between computers  
**Question 1**: 
Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 

**Answer 1**:
<br>
Install requirement for transfering files
<br>
apt update
<br>
apt install openssl: For ensure authenticity and integrity
<br>
apt install netcat-traditional: For transfering file
<br>
For the first virtual computer: <br> 
Create plain_text file for transmitting: 
```sh
echo "This is a plain text file." > plain_text.txt
```
Create a hmac file name fileMac.hmac with secret key : 25892589
```sh
openssl dgst -sha256 -mac HMAC -macopt key:25892589 -out fileMac.hmac plain_text.txt
```
After creating two neccessary files, i started to transmitting to another computer:
For the second virtual computer: <br>
Checking the ip of this computer: 
```sh
ifconfig
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/f5ab19341d48bbc2e3ee6e20ef3942060015507e/images/image1.png"/>
start listening on port 3034 and receiving plain_text.txt
```sh
nc -l -p 3034 > files.txt
```
start listening on port 3034 and receiving fileMac.hmac 
```sh
nc -l -p 3034 > fileMac.hmac
```
<img src="https://github.com/letmehear159/IS-Lab-Crypto/blob/b12130ae8cf5e9d51e70e7db03426f4e7a5c0198/images/image%202.png"/>
On the first virtual computer: <br>
Start sending two files via port and ip address.
# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:


# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests from the other host.

**Answer 1**:
