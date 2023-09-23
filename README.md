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

   Not much to say, I hope you know it already. If not : http://users.dcc.uchile.cl/~pcamacho/tutorial/crypto/openssl/openssl_intro.html

### What you may have to do

From what I experienced in previous ctf, here's what you may have to do in order to solve an RSA challenge :

  * Recover private key from public key and decrypt the message

   In those cases, you will be provided one or more RSA public key. That means you know ```modulus``` and ```public exponent```. If you factorize the ```modulus``` you will be able to recover private key.

  * Use math to break the cipher without private key

	Those kind of RSA challenges are usually the trickiest ones. Depending on the ctf level, you may need from basic to medium math understanding. I must recommand the [stackexchange crypto forum](https://crypto.stackexchange.com/) which contains numerous valuable threads.

### What you should look for

  * In most cases, you will be provided a public key so :

   Print modulus : `openssl rsa -pubin -in <pubkey> -noout -modulus`  
   Print exponent : `openssl rsa -pubin -in <pubkey> -noout -text`

  * Convert modulus to decimal and paste it at http://factordb.com

   If the challenge is easy enough, this should give you two prime numbers `p` and `q`.

  * Factorization algorithms

   Sometimes you just need to run a general factorization algorithm. It could work.   
   Fermat : https://github.com/willyrv/FactoringNumbers  
   Msieve : https://github.com/radii/msieve  

  * Public exponent and modulus of the same size

   This means you are facing a **Wiener attack** challenge 100% of the time.  
   All the details here : https://en.wikipedia.org/wiki/Wiener%27s_attack  
   If you are a **lazy fuck** : https://ctfcrew.org/writeup/87  

  * Public exponent = 3

   A low public exponent (like 0x03) means you should use the **Chinese Remainder Theorem**.   
   Those kind of RSA challenges usualy implies one encrypted message send to three people. You'll be given three public keys and three ciphertexts (from the same plaintext).   
   If so, here's how you can recover the plaintext assuming e = 3:

```
# Let's have:
c0, c1, c2: ciphertexts
n0, n1, n2: moduli
```
```
# We want to solve the following system:
m = c0 ** 3 % n0
m = c1 ** 3 % n1
m = c2 ** 3 % n2

# Here's how this is done thanks to the CRT:
M =  c0 * (n1*n2) * [(n1*n2) ** −1] % n0
   + c1 * (n0*n2) * [(n0*n2) ** −1] % n1
   + c2 * (n0*n1) * [(n0*n1) ** −1] % n2

# with [x ** -1] % n the multiplicative inverse of x modulo n
# use gmpy.invert(x,n) for this calculation in python

# Calculate the cubic root of this result to recover pt:
plaintext = M ** (1/3)
```

  * Faulty signature with CRT optimization

  Florian Weiner's paper : https://people.redhat.com/~fweimer/rsa-crt-leaks.pdf
  
  I recently discovered a paper about a very interesting attack implying the Chinese Remainder Theorem. Long story short, this theorem says that if you're working in Z/nZ (ie. modulus n) and you have factors of n (e.g. p and q) which are coprime pairwise, you can split the computation in Z/qZ * Z/pZ. This is very interesting as it is much faster to do so than working with huge modulus.

  When a message is signed with an RSA private key with CRT optimization, the calculus are split as in Z/qZ and Z/pZ. If a fault happen in the Z/qZ part, an attacker could deduce p (thus q -> d -> private key) with the faulty signature and the original message and vice versa. 

  So, if you encounter a challenge with a faulty signature story maybe you
should try this:

```
# Let's have:
y: faulty signature
x: original message
e: public exponent
n: modulus
```
```
q = gmpy.gcd(pow(y,e)-x,n)
p = n / q
d = gmpy.invert(e,(p-1)*(q-1))
```
