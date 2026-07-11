# Migration

You can move an existing user table to a stronger password scheme without knowing anyone's password and without forcing a mass reset. The trick is to upgrade each hash the next time that user logs in, and optionally to wrap old hashes so they are safe in the meantime.

## Upgrade on login

You never have the plaintext passwords, so you cannot re-hash them in bulk directly. Instead, at login you already receive the plaintext the user typed. Verify it against the old stored hash as usual; if it matches, immediately re-hash that plaintext with the new algorithm and overwrite the stored value. Store an algorithm marker (the modern hash strings include one) so your verify code knows which scheme each row uses. Over time, active users migrate themselves transparently, and no one is logged out.

## Wrapping old hashes now

Upgrade-on-login leaves old weak hashes sitting in the database until each user returns, which is risky if the old scheme was a fast hash. You can harden every row immediately by wrapping: run each existing hash through the new strong function, storing something like `argon2(existing_sha256_hash)`. Record that the row is wrapped. At login you apply the same transformation to verify, and once verified you can replace it with a clean argon2 hash of the plaintext. This protects the whole table at once instead of waiting.

## Practical steps

Add a column or use the self-describing hash string to record which scheme each row uses. Deploy verify code that recognizes both old and new formats. Choose whether to wrap immediately (stronger, slightly more code) or only upgrade on login (simpler, slower rollout). Monitor how many rows still use the old format and, after enough time, consider forcing a reset for the stragglers. Test the dual-path verify carefully before shipping, because a bug here locks users out.
