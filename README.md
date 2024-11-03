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
| B.2  |  Let’s look at the Elliptic curves we can create: <br>  `openssl ecparam -list_curves` <br> We can create our elliptic parameter file with: `openssl ecparam -name  secp256k1 -out secp256k1.pem`  Now view the details with:<br>`openssl ecparam -in secp256k1.pem -text -param_enc explicit -noout`<br> What are the details of the key?<br>Now we can create our key pair:<br> `openssl ecparam -in secp256k1.pem -genkey -noout -out mykey.pem`<br>                   | Bob's value A<sup>x</sup> mod N:<br>4868<>brAlice's value B<sup>y</sup> mod N:<br>4868<br>Do they match?<br> [Yes]               |

