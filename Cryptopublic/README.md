This is a concept for a Global Identity/communication system that maintains end user privacy while providing authorization and authenticity of higher level systems.  


**Key Traits**  
- PKI signing used as primary method of authorization of identity and global communications
- Shared Symmetric key is used for Local Data Access
- Encryption at rest with decryption performed only by the end user.
- Access to the network is contained within a "Shared" local IPV6 network. Local Network is duplicated on a peer to peer level. 
- The traditional internet is used primarily for encrypted backups between networks or the publishing of global routing/signing keys  
- Organized in groups of 10 (scaling up to 1 billion users via 10^9 groupings).
- Each group relies on Shared secrets to encrypt a "Master Private key" (requiring 6/10 majority)
- Master private key allows for managment of continuity in event of major vulnerability or rewrite. 
  

**Cryptography Standards Used**  
- FIPS 203 Encryption  
- FIPS 204 Signature

**Example Global Identity File**  
```yaml
CryptoID: [1248243esda] (Hashed identity value provides anonymity but prevents identity disclosure)  
[name, birthdate, 3-character salt] (The 3-character salt acts as a memorable password for users to reconstruct their ID)  
Username: somoking  
Private Address: fde7:f3d2:4a5a::/48  
Tags150char: [Cybersecurity, Nerd, AntiIdeology, Weeb, ~30, TG]  
Public Key: [Public Key]  
[Signing Sheet for Clubs]  
```
**Example User Key File**  
```yaml
SharedKey: Raw Symmetric key
SharedKeyPartner: [Hashed User ID]
SharedKResourceRoute:  URL Private Address and resource type e.g. `ipv6/mailbox` or ipv6/Feed`
```

**User Registration Flow**
- User visit in person office
- User uses Airgapped office terminal to create a Universal Public Identity and Private keys
- User's Public Identity is signed by local Office with the Official Office Signing Key and uploaded to the "Global Register" 
- User is provided a copy of their private keys (Literal paper copy or USB key)

**User activity flow**
- User visits local office.
- User accesses local network.
- User navigates to message box on Private Address that contains encrypted messages.
- Users device decrypts messages on device using private key.
- User uploads signed encrypted files to Private Address to act as social media content.
- Local office verifies content is signed by address owner and allows upload.
- Friend of user navigates to address and is able to decrypt signed encrypted files due to preshared key.

**Custodian Workflows**
- Verify user data for upload via public Signature
- Prioritize the data that remains stored. 

