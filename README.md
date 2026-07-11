# Password Hashing

A plain-language reference on how to store user passwords safely. It covers why plaintext, encryption, and fast hashes are all wrong for this job, what salting and peppering actually do, the three algorithms worth using (bcrypt, scrypt, argon2), how to pick work factors, and how to migrate an existing database without logging everyone out.

## The core idea

Store passwords as salted hashes from a slow, purpose-built password hashing function: argon2id, scrypt, or bcrypt. You never store the password itself and never encrypt it, because encryption is reversible and the key becomes a single point of failure. Each password gets a unique random salt so identical passwords produce different hashes, and the function is deliberately slow so that an attacker who steals the database cannot test billions of guesses per second.

## What is inside

- `01-what-not-to-do.md` plaintext, encryption, and fast hashes, and why each fails.
- `02-salting.md` what a salt is, why it is per-user, and where it lives.
- `03-algorithms-and-work-factors.md` bcrypt, scrypt, argon2, and tuning cost.
- `04-pepper-and-extras.md` pepper, constant-time compare, and length limits.
- `05-migration.md` upgrading a live user table safely.

## The stance

This is a solved problem with a short list of correct answers. Do not invent your own scheme, do not reach for SHA-256 because it is familiar, and do not encrypt. Pick argon2id (or bcrypt if that is what your platform gives you), salt every password, tune the cost, and move on.

## License

MIT. Copyright 0xelitesystem 2026.
