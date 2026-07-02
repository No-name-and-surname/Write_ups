# ☆ Challenge Description

>Adrien's been looking at ways to encrypt his messages with the help of symbols and minus signs. Can you find a way to recover the flag?

>Challenge files:
>  - [source.py](https://cryptohack.org/static/challenges/source_734d7e14251f950935f83d228f8694ab.py)
>  - [output.txt](https://cryptohack.org/static/challenges/output_80fc6398d2fd9f272186d0af510323f9.txt)

## Let's look through given algorithm:

We are given the parameters $a$ and $p$, along with the encrypted `flag`.

### Known Values:
* $a = 238260533169915$
* $p = 1007621497415251$
* $e = randint(1,p)$
### Encryption Algorithm:
The flag string is converted into bits. Then, the algorithm processes the flag bit by bit in a loop:
* If `bit == 1`:
  $$\text{print}(a^e \pmod p)$$
* If `bit == 0`:
  $$\text{print}(-a^e \pmod p)$$

---

## Solution

Well, we can't find the real e for each bit, so I think we should try one thing..

```python
>>> pow(a,(p-1)//2,p)
1
```

Wow, that's really helpfull! We've figured out that the base element $a$ is a **quadratic residue (QR)** modulo $p$!

Based on the properties of the Legendre symbol and quadratic residues:
1. The element $a$ raised to any power $e \pmod p$ will always remain a **quadratic residue (QR)**.
2. The value $-a^e \pmod p$ (which is $a^e \cdot (-1) \pmod p$) will always be a **quadratic non-residue (QNR)**. This holds true because $-1$ is a QNR for this specific modulus $p$, since $p \equiv 3 \pmod 4$ (Euler's criterion yields $(-1)^{\frac{p-1}{2}} \equiv -1 \pmod p$).

### Attack Strategy:
To recover the flag, we simply need to apply **Euler's criterion** to each element of the ciphertext:
* If the ciphertext element is a quadratic residue $c^{\frac{p-1}{2}} \equiv 1 \pmod p$, then the original bit is `1`.
* If the ciphertext element is a quadratic non-residue $c^{\frac{p-1}{2}} \equiv -1 \pmod p$, then the original bit is `0`.

After extracting all the bits, we decode them back into a string to get the final flag! ^^

Here is the main solution, to get bits of flag:

```python
for c in ciphertext:
  w= pow(c, (p-1)//2, p)
  if w == 1:
    print('1',end='')
  else:
    print('0',end='')
```
    
