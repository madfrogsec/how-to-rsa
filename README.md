# How to solve most basic RSA CTF's challenges

I'll assume you're using some linux distro to solve challenges. Don't use Windows to solve challenges.

### RSA : How it works

If you want a full explanation on RSA mechanics, just go to https://en.wikipedia.org/wiki/RSA_(cryptosystem), but here's a quick review.

#### What it is

RSA is an asymmetric cryptosystem whose strength lies in the difficulty of factorizing big numbers.

#### Key generation

  * pick to prime numbers `p` and `q`
  * calculate modulo `N` : `N = p * q`
  * calculate `φ(n) = (p - 1) * (q - 1)`
  * pick public exponant `e` prime with `φ(n)`
  * calculate private exponant `d` : `d = e ** -1 mod φ(n)` 
  * `((n,e))` is your public key
  * `((n,e,d))` is your private key

#### Encryption

  * plaintext is `m`  
  * ciphertext is `c`
  * `c = m ** e mod N`

#### Decryption

  * `m = c ** d mod N`


### Useful tools

  * pyCrypto
   
   This is an invaluable tool. Might even steal your girlfriend. Allow you to easily generate or manipulate keys, encrypt, decrypt and so much more, in Python of course. Check it here : https://www.dlitz.net/software/pycrypto/ !

  * openssl 

   Not much to say, i hope you know it already. If not : http://users.dcc.uchile.cl/~pcamacho/tutorial/crypto/openssl/openssl_intro.html

### What you should look for

  * In most cases, you'll be provided a public key so :

   Print modulus : `openssl rsa -pubin -in <pubkey> -noout -modulus`  
   Print exponent : `openssl rsa -pubin -in <pubkey> -noout -text`

  * Convert modulus to decimal and paste it at http://factordb.com

   If the challenge is easy enough, this should give you two prime numbers `p` and `q`.

  * Public exponent and modulus of the same size

  * No-brain factorization

   Sometimes you just need to run a general factorization algorithm. It could work. PS: you are brainless. Not the tools.
   Fermat : https://github.com/willyrv/FactoringNumbers  
   Msieve : https://github.com/radii/msieve  

  * Run some factorization algorithms

   This could take some time so better run them early


### If things get tricky

 
