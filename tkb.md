Thales’s proprietary “TKB” format (key scheme “S”) refines TR-31 with additional flexibility. 

A Thales Key Block has four parts -
1. a 16-byte clear header (including a field for the Thales LMK ID), 
2. an optional TLV-encoded header section, 
3. the encrypted key data, 
4. and a cryptographic authenticator. 

For example, the optional header may include tags for key set ID, block version, algorithm ID, validity dates, etc. 
The payload is encrypted (3DES or AES) under a key derived from the HSM’s LMK, using part of the header as the IV
Finally a MAC (DES CBC-MAC or AES-CMAC) covers ```header ‖ optional-header ‖ ciphertext``` for integrity

In practice, only Thales payShield HSMs create or consume TKBs. The header’s LMK-ID field ties the block to a specific LMK partition (LMK-AES or LMK-3DES)

Because of the optional TLV fields, TKB can annotate keys richly (e.g. policy flags). A TKB is not interoperable with other vendors’ HSMs – it is “trusted” only on Thales machines

Multiple LMK variants allow one HSM to maintain separate key domains. Python Example (Thales TKB): 

Below is a simplified simulation of TKB assembly. We construct a 16-byte header and a TLV optional header (e.g. tag “KS” for key set). We then encrypt the key under an AES key (standing in for a LMK-derived key) and compute AES-CMAC over all.

```python
from Crypto.Cipher import AES
from Crypto.Hash import CMAC

# Construct header (16 bytes). For example: version='S', usage='G0', LMK id, etc.
header = b'S' + b'\x00' + b'G0' + b'\x00'*12
# Optional header TLV: Tag='KS', length=2, value=0x0001 (just an example)
opt = b'KS' + b'\x02' + b'\x00\x01'

clear_key = b'0123456789ABCDEF'  # key data to encrypt (16 bytes)
# Encrypt payload under "LMK-derived" key (simulate with AES-ECB or CBC)
lmkk = AES.new(b'ThalesLMK123456', AES.MODE_CBC, iv=header[:16])
enc_key = lmkk.encrypt(clear_key)

# Compute MAC (AES-CMAC) over header+opt+enc_key
mac = CMAC.new(b'\xAB'*16, ciphermod=AES)
mac.update(header + opt + enc_key)
auth = mac.digest()

tkb = header + opt + enc_key + auth
print("Thales TKB:", tkb.hex())
```
On a real payShield, loading a key-block involves HSM commands (e.g. KU) that specify the key scheme S and verify the MAC. 
The HSM uses its LMK so that the wrapped key is never exposed in the clear. Optional fields (key-set ID, algorithm tag, validity dates) are parsed by the HSM to enforce key policies.
