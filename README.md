# Lab 5 Key exchange
*Objective:* Key exchange allows us to pass a shared secret key between Bob and Alice. Themain methods for doing this are either encrypting with the public key, the Diffie Hellman
Method and the Elliptic Curve Diffie Hellman (ECDH) method. This lab investigates thesemethods.

## A  Diffie-Hellman 

| No   | Description                                                                                               | Result                                                                                                          |
|------|-----------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| A.1  | Bob and Alice have agreed on the values:<br> g=2879, N=9929<br>Bob selects x=6, Alice selects y=9         | Now calculate (using a calculator):<br>Bob's B value g<sup>x</sup> mod N:<br>4850<br>Alice's A value g<sup>y</sup>modN:<br>3614  |
| A.2  | Now they exchange the values. Next calculate the shared key:                                               | Bob's value A<sup>x</sup> mod N:<br>4868<>brAlice's value B<sup>y</sup> mod N:<br>4868<br>Do they match?<br> [Yes]               |
| A.3  | If you are in the lab, select someone to share a value with. Next, agree on two numbers (g and N).<br>You should generate a random number, and so should they. Do not tell them what your random number is<br>. Next, calculate your A value, and get them to do the same.<br>Next exchange values. | Numbers for g and N:<br>450, 184<br>Your x value: 2 <br>Your A value: 104<br>The B value you received: 100 <br>Shared key:<br>144<br>Do they match: [Yes]|

## B  OpenSSL   (Diffie-Hellman and ECC) 
| No   | Description                                                                                               | Result                                                                                                          |
|------|-----------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| B.1  |Generate 768-bit Diffie-Hellman parameters: <br> `openssl dhparam -out dhparams.pem-text 768`<br>View your key exchange parameters with:<br>`cat dhparams.pem`  |What is the value of g:<br>2<br>How many bits does the prime number have?<br>768 bits<br>How long does it take to produce the parameters for 1,024 bits (Group 2)?<br>2 seconds <br>How long does it take to produce the parameters for 1536 bits (Group 5)?<br>23 seconds<br>How would we change the g value?<br>`openssl dhparam -out dhparams.pem -text -5768`|
| B.2  |  Let’s look at the Elliptic curves we can create: <br>  `openssl ecparam -list_curves` <br> We can create our elliptic parameter file with: `openssl ecparam -name  secp256k1 -out secp256k1.pem`  Now view the details with:<br>`openssl ecparam -in secp256k1.pem -text -param_enc explicit -noout`<br> What are the details of the key?<br>Now we can create our key pair:<br> `openssl ecparam -in secp256k1.pem -genkey -noout -out mykey.pem`<br>                   | Name three 160-bit curves:<br>secp160k1<br>secp160r1<br>secp160r2<br>openssl ecparam -list_curves<br>What elliptic curve does Bitcoin use, according to an online search?<br>Bitcoin utilizes the secp256k1 curve for its elliptic curve cryptography, valued for its specific properties and widespread use in blockchain and cryptocurrency applications.<br>To create an elliptic parameter file, use:<br>openssl ecparam -name secp256k1 -out secp256k1.pem<br>Curve25519 is a popular curve. What are some of its common applications, as found via Google?<br>Curve25519 is a commonly used elliptic curve in cryptography (ECC) with various applications, such as:<br>• SSH Key Exchange: Curve25519 is often used in secure key exchanges for SSH, providing encrypted communication between clients and servers.<br>• Cryptographic Libraries: Integrated into many cryptographic libraries, it enables secure communication in numerous applications.<br>• Signal Protocol: Curve25519 is used in the Signal Protocol for end-to-end encryption, found in messaging apps like Signal, WhatsApp, and others.<br>• TLS (Transport Layer Security): It's used for secure communication in TLS 1.3, the latest TLS protocol version.<br>To view details of the key, run:<br>openssl ecparam -in secp256k1.pem -text -param_enc explicit -noout<br>What details are shown in the key?<br>To create a key pair, use:<br>openssl ecparam -in secp256k1.pem -genkey -noout -out mykey.pem<br>How would you use these EC parameters to carry out an ECDH key exchange?<br>To perform an ECDH (Elliptic Curve Diffie-Hellman) key exchange with the given EC parameters, follow these steps:<br>Both parties (say, Alice and Bob) agree on the same elliptic curve parameters. These include the curve equation, generator point (G), prime order (n), and the curve’s coefficients (A and B).<br>Alice and Bob each generate temporary private keys (a and b), which are random values within the range [1, n-1].<br>Using their private keys, Alice and Bob calculate their public keys as follows:<br>Alice: A = a * G<br>Bob: B = b * G<br>They exchange their public keys (A and B) over an unsecured channel.<br>Next, both parties calculate the shared secret key independently:<br>Alice: S = a * B<br>Bob: S = b * A<br>Due to the properties of elliptic curves, both Alice and Bob end up with the same shared secret key (S) without needing to exchange private keys.<br>This shared key (S) can then be used by both parties to derive symmetric encryption keys for secure communication.<br>The ECDH key exchange process enables two parties to establish a shared secret key over a network without having to share their private keys, ensuring secure key establishment for encryption.  |

