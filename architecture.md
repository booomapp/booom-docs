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
   
## Authentication 

`Identity` - is a unique `code` linked to the private key (and public key of course) and the multiple authentication methods.

1. `CA` provide the common ways to auth the users. For example, it can provide the mobile number authentication, then the flow can be: user inputs the mobile number, `CA` checks the uniquness of number, then sends the SMS, user inputs the right code from SMS, after all `CA` creates the `Identity` for this mobile number including the pair of keys, `code`. In additonal `CA` adds to the client response the two special field: regTimestamp and regSign. `RegSign` is the crypto signatures signed by the `CA` and body for the sign includes: `code` and `regTimestamp`.
2. `Client` should store the private key in the own local storage. It should be used to make the all transactions by `blockchain` and for the authorization in `Index`.
3. The next step is to register the `Identity` in `blockchain`. To do this `Client` should call the `register` method of `blockchain` signed by the identity private key with arguments: `code`, `regTimestamp`, `regSign` and the additional user fields like `firstName`, `lastName`. `Blockchain` should check the uniqueness of the additional fields by ownself if it's requires.
4. `Blockchain` has the knowledge about the `CA` public key so it can check that `regSign` was signed by the `CA` and the `regTimestamp` is not expired.
5. `Blockchain` linked the `address` (public key) of private key to the `code` and store this info with the additional user fields.
6. Every transaction in `blockchain` should emit the event so `Index` server will fetch `Register` event as soon as possible. `Register` event should contain: `code`, `address` and the user fields.
7. When `client` do any transaction by `blockchain` it get the transaction hash. `Index` server provite the way to check if transaction was processed or not. So `client` can implement some `loader` waiting for the transaction process finishing by `index`.
8. After processing the `Register` event `Index` server creates the record about the user that was already stored in `blockchain`. The record will contain `code` and `address`.
9. For using `index` API the `client` should be authenticated by `index`. The way is: the first step is getting the temporary random message from the `index` linked to `address`, the second step is the signing this message by private key on the client and the last step is the verifying by `index` that message was signed by the private key linked to the `address`. After all `index` server gives to client the JWT token that can be stored to `cookies` or local storage.
