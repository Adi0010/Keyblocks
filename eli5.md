# ELI 5 Version

A key block is like packing your secret ingredient (key) inside a sealed, tamper-proof lunchbox before handing it over to someone. 
The box has a label that says what’s inside (usage), it’s locked (encrypted), and sealed (MAC) so no one can open or mess with it on the way.

Let’s expand that with your food analogy:

|🍱 Key Block | Sealed Food Container|
|------------|-----------------------|
|Cryptography | Term	Food Analogy Equivalent|
|Key	         | The food (e.g., your secret recipe or item)|
|Key Block (TR-31)	| A sealed Tupperware box|
|Header	| Label on the box: what's inside, when packed|
|Encryption	| Locking the box so only the receiver can open|
|MAC	| Tamper-evident seal to detect tampering|
|KEK	| The lock's key held by you and the receiver|


Just like you don’t give your homemade food in an open plate to a delivery guy — you pack it in a sealed, 
labeled container that only the person at the other end can open — we do the same for cryptographic keys using a key block.”
