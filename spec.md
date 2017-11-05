### Overview
* Boards are conneted in homomorph hierarchy using proprietary protocol over TCP
```
                                          +--------+
                                          | Server |
                                          ++-----+-+
                                           |     |
                                           |   . |
                                           |     |
                              +------------+   . +--------------+
                              |                                 |
                              |                .                |
                              |                                 |
                              |                                 |
                         +----+-----+                      +----+-----+
                         |Boad 0|1|0|      .    .    .     |Boad 0|1|k|
                         +-+--+--+--+                      +----------+
                           |  |  |                                |
      +--------------------+  |  +-------+                        |
      |                 +-----+          |                        +-----------+
      |                 |                |                                    |
+-----+----+      +-----+----+     +-----+----+                          +----+-----+
|Boad 0|0|1|      |Boad 0|0|2|     |Boad 0|0|3|         .    .    .      |Boad 0|0|n|
+----------+      +----------+     +----------+                          +----------+
```
* All connections are permanent, bidirectional
* Protocol is based on encrypted structured binary data exchange
* All data exchange is secure, encrypted using one time session key
* Connection is initiated by client board
* Each board is server and client at the same time and can be a node between levels
* Each board has fixed number (256) of server processes and single client process

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
