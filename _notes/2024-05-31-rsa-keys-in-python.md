---
title:       "Generating RSA Keys in Python"
description: ""
tags:        [Python, RSA]
math:        true
redirect_from:
 - /2024/05/31/rsa-keys-in-python.html
---



- TOC
{:toc}

## Actionable Takeaways
```python
from Crypto.PublicKey import RSA

key = RSA.generate(2048)

print("PUBLIC KEY:")
print("N =", key.n)
print("E =", key.e)

print("PRIVATE KEY:")
print("D =", key.d)
```



## The RSA Algorithm[^1]
The RSA algorithm relies on the fact that for sufficiently large $$n$$, $$e$$, and $$d$$ it can be shown that for all integers $$m$$ such that $$0 \leq m < n$$ the values of $$(m^e)^d$$ and $$m$$ are congurent modulo $$n$$.  Written as an equation:

$$ (m^e)^d \equiv m \  (\operatorname{mod}n) $$

Here $$m$$ is the message, $$e$$ the encryption key, and $$d$$ the decryption key.  The value of $$n$$ is found by multiplying two large primes together.  

The **public** key is $$n$$ and $$e$$, which can be freely distributed.  

The **private** key is $$d$$, which must remain secret.

It is also critical that the two prime numbers used to generate $$n$$ remain secret, since with them the value of $$d$$ can be easilt calculated.

**NOTE:** The values of $$e$$ and $$d$$ can be swapped.  This is because $$(x^m)^n = x^{(mn)}$$ and $$mn = nm$$.  Generally, the smaller of the two values is used for $$e$$ and the larger for $$d$$.

## Python's `PyCryptodome` Module
Installed with the command `pip install PyCryptodome` at the command line (or with `!pip install PyCryptodome` in a Jupyter notebook).

Provides the class `Crypto.PublicKey.RSA.RsaKey`[^2] with a `generate()` function and reports to values of `n`, `e`, `d`, etc.

```
from Crypto.PublicKey import RSA

print(RSA.generate(2048)) 
```

Python conveniently has builtin support for big integers.

## Key Generation
First, $$n$$ is found by selecting two prime numbers $$p$$ and $$q$$, where $$n = pq$$.

In practice, $$e$$ is always taken to be $$65,537$$, and the value of $$d = e^{-1} (\operatorname{mod}n)$$, in Python: `d = pow(e, -1, n)`.

Theoretically, the value of $$e$$ is chosen by using either Euler's or Carmichael's totient functions.  However, using $$65,537$$ is considered secure and makes calculating the encrypted value faster because $$65,537 = 2^{16} + 1$$.



## (En|De)crypting with the Key

Using the RSA key is easy with `Crypto.Cipher.PKCS1_OAEP`[^5]:

```
from Crypto.Cipher import PKCS1_OAEP

cipher = PKCS1_OAEP.new(key)

ciphertext = cipher.encrypt(b"this is a test")

print(cipher.decrypt(ciphertext))
```

It is possible (although not advised, due to attacks against improperly padded messages) to just do the math directly:

```
# convert string to binary string to integer
message = int(''.join(bin(ord(c))[2:].zfill(7) for c in "test"), 2)

# encrypt
encrypted = pow(message, key.e, key.n)

# decrypt
decrypted = pow(encrypted, key.d, key.n)

# convert int to binary string to string
''.join([chr(int(bin(decrypted)[i+2:i+9], 2)) for i in range(0, len(bin(decrypted)[2:]), 7)])
```

The construct `(a ** b) % c` is extremely slow compared to `pow(a, b, c)`.

## Python's `gmpy` Module[^3]
Python's builtin support for big integers can be slow for certain operations (e.g. int to string conversion[^4]).

The `gmpy` module provides improved performance.

Not strictly necessary, but possibly useful if performance slowdowns are seen.




[^1]: [Wikipedia - RSA (cryptosystem)](https://en.wikipedia.org/wiki/RSA_(cryptosystem))  

[^2]: [PyCryptodome Documentation - Crypto.PublicKey - RSA](https://pycryptodome.readthedocs.io/en/latest/src/public_key/rsa.html#Crypto.PublicKey.RSA.RsaKey)

[^3]: [gmpy2 Documentation](https://gmpy2.readthedocs.io/en/latest/index.html)

[^4]: [StackOverflow - Answer by `gmpy` author/maintainer](https://stackoverflow.com/a/1386735)

[^5]: [PyCryptodome Documentation - Crypto.Cipher - PKCS1_OAEP](https://pycryptodome.readthedocs.io/en/latest/src/cipher/oaep.html)