## C Discrete Logarithm

### C.1 
ElGamal and Diffie Hellman use discrete logarithms. This involves a generator value (g) and a prime number. A basic operation is g <sup>x </sup>(mod p). If p=11, and g=2, determine the results (the first two have already been completed):

| x | g=2, p=11 <br> g<sup>x</sup>(mod p)|
|--------|---------------|
1|2|
2|4|
3|8|
4|5|
5|10|
6|9|
7|7|
8|3|
9|6|
10|1|
11|2|
12|4|

Note: In Python you can implement this as:
```
g=2
p=11
x=3
print (g**x % p)

```
```
What happens to the values once we go past 10?
The values were repeat

What happens to this sequence if we use g=3?
The values are repeating once they go past 10
```

### C.2
We can determine the values of g which will work for a given prime number with the following:

```
import sys
p=11
def getG(p):
for x in range (1,p):
rand = x
exp=1
next = rand % p
while (next != 1 ):
next = (next*rand) % p
exp = exp+1
if (exp==p-1):
print (rand)
print (getG(p))

```
Code: https://asecuritysite.com/dh/pickg

Run the program and determine the possible g values for these prime numbers:

```
p = 11:

2,6,7,8

p = 41:
6, 7, 11, 12, 13, 15, 17, 19, 22, 24, 26, 28, 29, 30, 34, 35

On the Internet, find a large prime number, and determine the values of g that are possible:
p =  811
3, 10, 13, 18, 21, 23, 31, 33, 48, 52, 57, 68, 70, 71, 72, 75, 78, 86, 87, 88, 90, 91, 92, 102, 111, 113, 115, 124, 126,

 127, 129, 132, 134, 135, 139, 143, 147, 157, 159, 160, 161, 162, 165, 177, 179, 186, 187, 190, 192, 193, 200, 207, 208,

 217, 218, 228, 231, 235, 242, 244, 251, 257, 272, 277, 282, 284, 288, 290, 297, 300, 302, 309, 312, 316, 317, 323, 325,

 326, 332, 336, 341, 344, 346, 348, 349, 352, 355, 360, 364, 366, 368, 370, 375, 389, 390, 394, 395, 397, 399, 404, 408,

 411, 418, 423, 425, 428, 431, 439, 442, 444, 446, 452, 453, 454, 457, 460, 466, 476, 478, 487, 489, 490, 491, 492, 493,

 496, 497, 498, 503, 504, 508, 510, 516, 517, 519, 525, 528, 530, 533, 536, 540, 541, 542, 543, 546, 550, 557, 559, 562,

 563, 565, 566, 572, 573, 578, 584, 589, 590, 597, 602, 606, 609, 616, 628, 629, 630, 635, 636, 637, 638, 639, 640, 642,

643, 644, 645, 653, 655, 660, 661, 669, 670, 671, 675, 689, 690, 697, 702, 708, 711, 714, 715, 718, 730, 731, 734, 738,

745, 748, 760, 765, 766, 767, 768, 769, 775, 776, 777, 785, 786, 787, 791, 800, 802, 805, 806

```
### C.3
We can write a Python program to implement this key exchange. Enter and run the following program:

```
import random
import hashlib
import sys
g=9
p=997
a=random.randint(5, 10)
b=random.randint(10,20)
A = (g**a) % p
B = (g**b) % p
print ('g: ',g,' (a shared value), n: ',p, ' (a prime number)')
print ('\nAlice calculates:')
print ('a (Alice random): ',a)
print ('Alice value (A): ',A,' (g^a) mod p')
print ('\nBob calculates:')
print ('b (Bob random): ',b)
print ('Bob value (B): ',B,' (g^b) mod p')
print ('\nAlice calculates:')
keyA=(B**a) % p
print ('Key: ',keyA,' (B^a) mod p')
print ('Key: ',hashlib.sha256(str(keyA).encode()).hexdigest())
print ('\nBob calculates:')
keyB=(A**b) % p
print ('Key: ',keyB,' (A^b) mod p')
print ('Key: ',hashlib.sha256(str(keyB).encode()).hexdigest())
```
Pick three different values for g and p, and make sure that the Diffie Hellman key exchange works.

