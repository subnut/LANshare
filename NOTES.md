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
