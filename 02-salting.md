# Salting

A salt is a unique random value generated per password and stored alongside the hash, mixed in before hashing so that identical passwords produce different hashes. Its job is to defeat precomputation: an attacker cannot use a prebuilt table of hashes, and cannot crack many accounts at once by testing one guess against the whole database.

## Why per-user and random

If two users both choose `password123` and you hash without salt, their stored hashes are identical, which leaks that fact and lets one cracked hash reveal both. With a unique salt each, the two hashes differ completely. The salt must be random (from a cryptographically secure source) and long enough to be unique in practice; 16 bytes is a standard choice. A shared or predictable salt loses most of the benefit.

## Defeating rainbow tables

A rainbow table is a giant precomputed map from hashes back to inputs. It only works when the hash of a given password is always the same. A unique per-user salt makes every stored hash effectively one-of-a-kind, so no general table can exist for it, and an attacker is forced to attack each password separately. This is the specific thing salting solves.

## Where the salt lives

The salt is not a secret and does not need to be hidden; it is stored right next to the hash, typically in the same column. Modern password hashing functions handle this for you: the output string bundles the algorithm, parameters, salt, and hash together. A bcrypt hash looks like this:

```
$2b$12$R9h/cIPz0gi.URNNX3kh2OPST9/PgBkqquzi.Ss7KIUgO2t0jWMUW
```

Here `$2b$` is the algorithm, `12` is the cost, the next 22 characters are the salt, and the rest is the hash. You store that one string; on login you feed it and the entered password back to the verify function, which reads the salt and cost from the string itself. You do not manage the salt by hand.
