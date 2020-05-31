# Intro to Crypto 1

We are given a public key (pubkey.pem) and a message encrypted with this key (message.txt).

## Solution

At first, we want to figure out the exponent _e_ and the modulus _N_. To do so, we run the following:
```
$ openssl rsa -pubin -in pubkey.pem -text -noout -modulus
RSA Public-Key: (2047 bit)
Modulus:
    51:cf:f4:6d:9e:e3:20:96:d6:c8:06:cb:c7:df:2d:
    1d:3b:ea:7e:7b:2f:c4:e8:26:d9:fc:5e:18:79:99:
    12:dc:a1:50:b2:9c:65:c0:f9:e6:64:53:39:6c:e7:
    de:63:1a:0f:9a:67:45:13:8b:61:25:bb:cd:18:5a:
    a1:2e:b0:9a:4a:1b:d8:06:11:8c:97:a8:de:05:ed:
    0b:e6:b4:5f:c1:c9:e9:93:71:92:f5:8b:c4:a5:cc:
    27:67:80:3c:0b:21:34:2a:f5:cb:8f:34:af:fb:1a:
    6e:c2:52:0c:76:5d:87:52:1c:68:48:db:d8:31:81:
    2e:cc:6d:8b:b3:d6:17:33:b0:eb:c3:52:cf:64:d4:
    44:5c:99:55:72:92:2f:49:3d:71:89:95:9d:b2:32:
    1e:1b:ac:59:25:fa:56:dc:69:f6:85:8e:fe:eb:a0:
    a5:a9:d7:6b:a1:98:18:71:53:92:74:24:e5:f7:b6:
    80:98:ab:8c:10:44:2b:73:d1:49:02:7c:fc:37:d0:
    30:05:63:37:c3:e0:f4:21:6c:f4:32:23:96:74:41:
    b6:08:ee:c2:a6:48:e8:ce:85:78:94:c6:65:03:0c:
    01:24:56:29:27:9b:38:7f:cd:bd:c3:5b:61:67:71:
    5b:54:bd:55:56:18:0d:9a:f2:50:4b:52:7a:90:fa:
    e7
Exponent: 65537 (0x10001)
Modulus=51CFF46D9EE32096D6C806CBC7DF2D1D3BEA7E7B2FC4E826D9FC5E18799912DCA150B29C65C0F9E66453396CE7DE631A0F9A6745138B6125BBCD185AA12EB09A4A1BD806118C97A8DE05ED0BE6B45FC1C9E9937192F58BC4A5CC2767803C0B21342AF5CB8F34AFFB1A6EC2520C765D87521C6848DBD831812ECC6D8BB3D61733B0EBC352CF64D4445C995572922F493D7189959DB2321E1BAC5925FA56DC69F6858EFEEBA0A5A9D76BA198187153927424E5F7B68098AB8C10442B73D149027CFC37D030056337C3E0F4216CF43223967441B608EEC2A648E8CE857894C665030C01245629279B387FCDBDC35B6167715B54BD5556180D9AF2504B527A90FAE7
```

It is necessary to know _p_ and _q_ if we want to decrypt the message. This small python script tries to brute force one factor of _N_, based on that we can easily calculate _p_: `p = N / q` and _d_: `d = inverse(e, (p - 1) * (q - 1))` and thus decrypt the message and get the flag.
```python
from Crypto.Util.number import long_to_bytes
from Crypto.Util.number import inverse

N = 10327849034940138613515485956077213322791085874638285662823764630659653931824178919168344401508423966366637831067655701114352106747323628144645384205073278784870804834942988268503504130770762781798270763453272421050209487483563600870343875197428105079394315585993355808937811229959083289653056248770988647762812998870912510238393368777882358059256678052653963583286245796285737035786447522814310717433588049686223718247661713594680120785280795132759253149754143640871380226770164628599577669124463514838464342769690232097283333816896581904763736283142031118073027496197756777460403007359764250621763279762041468943079
e = 65537

for q in range(2,1000000):

    if N % q == 0:
        break

p = N // q
d = inverse(e, (p - 1) * (q - 1))
m = int(open("message.txt", "r").read().strip())

decrypted = pow(m, d, N)

print(long_to_bytes(decrypted).decode("utf-8"))
```
Flag: `CSCG{factorizing_the_key=pr0f1t}`

## Mitigation

Choose two large and distinct primes for _p_ and _q_, recommended is a size of 512 bits for _p_ and _q_.

