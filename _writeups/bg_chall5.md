---
layout: post
title: Beginner Quest 2021 - Twisted Robot (misc)
description: Istanbul - Bazaar | Twisted robot (misc)
date: 2021-08-01
---

We get three files: An encrypted secret (likely the flag), a list of randomly generated phone numbers, and a python program

`RoboCaller1337.py` generates phone numbers and encrypts a given secret

```python
def menu():
    print("""\n\nWelcome to the RoboCaller!! What would you like to do?
1: generate a new list of numbers
2: encrypt a super secret (in beta)
3: decrypt a super secret (coming soon!!)
4: exit""")
    choice = ''
    while choice not in ['1','2','3']:
        choice = input('>')
        if choice == '1':
            open('robo_numbers_list.txt','w').write('\n'.join(generateRandomNumbers()))
            print("...done! list saved under 'robo_numbers_list.txt'")
        elif choice == '2':
            secret = input('give me your secret and I\'ll save it as "secret.enc"')
            open('secret.enc','wb').write(encodeSecret(secret))
        elif choice == '3':
            print("stay tuned for this awesome feature\n\n")
        elif choice == '4':
            print("Thank you for using RoboCaller1337!")
    return
```

Looking at the `encodeSecret` function, it encrypts using one-time-pad with Python's `random.getrandbits`:
```python
def encodeSecret(s):
    key = [random.getrandbits(8) for i in range(len(s))]
    return bytes([a^b for a,b in zip(key,list(s.encode()))])
```

It also uses `random.getrandbits` to generate phone numbers:
```python
# This generates random phone numbers because it's easy to find a lot of people!
# Our number generator is not great so we had to hack it a bit to make sure we can
# reach folks in Philly (area code 215)
def generateRandomNumbers():
    arr = []
    for i in range(624):
        arr.append(formatNumber(random.getrandbits(32) + (1<<31)))
    return arr
```

Unfortunatly for us (but good for the world), one-time-pad is perfectly secure. However, looking at the Python documention:
> Warning The pseudo-random generators of [random] should not be used for security purposes. For security or cryptographic uses, see the secrets module.
> - [https://docs.python.org/3/library/random.html](https://docs.python.org/3/library/random.html)

After a bit of googling, we find that there are some [lovely python tools](https://github.com/tna0y/Python-random-module-cracker) that can help us figure out what the original state of `random` was when the secret was encrypted.

Assuming the author generated the secret after generating the list of random numbers, we would be able convert the phone numbers back into the original values generated by `random.getrandbits(32)` and feed it to `randcrack`. Using this, `randcrack` would be able to predict what the random bits used as the original key

```python
from randcrack import RandCrack

# Read the list of files generated (primarily) using Python's random library
with open("challenge5/robo_numbers_list.txt", "r") as file:
    numbers = file.read().split("\n")

# Convert the list of random phone numbers back into the original 32 bits
# generated by the program
rc = RandCrack()
for phone in numbers:
    rand = int(phone.replace("-", "")) - (1<<31)
    rc.submit(rand)

# Read the encrypted flag
with open("challenge5/secret.enc", "rb") as file:
    secret = file.read()

# Decrypt the flag with the predicted key 
key = [rc.predict_getrandbits(8) for i in range(len(secret))]
flag = "".join([chr(a^b) for a,b in zip(key,list(secret))])

print(flag)
```

flag: `CTF{n3v3r_3ver_ev3r_use_r4nd0m}`