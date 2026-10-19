# Cryptography

## Symmetric Cryptography - Uses same key for both encryption and Decryption

### Block ciphers - encrypt fixed-size blocks of data at a time

* Advanced Encryption Standard (AES) - 128, 192, 256-bits

  * DES
  * 3DES (Older, now largely deprecated)
  * Blowfish
  * Two fish
  * IDEA
  * Modes of Operation - Determines how blocks are chained together

    * ECB
    * CBC
    * CTR
    * GCM

### Stream ciphers

* Encrypt data one bit or byte at a time - useful for continuous streams
* Types

  * RC4 (now considered insecure)

    * ChaCha20
    * Salsa20

## Asymmetric Cryptography

* uses a mathematically linked key pair
* A public key to encrypt (or verify) and a private key to decrypt (or sign)
* It's slower but solves the key-distribution problem and enables digital signatures
* Types

  * RSA - based on the difficulty of factoring large numbers, used for encryption and signatures

    * **Diffie-Hellman (DH)**, used for **secure key exchange rather than encrypting messages directly**.
    * Elliptic Curve Cryptography (ECC) - Gives equivalent security to RSA with much smaller keys

      * Variants include **ECDH for key exchange** and **ECDSA for signatures**
      * ECDH
      * ECDSA
    * ElGamal, based on the discrete logarithm problem
    * post-quantum algorithms like **CRYSTALS-Kyber** and **CRYSTALS-Dilithium**

      * Designed to resist attacks from quantum computers

## A quick contrast

* Symmetric is fast but requires securely sharing the key beforehand; asymmetric is slower but removes that problem.
* In practice they're combined—for example, **Transport Layer Security (TLS) uses asymmetric crypto to exchange a symmetric key, then switches to symmetric crypto for the actual data**.

  * TLS1.2

    * **TODO**???
    * **TODO**???
  * TLS1.3

    * **TODO**???
    * **TODO**???





\# 1. Clone your Cryptography repository

git clone https://github.com/NaveenSEC/Cryptography.git

cd Cryptography



\# 2. Open in VS Code

code .



\# 3. Make your edits, then stage \& commit

git status

git add .

git commit -m "Add cryptographic algorithms and scripts"



\# 4. Push directly to the main branch

git push origin main

