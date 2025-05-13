# Base24 KEY Exchange
In legacy ATM/POS systems, Base24 used a proprietary “zone” key scheme rather than standardized blocks. 
Keys like the Zone PIN Key (ZPK), Terminal PIN Key (TPK), and Data Encryption Key (DEK) are distributed encrypted under a shared Zone Master Key (ZMK) between sites. 
For example, a host HSM can generate a new ZPK and return it encrypted under both the ZMK and the HSM’s LMK

1. The ATM sends a PIN encrypted under its TPK, with that TPK wrapped by the ZMK; 
2. the host HSM decrypts the TPK (and PIN) using its LMK, 
3. then re-encrypts the PIN under the ZPK for onward processing

In practice, Base24’s legacy key exchange involves raw key payloads (no formal header or MAC as in standardized blocks). 
A typical flow is: 
1. the terminal PIN pad encrypts the PIN with its TPK; 
2. the terminal also sends the TPK encrypted under the ZMK to the host; 
3. the host HSM decrypts the TPK with its LMK, recovers the PIN, then re-encrypts the PIN under a higher-level key (often called ZPK or interchange key) for submission. 
    All keys (ZPK, TPK, Terminal Data Key, etc.) are managed by the HSM and distributed wrapped under the ZMK

The host maintains a “KEYF” file of encrypted working keys and their check values, without a unified block format. 
Under recent PCI/PIN standards, Base24 platforms have started supporting ANSI key-block formats (TR-31 for working keys, TR-34 for PIN keys) for more secure key import/export. 

In future, Base24 may package keys in TR-31/TR-34 blocks when loading to ATMs

Base24-style key wrapping: Below is a simple Python demo of wrapping a 3DES key under a ZMK using CBC (a rough analog of Base24’s ZMK encryption). 
In a real HSM/Base24 system, variant bits and key check values would also be handled.
```python
from Crypto.Cipher import DES3
from Crypto.Random import get_random_bytes

# Sample 2-key 3DES ZMK and working key (16 bytes each)
zmk = DES3.adjust_key_parity(get_random_bytes(16))
work_key = get_random_bytes(16)

# Use DES3-CBC with zero IV to wrap (illustrative; actual scheme may vary)
iv = b'\x00' * 8
cipher = DES3.new(zmk, DES3.MODE_CBC, iv)
wrapped = cipher.encrypt(work_key)

print("ZMK:", zmk.hex())
print("Work key wrapped under ZMK:", wrapped.hex())
```
