# Pepper and other extras

Beyond salting and a slow algorithm, a few extra measures harden password storage: a pepper, a constant-time comparison, sensible length limits, and never truncating silently. These are refinements on top of the fundamentals, not replacements for them.

## Pepper

A pepper is a single secret value added to every password before hashing, but unlike a salt it is the same for all users and is kept outside the database, in application config, an environment variable, or a hardware security module. The point is that if an attacker steals only the database (a very common scenario, for example a SQL injection dump), they still lack the pepper and cannot verify guesses at all. A pepper complements per-user salts; it does not replace them. One practical approach is to apply the pepper with an HMAC using a secret key, then hash with argon2 or bcrypt.

## Constant-time comparison

When verifying, compare the computed hash to the stored one using a constant-time comparison function, not an ordinary `==`. A naive comparison can return early on the first differing byte, and the tiny timing difference can leak information over many attempts. Every good password library provides a constant-time verify function; use it rather than comparing strings yourself.

## Length limits and encoding

Set a generous maximum password length (for example 128 characters) so an attacker cannot send a megabyte-long password to make your slow hash exhaust CPU, a denial-of-service risk. Enforce a sane minimum length too. Remember bcrypt only reads the first 72 bytes; if you support longer passphrases, pre-hash the password (for instance with SHA-256) before bcrypt, or use argon2 which has no such limit. Handle Unicode consistently by normalizing input so the same typed password always hashes the same way.
