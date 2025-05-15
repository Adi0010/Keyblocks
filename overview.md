keyblocks are standardized data structures used to store and transmit cryptographic keys along with metadata that describes how the key should be used. 
These structures ensure interoperability, security, and proper key management across cryptographic systems.



There are already several key block implementations, which are in an active use:

But I think of these 3 types

BASE24 Key Exchange
TR-31
Thales Key Block

why I think these 3 will be good candidates ? 
1) Base24 Key Exchange - base24 is in the name 
2) TR-31 - Common hai
3) Thales ----

  

| **Feature**             | **Base24 (Legacy)**                                                                 | **ANSI TR-31 Key Block**                                                                                 | **Thales Key Block (TKB)**                                                                 |
|-------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| **Origin**              | Proprietary ACI/host system protocol                                                | ANSI X9.24 Technical Report (TR-31)                                                                       | Proprietary Thales (payShield HSMs)                                                        |
| **Structure**           | No formal block; keys exchanged as raw ciphertexts under ZMK                        | Structured: clear header + encrypted key + MAC                                                            | Structured: 16-byte header + optional TLV + encrypted key + MAC                            |
| **Key Encryption**      | Keys wrapped using ZMK, often via 3DES                                              | Keys encrypted under a Key Encryption Key (KEK), typically AES or 3DES                                    | Keys encrypted under LMK-derived key (AES or 3DES), IV based on header                     |
| **MAC/Authentication**  | None (in legacy mode)                                                               | Mandatory MAC (e.g., AES-CMAC) to ensure integrity of header and key data                                 | AES-CMAC or DES-CBC-MAC over full block                                                     |
| **Interoperability**    | Limited to Base24 systems                                                           | Standardized and interoperable across HSMs supporting TR-31                                               | Proprietary to Thales HSMs only                                                             |
| **Usage Restrictions**  | Usage implied by context, not cryptographically bound                               | Key usage type locked into header (e.g., PIN, data encryption)                                            | Usage, algorithm, key set and policy flags encoded into header and TLV                     |
| **Export Control**      | Not enforced cryptographically                                                       | Explicit field for exportability in header                                                                | Export permissions embedded in header/TLV                                                   |
| **PCI Compliance**      | Non-compliant (unless migrated to TR-31/TR-34 by 2025)                              | PCI PIN compliant format                                                                                   | PCI PIN compliant (when used with Thales HSM)                                               |
| **Migration Path**      | Typically migrating to TR-31 for ATM key exchange                                   | Adopted for secure key loading and transport in open ATM/POS systems                                      | Used in modern Thales-only environments or integrated key management systems               |


