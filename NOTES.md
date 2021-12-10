# Encryption
The sender may want to encrypt a file using a password before sharing it. In
that case, the password shall first be hashed, and then the hash shall be hashed
again. The file shall be encrypted by using the hash as the encryption key, and
the double-hash shall be sent to the receiver along with the encrypted file.

The double-hash shell be sent to the receiver to check whether the password
entered by the receiver (to decrypt the file) is correct (by hashing the entered
password twice and checking if the resulting double-hash matches the one sent by
the sender).

If the receiver enters the correct password (ie. double-hashes have matched),
then the receiver side software can proceed to decrypt the file using the hash
of the entered password (not the double-hash).

Should prevent MitM attackers from accessing the data.

Also, **DON'T FORGET to `bzero()` the buffer containing the password once you're
done with it**

# Encrypted connection
Instead of encrypting using a new password each time something is sent, users
may want to set a permanent password. If so, we can use something similar to the
above.

First off, when the app is opened for the first time, create a UID. eg.
something like `<Device name>-<UNIX timestamp>`. Now, when the user wants to set
a permanent password for a device, the _hash_ of the password shall be saved in
the database for that UID. Everytime something is sent/recevied, the data shall
be encrypted/decrypted using that hash.

Everytime, before sending a file, we shall verify if the device we are
connecting to is the correct device. We shall send the first few characters of
the double-hash to it, and it should reply with the same number of characters of
the double-hash, but from the end. That should verify that it knows the hash.

ie. if the double-hash is `ABCDEFGHIJKLMNOPQRSTUVWXYZ`  
if we send `ABCDE`, we should get `VWXYZ` back.

# Architecture
- First, decide on a port to UDP on. (How about 5050 ?)
- Then, choose between the following options

## Option 1
### Procedure
- A receiver (daemon?) shall first listen on the UDP port if someone is
  broadcasting themselves to be a server.
- If yes, then connect to them (over TCP) and tell them that you are available
  to receive.
- If no, then become the authoritative server, and start broadcasting on the UDP
  port that you are the server (probably loop every 1sec?). Any new receivers
  shall connect to you (over TCP) to register themselves as available to
  receive. You shall ping them (in accordance to the specified protocol) every
  3 seconds, and they must pong back within 2 seconds. If they don't, then
  consider them unavailable to receive.
- A sender shall query the authoritative server (over TCP) to fetch the list of
  currently available receivers. The authoritative server will send it.
- The sender shall connect to the intended receiver over TCP.
### Drawbacks
- When the currently authoritative server goes down, which of the remaining
  receivers should act as the authoritative server? If they all decide to become
  the authoritative server, then it would obviously cause a big problem.

## Option 2
### Procedure
- A sender shall broadcast over UDP on the specified port that it wants to send
  some data, and shall wait for the available receivers to respond by connecting
  to it over TCP and saying 'I am ready' (or something like that :P).
- Since we are broadcasting over UDP, so there is no guarantee that all of the
  available receivers have heard our message. That means, we should keep
  broadcasting repeatedly (2 second loop?)
### Drawbacks
- Dunno yet
