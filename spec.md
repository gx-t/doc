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
* All data exchange is secure, encrypted, using one time session key
* Connection is initiated by client board
* Each board is server and client at the same time and can take the role of a node between levels
* Each board has fixed number (256) of server processes and single client process
* Each data block consists of command and following data
* Command list is extendable
* Data block may contain several commands and data, commands may be chained in more complex action
* The reponse is encrypted with same session key

## Protocol Layers
```
+------------------+
|  COMMAND LAYER   |
+------------------+
|  SECURITY LAYER  |
+------------------+
|  UDP             |
+------------------+
```

### Command Layer

```
+--------------------+
| OP CODE (Command)  |
+--------------------+
| DATA               |
+--------------------+
| ...                |
| ...                |
| ...                |
+--------------------+
| OP CODE (Command)  |
+--------------------+
| DATA               |
+--------------------+
```

### Security Layer
* Request
```
+-----------------------------+
|  KEY ID                     |
+-----------------------------+
|  RANDOM DIVERSIFIER         |
+-----------------------------+
|  DATA HASH (ECRYPTED)       |
+-----------------------------+
|  DATA (ENCRYPTED)           |
+-----------------------------+
```
* Response
```
+-----------------------+
| ERROR CODE            |
+-----------------------+
| EXTRA DATA (optional) |
+-----------------------+
```
### Secure Data Exchange Protocol
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

###  Board Key Table
Every board and server has sqlite database with preinstalled key table of 32 bytes (256 bits) length AES keys identified by 1 byte key id.

##  Board structure
Board may have several sensor or servo chips on it. Board also may transparently retransmit the information received from other boards.

### Sensor/servo chips
* Different type of chips may be connected to board using standard protocols: I2C, SPI, 1-Wire.
* Each chip has configuration text file with protocol specific information (address, etc).
* "Driver" part of the software analyzes all found configuration files and initialzes sensors/servos.
* "Driver" scans all sensor chips and detects measured value changes, error cases and sends to collector.
* Server processes receive information from other boards and send to collector appending own path to received data path.
* Collector periodically sends collected information to upped level server using network client.
* To avoid colissions between sensor scanning and servo command processes, flock synchronization is used for each device file (/dev/i2c-0, etc.)

### Data collector
### Network agent