```
g:  5  (a shared value), n:  23  (a prime number)

Alice calculates:
a (Alice random):  7
Alice value (A):  17  (g^a) mod p

Bob calculates:
b (Bob random):  20
Bob value (B):  12  (g^b) mod p

Alice calculates:
Key:  16  (B^a) mod p
Key:  b17ef6d19c7a5b1ee83b907c595526dcb1eb06db8227d650d5dda0a9f4ce8cd9

Bob calculates:
Key:  16  (A^b) mod p
Key:  b17ef6d19c7a5b1ee83b907c595526dcb1eb06db8227d650d5dda0a9f4ce8cd9
```
```
g:  3  (a shared value), n:  27  (a prime number)

Alice calculates:
a (Alice random):  8
Alice value (A):  0  (g^a) mod p

Bob calculates:
b (Bob random):  14
Bob value (B):  0  (g^b) mod p

Alice calculates:
Key:  0  (B^a) mod p
Key:  5feceb66ffc86f38d952786c6d696c79c2dbc239dd4e91b46729d73a27fb57e9

Bob calculates:
Key:  0  (A^b) mod p
Key:  5feceb66ffc86f38d952786c6d696c79c2dbc239dd4e91b46729d73a27fb57e9

```
```
g:  7  (a shared value), n:  811  (a prime number)

Alice calculates:
a (Alice random):  5
Alice value (A):  587  (g^a) mod p

Bob calculates:
b (Bob random):  11
Bob value (B):  69  (g^b) mod p

Alice calculates:
Key:  7  (B^a) mod p
Key:  7902699be42c8a8e46fbbb4501726517e86b22c56a189f7625a6da49081b2451

Bob calculates:
Key:  7  (A^b) mod p
Key:  7902699be42c8a8e46fbbb4501726517e86b22c56a189f7625a6da49081b2451
```
```
Can you pick a value of g and p which will not work?

No, but this can bring vulnerabilities, form arriving at the same shared key.

```

The following program sets up a man-in-the-middle attack for Eve:

```
import random
import base64
import hashlib
import sys
g=15
p=1011
a= 5
b = 9
eve = 7
message=21
A=(g**a) % p
B=(g**b) % p
Eve1 = (A**eve) % p
Eve2 = (B**eve) % p
Key1= (Eve1**a) % p
Key2= (Eve2**b) % p
print ('g: ',g,' (a shared value), n: ',p, ' (a prime number)')
print ('\n== Random value generation ===')
print ('\nAlice calculates:')
print ('a (Alice random): ',a)
print ('Alice value (A): ',A,' (g^a) mod p')
print ('\nBob calculates:')
print ('b (Bob random): ',b)
print ('Bob value (B): ',B,' (g^b) mod p')
print ('\n==Alice sends value to Eve ===')
print ('Eve takes Alice\'s value and calculates: ',Eve1)
print ('Alice gets Eve\'s value and calculates key of: ',Key1)
print ('\n==Bob sends value to Eve ===')
print ('Eve takes Bob\'s value and calculates: ',Eve2)
print ('Bob gets Eve\'s value and calculates key of: ',Key2)

```



## D Elliptic Curve Diffie-Hellman (ECDH)

ECDH is now one of the most used key exchange methods, and uses the Diffie Hellman
method, but adds in elliptic curve methods. With this Alice generates (a) and Bob generates
(b). We select a point on a curve (G), and Alice generates aG, and Bob generates bG. They
passthe valuesto each other, and then Alice received bG, and Bob receives aG. Alice multiplies
by a, to get abG, and Bob will multiply by b, and also get abG. This will be their shared key. 

### D.1
In the following we will implement ECDH using the secp256k1 curve (as used in Bitcoin). Confirm that Bob and Alice will have the same shared key.

In the following we will implement ECDH using the secp256k1 curve (as used in
Bitcoin). Confirm that Bob and Alice will have the same shared key.

 Web link (ECDH): https://asecuritysite.com/hazmat/hashnew13

```
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.asymmetric import ec
from cryptography.hazmat.primitives.kdf.hkdf import HKDF
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.backends import default_backend
import binascii
import sys
size=32 # 256 bit key
Bob_private_key = ec.generate_private_key(ec.SECP256K1(),default_backend())
Alice_private_key = ec.generate_private_key(ec.SECP256K1(),default_backend())
Bob_shared_key = Bob_private_key.exchange(ec.ECDH(), Alice_private_key.public_key())
Bob_derived_key =
HKDF(algorithm=hashes.SHA256(),length=size,salt=None,info=b'',backend=default_backend()).deri
ve(Bob_shared_key)
Alice_shared_key = Alice_private_key.exchange(ec.ECDH(), Bob_private_key.public_key())
Alice_derived_key =
HKDF(algorithm=hashes.SHA256(),length=size,salt=None,info=b'',backend=default_backend()).deri
ve(Alice_shared_key)
print ("Name of curve: ",Bob_private_key.public_key().curve.name)
print (f"Generated key size: {size} bytes ({size*8} bits)")
vals = Bob_private_key.private_numbers()
print (f"\nBob private key value: {vals.private_value}")
vals=Bob_private_key.public_key()
enc_point=binascii.b2a_hex(vals.public_bytes(encoding=serialization.Encoding.PEM,format=seria
lization.PublicFormat.SubjectPublicKeyInfo)).decode()
print("Bob's public key: ",enc_point)
vals = Alice_private_key.private_numbers()
print (f"\nAlice private key value: {vals.private_value}")
vals=Alice_private_key.public_key()
enc_point=binascii.b2a_hex(vals.public_bytes(encoding=serialization.Encoding.PEM,format=seria
lization.PublicFormat.SubjectPublicKeyInfo)).decode()
print("Alice's public key: ",enc_point)
print ("\nBob's derived key: ",binascii.b2a_hex(Bob_derived_key).decode())
print("Alice's derived key: ",binascii.b2a_hex(Alice_derived_key).decode())

```


 


