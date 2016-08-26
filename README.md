# How to solve most basic RSA CTF's challenges

### RSA : How it works

If you want a full explanation on RSA mechanics, just go to https://en.wikipedia.org/wiki/RSA_(cryptosystem), but here's a quick review.

#### What it is

RSA is an asymmetric cryptosystem whose strength lies in the difficulty of factorizing big numbers.

#### Key generation

  * pick to prime numbers `p` and `q`
  * calculate modulo `N` : `N = p * q`
  * calculate `φ(n) = (p - 1) * (q - 1)`
  * pick public exponant `e` prime with φ(n)
  * calculate private exponant `d` : `d = e ** -1 mod φ(n)` 
  * `((n,e))` is your public key
  * `((n,e,d))` is your private key

#### Encryption

  * plaintext is `m`  
  * ciphertext is `c`
  * `c = m ** e mod N`

#### Decryption

  * `m = c ** d mod N`

### What you should look for

### If things get tricky

 
