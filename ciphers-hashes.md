- [Caesar Box Cipher](#caesar-box-cipher)
- [Rail Fence Cipher](#rail-fence-cipher)
  - [Encoding](#rf-encoding)
  - [Decoding](#rf-decoding)
- [Vigenere Cipher](#vigenere-cipher)
  - [Encoding](#v-encoding)
  - [Decoding](#v-decoding)
- [RSA](#rsa)
  - [Encoding](#rsa-encoding)
  - [Decoding](#rsa-decoding)
    - [Small e Attack](#small-e-attack)
- [NTLM](#ntlm-windows-passwords)


# Caesar Box Cipher
[Online Decoding](https://www.dcode.fr/caesar-box-cipher)

# Rail Fence Cipher
Also called zigzag

[Online Decoding](https://www.dcode.fr/rail-fence-cipher)

## RF Encoding
```
Input: Example123, 3 levels, no offset

| E |   |   |   | p |   |   |   | 2 |   |
|   | x |   | m |   | l |   | 1 |   | 3 |
|   |   | a |   |   |   | e |   |   |   |

Output: Ep2xml13ae
```

## RF Decoding
Need to know the number of levels

```
Input: Ep2xml13ae, 3 levels

Create the zigzag with the correct number of letters/levels

| X |   |   |   | X |   |   |   | X |   |
|   | X |   | X |   | X |   | X |   | X |
|   |   | X |   |   |   | X |   |   |   |

Fill in the letters by line

| E |   |   |   | p |   |   |   | 2 |   |
|   | x |   | m |   | l |   | 1 |   | 3 |
|   |   | a |   |   |   | e |   |   |   |

Read off the diagonals

Output: Example123
```

# Vigenere Cipher
Requires an alphabet and a key

[Online Decoding](https://www.dcode.fr/vigenere-cipher)

## V Encoding
Add the first letter of the plaintext and the first letter of the key and add their values
together (A = 0, B = 1, etc.). The value of the output is the letter in the output. Continue
with the next letter of the plaintext and key. If the key is shorter than the plaintext,
when you get to the last letter of the key, go back to the beginning. 

```
Input: Example123, Key: abcdxyz

E = 4  ^ A = 0  -> Out           = 4  = E
X = 23 ^ B = 1  -> Out           = 24 = Y
A = 0  ^ C = 2  -> Out           = 2  = C
M = 12 ^ D = 3  -> Out           = 15 = P
P = 15 ^ X = 23 -> Out = 38 % 26 = 12 = M
L = 11 ^ Y = 24 -> Out = 35 % 26 = 9  = J
E = 4  ^ Z = 25 -> Out = 29 % 26 = 3  = D
1               -> Out                = 1
2               -> Out                = 2
3               -> Out                = 3

Output = Eycpmjd123
```

## V Decoding
Subtract the first letter of the key from the first letter of the cipher. If the result
is negative, add the number of letters in the alphabet. If you get to the end of the key,
go back to the beginning.

```
Input: Ewbkrie123, Key: azbycx

E = 4  ^ A = 0  -> Out            = 4  = E
W = 22 ^ Z = 25 -> Out = -3 + 26  = 23 = X
B = 1  ^ B = 1  -> Out            = 0  = A
K = 10 ^ Y = 24 -> Out = -14 + 26 = 12 = M
R = 17 ^ C = 2  -> Out            = 15 = P
I = 8  ^ X = 23 -> Out = -15 + 26 = 11 = L
E = 4  ^ A = 0  -> Out            = 4  = E
1               -> Out                 = 1
2               -> Out                 = 2
3               -> Out                 = 3

Output = Example123
```

# RSA
[Online Decoding](https://www.dcode.fr/rsa-cipher)

1. Generate 2 prime numbers `p`, `q` 
2. Calculate `n = pq`
3. Choose `e` such that `1 < e < (p-1)(q-1)`
3. Calculate `d` such that `de = 1 % (p-1)(q-1)` 
4. The public key is generated from `n` and `e`
5. The private key is generated from `d`, `p`, and `q`

## RSA Encoding
Convert the plaintext into an integer `m`. The output `c = m^e % n`

```
Input: SKY

p = 13, q = 83
n = 13 * 83 = 1079
e = 43
d = 43^(-1) % (12)(82) = 595

(From ASCII)
S = 83, K = 75, Y = 89

c_1 = 83^43 % 1079 = 996
c_2 = 75^43 % 1079 = 894
c_3 = 89^43 % 1079 = 379

Output: 996 894 379
```

## RSA Decoding
The amount of needed information depends on the security of the selected primes. If `p`, `q`
are sufficiently small then you may only need some of the information. 
`m = c^d % n`

```
Input: 894 82 379 852, n = 1079, e = 43

n = pq -> pq = 1079  ; attain this through prime factors. In this case, only 13, 83 are possible
p = 13, q = 83

de = 1 % (p-1)(q-1) -> d = 43^(-1) % (12)(82) = 595

m_1 = 894^595 % 1079 = 75
m_2 = 82^595  % 1079 = 82
m_3 = 379^595 % 1079 = 89
m_4 = 852^595 % 1079 = 71

Convert to ASCII

Output: KRYG
```

### Small e Attack
When e is small (m^e < N) then you can decrypt using `m = c^(1/e)`. If it's not quite that small, then
you can progressively add multiples of `n` until there's a valid answer: `m = (c + know)^(1/e)`

# NTLM (Windows) Passwords
Can crack with rainbow tables in `ophcrack`

## Examples
- FBBDA33FC12E83FB0C240E84A183686E:DDE9DC6E34E2E6E11EF9E51C6B27ED96
- 21C4E7C2EFE8E8D1C00B70065ED76AA7:A7A0F9AFD4A78F531A1CF4C42E531BBF
- 21259DD63B980471AAD3B435B51404EE:1E43E37B818AB5EDB066EB58CCDC1823

