# ECC

## Resources

[Group Theory | Brilliant Math & Science Wiki](https://brilliant.org/wiki/group-theory-introduction/)

[Picking a Base Point in ECC](https://medium.com/asecuritysite-when-bob-met-alice/picking-a-base-point-in-ecc-8d7b852b88a6)

[What’s The Order in ECC?](https://medium.com/asecuritysite-when-bob-met-alice/whats-the-order-in-ecc-ac8a8d5439e8)

- [https://github.com/ethereumbook/ethereumbook/blob/develop/04keys-addresses.asciidoc#elliptic_curve](https://github.com/ethereumbook/ethereumbook/blob/develop/04keys-addresses.asciidoc#elliptic_curve)
[Secp256k1 | River Glossary](https://river.com/learn/terms/s/secp256k1/)

[Discrete Log Problem (DLP) | River Glossary](https://river.com/learn/terms/d/discrete-log-problem-dlp/)

[Elliptic curves secp256k1 and secp256r1](https://www.johndcook.com/blog/2018/08/21/a-tale-of-two-elliptic-curves/)

[Why did Satoshi decide to use secp256k1 instead of secp256r1? - DappWorks](https://dappworks.com/why-did-satoshi-decide-to-use-secp256k1-instead-of-secp256r1/)

[Exploring Elliptic Curve Pairings](https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627)

## Elliptic Curve Cryptography

$$y^2 = x^3 + ax^2 + bx + c$$

secp256k1: used by Bitcoin and Ethereum to implement public key cryptography. Elliptic curve over a field $z_p$ where $p$ is a 256-bit prime.

ECDSA: Elliptic Curve Digital Signature Algorithm

Public key cryptography uses this method to calculate public keys which is a point on ECC curve.

$$K = (k * G) \% p$$

- K = 512-bit public key
- k = 256-bit randomly generated private key
- G = base point on the curve
- p = prime number

Take a base point $G$, add it $n$ (private key) times to make $nG (\% p)$ (public key).

> **Note**: addition here means addition in elliptic curve and not addition in field of integers mod p.

Order of a base point is when keys generated using this point starts to form a cycle. Max number of points on the curve.

Thus, choosing a good base point is necessary in any public key generation curves.

secp256k1:

```other
x = 0x79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798
y = 0x483ADA7726A3C4655DA4FBFC0E1108A8FD17B448A68554199C47D08FFB10D4B8
p = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F
```

The order is:

```other
N = FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141
```

Ethereum public keys are serialization of 130 hex characters

```other
04 + x-coord (64) + y-coord (64)
```

> 04 is prefixed as it is used to define uncompressed point on the ECC.

Ethereum addresses are hexadecimal numbers, identifiers derived from the last 20 bytes of the Keccak256 hash of the public key.

## EIP55

Mixed capitalisation of letters in the address

Take keccak256 hash of the address, capitalize character if hex digit of hash is greater than 8.

## Discrete Log Problem (DLP)

describes that there are currently no known method for calculating point division on an elliptic curve.

### Why Discrete logarithm?

ECC is significant because solving $k*G$ is trivial but obtaining $k$ from product $k*G$ is not.

k*G can be obtained using Fast-Exponentiation algorithm but solving for k requires computing discrete logarithms.

## Security

Big-O Notation of discrete logarithm problem is $O(\sqrt{n})$.

Base point G, is chosen to be closer to $2^{256}$ and thus is in the order of 256.

So, $\sqrt{256} = 128$ bits level of security is provided by curves like secp256k1.

## secp256k1 v/s secp256r1

secp256k1 is a Koblitz curve defined in a characteristic 2 finite field while secp256r1 is a prime field curve.

Not going into details as to what a characteristic 2 finite field is, we can specify secp256r1 as a pseudo-randomised curve and secp256k1 as completely random curve which can’t be solved using discrete logarithm problem **yet**.

## BLS Signatures


## To-Read

1. [https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627](https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627)
2. [https://eth2book.info/altair/part2/building_blocks/signatures](https://eth2book.info/altair/part2/building_blocks/signatures)