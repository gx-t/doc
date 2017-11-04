##  Board Key Table
Every board has sqlite database with preinstalled key table of 32 bytes (256 bits) length 256 AES keys identified by 1 byte key id.
## Secure Data Exchange Protocol
1.  Client randomly selects key from key DB.
2.  Client generates 32 bytes length random diversifier.
3.  Client generates 32 bytes length session key based on selected key and diversifier.
4.  Client calculates sha256 hash on payload to be sent.
5.  Client encrypts payload using session key.
6.  Client encrypts calculated hash.
7.  Client sends 1 byte length key id, 32 bytes length diversifier, encrypted data and encrypted hash.
8.  Server reads key id and selects key from key DB.
9.  Server reads 32 bytes length diversifier.
10. Server generates session key based on selected key and diersifier.
11. Server decrypts data using session key.
12. Server calculates sha256 hash on decrypted data.
13. Server decrypts received hash.
14. Server compares calculated hash with decrypted one
15. Server sends response code
