# encrypt.life-python
A simplistic prototype of encrypt.life in python. Focus has been on the QC-MDPC part, not the protocol. You may find vulnerabilities in the current implementation. Also, because of the numpy FFT, prime power is not used.

# High-level description of the desired final result

##The sender side
The protocol is based on Fujisaki-Okamoto

###Possibile vulnerabilities

#####Decryption oracle
The protocol can be designed using normal McEliece or Niederreiter. In case of McEliece, the error vector should be part of the authentication (for instance, generate MAC using a concatenation of message and error vector). Such a measure will mitigate the usual decryption oracle attack, described below.

```
1. Intercept an encrypted message.
2. Pick a random bit the ciphertext.
3. Flip it. If decryption fails, this was not an error position.
4. Repeat until all error positions have been unraveled.
```

Obviously, there is an implicit assumption that the receiver will either reject any error larger than T or the decoder will fail (which is rarely the case).

If the protocol instead is designed using the Niederreiter model, the error vector will be/encode the token. In this case, there is no need to authenticate the error vector, since any flipped bit in the cipher text will cause the receiver to deocde a different token, hence breaking the decryption oracle.

#####Distinguishing attacks

The simplest imaginable distinguisher will detect a constant-error encryption with probability 1. 

```
1. Pick a ciphertext block with block weight l and error weight w.
2. Sum all symbols mod 2 and check if it equals (l + w) mod 2.
```

The theory is described in more detail [here](https://grocid.net/2015/01/28/attack-on-prime-length-qc-mdpc/)

#####Squaring/subcode attacks

Squaring attacks exploit that (the now deprecated) p = 4800 = 2⁶ × 75. By squaring the polynomial, the vector space decreases in size by a factor 2. It also causes collisions in the error vector, making it to decrease in weight. This allows an attacker to go quite far below 80-bit security. See [this paper](http://link.springer.com/article/10.1007/s10623-015-0099-x).

![protocol sender](https://raw.githubusercontent.com/grocid/encrypt.life-python/master/sender.png)

##The receiver end
![protocol receiver](https://raw.githubusercontent.com/grocid/encrypt.life-python/master/receiver.png)

#Academic papers
[MDPC-McEliece: New McEliece Variants from Moderate Density Parity-Check Codes](https://eprint.iacr.org/2012/409.pdf)

[Lightweight Code-based Cryptography: QC-MDPC McEliece Encryption on Reconfigurable Devices](https://www.date-conference.com/files/proceedings/2014/pdffiles/03.3_1.pdf)

[Squaring attacks on McEliece public-key cryptosystems using quasi-cyclic codes of even dimension](http://link.springer.com/article/10.1007/s10623-015-0099-x)

#Acknowledgements
Miroslav Kratchovil (creator of [codecrypt](https://github.com/exaexa/codecrypt)) for pointing out a weakness in the protocol.