# Algorithms and work factors

Use a slow, purpose-built password hashing function: argon2id is the current first choice, scrypt is a strong alternative, and bcrypt remains acceptable and widely available. All three are deliberately expensive to compute, and all take a tunable cost parameter (the work factor) that you raise as hardware gets faster.

## The three to use

Bcrypt has been trusted for decades, is available everywhere, and takes a cost factor. Its main limitation is that it only uses the first 72 bytes of the password and its memory use is fixed and low. Scrypt adds a memory cost, making it harder to attack with custom hardware that has lots of compute but limited memory. Argon2 won the Password Hashing Competition and is the modern recommendation; use the `argon2id` variant, which resists both GPU and side-channel attacks. An example argon2 setup uses parameters like memory 64 MB, iterations 3, and parallelism 1, then tunes upward.

## What the work factor does

The work factor sets how much CPU (and, for scrypt and argon2, memory) each hash costs. Higher cost means slower hashing, which barely affects one honest login but multiplies an attacker's cost across billions of guesses. For bcrypt this is the cost value (each step doubles the work): cost 12 means 2^12 rounds. For argon2 it is the memory, iteration, and parallelism trio.

## Tuning the cost

Pick the highest cost your server can afford while keeping a single login verification around 200 to 500 milliseconds under real load. That is slow enough to cripple mass cracking but fast enough that users do not notice. Measure on your actual hardware rather than copying a number, because the right value depends on your CPU and traffic. Revisit it periodically: as machines get faster, raise the cost so your stored hashes keep pace. Because the cost is stored inside each hash string, old hashes still verify at their old cost while new logins use the new one.
