# Booom architecture

The main goal in developing the architecture of Booom was to utilize blockchain (including public) as the primary database ("source of truth").

The public and private blockchains have a many restrictions such as a speed, a limited access to the real world data and so on.

The high level of Booom architecture has a three parts:

1. Certificate Authority (CA)
2. Blockchain
3. Index Server (Index)

### CA
CA server is used to provide user authentication with common ways (email, phone, social, crypto wallet).
It also create the user blockchain identity (pair of keys, public and private).
Every identity has unique `code` to use through the whole Booom system.

### Blockchain

As a blockchain it can be any public or private technology that supports to make transaction with private key, store the date, execute a random programming code and to provide the way for subscribing to transactions logs by external listener.

These requirements are enough so an ordinary backend (for example Python + MongoDB) can serve as a `blockchain` if it provides the appropriate interface.

### Index

It is used only for reading data by Booom client (like browser app or mobile app). Index subscribes to the blockchain transaction logs and provide the usable API for the clients.


