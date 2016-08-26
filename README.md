# How to solve most basic RSA CTF's challenges

I'll assume you're using some linux distro to solve challenges. Don't use Windows <del>to solve crypto challenges</del>.

### RSA : How it works

If you want a full explanation on RSA mechanics, just go to https://en.wikipedia.org/wiki/RSA_(cryptosystem), but here's a quick review.

#### What it is

RSA is an asymmetric cryptosystem whose strength lies in the difficulty of factorizing big numbers.

#### Key generation

  * pick to prime numbers `p` and `q`
  * calculate modulus `N` : `N = p * q`
  * calculate `φ(n) = (p - 1) * (q - 1)`
  * pick public exponent `e` prime with `φ(n)`
  * calculate private exponent `d` : `d = e ** -1 mod φ(n)` 
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

```python
from Crypto.PublicKey import RSA
import gmpy

n = long(18791227133549398635268088450388409245642613659088653978153917551468974219799435280191/
95029843863294113251347949263889033893773449037418623195560116319413481180023063954171)
p = long(32807508642406493731255550386419906436234252673843934782493839340840638518957594639763)
q = long(57277214610743530253697304822463291469530207492874937459384849711645985217113052071417)
e = long(65537)

phi = (p-1)*(q-1)
d = long(gmpy.invert(e,phi))

key = RSA.construct((n,e,d))
print key.exportKey()
```
```
-----BEGIN RSA PRIVATE KEY-----
MIIBWgIBAAJIAfHq7sjugYLULdhlHxphzwoMT23pEIaQL2vrlMeQWYtc5qftvwhU
sF+Lw6hs7SfWoYtHUe8ApIayz97yWTp95GapLGElccL7AgMBAAECSAGpAxViFBwe
lYiwjth2x4hXllxygB+oDQk9gHCVzARzLhCAxQrXMT8pqwUpAAIMOMUxEItPz2cP
E0NnylI2qiZNO9mgK9sVMQIkEONKZoIvsKwDRd6Sz5yrFBcDa9FGKA2fZGXL0/QM
HjW9Z/2TAiQde9i/vqSeH0hwVo4gluOvLEREtcL4dvVFeBDnEUfRmlBuxfkCJA/5
oojDwC9FGLeAa6p/Toprlq3oQpBjCpOjhCQVKV1ugqSbhwIkDeIfckrHIV4BskRP
sLDcjcP+cYxVPsJlRE0BSI0ukFhlv4OhAiQJ9I7aAuUNDoPDccd5gWnJ2G397VIP
5e7W1Fhe7YnbO57hhcA=
-----END RSA PRIVATE KEY-----
```
  * openssl 

   Not much to say, i hope you know it already. If not : http://users.dcc.uchile.cl/~pcamacho/tutorial/crypto/openssl/openssl_intro.html


### What you should look for

  * In most cases, you'll be provided a public key so :

   Print modulus : `openssl rsa -pubin -in <pubkey> -noout -modulus`  
   Print exponent : `openssl rsa -pubin -in <pubkey> -noout -text`

  * Convert modulus to decimal and paste it at http://factordb.com

   If the challenge is easy enough, this should give you two prime numbers `p` and `q`.

  * No-brain factorization

   Sometimes you just need to run a general factorization algorithm. It could work. PS: you are brainless. Not the tools.
   Fermat : https://github.com/willyrv/FactoringNumbers  
   Msieve : https://github.com/radii/msieve  

  * Public exponent and modulus of the same size

   This means you are face a Wiener attack challenge 100% of time.  
   All the details here : https://en.wikipedia.org/wiki/Wiener%27s_attack  
   If you're a lazy fuck : https://ctfcrew.org/writeup/87  

### If things get tricky

 
