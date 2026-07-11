# What never to do

Never store passwords as plaintext, never encrypt them, and never hash them with a fast general-purpose hash like MD5, SHA-1, or SHA-256. Each of these is a well-known mistake with a well-known catastrophic outcome when the database leaks, and databases do leak.

## Plaintext

Storing the raw password means that anyone who reads the database, a leak, a rogue employee, a backup left in a bucket, instantly has every user's password. Because people reuse passwords, you have also handed over their email, bank, and everything else. There is no scenario where this is acceptable. If you can email a user their existing password, your storage is broken.

## Encryption

Encryption is reversible by design, and that is exactly the problem. To check a login you would decrypt, which means the key must live on the server. Anyone who steals the database and the key (they are often stored together, or the app server holds both) recovers every password in cleartext. You do not need to reverse a password to verify it, so reversibility buys you nothing and adds a single catastrophic point of failure. Hashing, which is one-way, is the right tool.

## Fast hashes

MD5, SHA-1, SHA-256, and SHA-3 are cryptographic hashes, but they are built to be fast, and speed is the enemy here. A modern GPU computes billions of SHA-256 hashes per second, so an attacker with your leaked hashes runs a dictionary and brute-force attack and cracks weak and medium passwords in minutes to hours. Salting a fast hash helps against precomputed rainbow tables but does nothing about raw guessing speed. The whole point of a password hash is to be deliberately slow, which is what the algorithms in file 03 provide. Using SHA-256 for passwords is the most common serious mistake in this area.
