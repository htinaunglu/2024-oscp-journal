While there are many modern approaches to user account and service authentication (such as _biometric authentication_ or _Public Key Infrastructure_), simple password authentication remains the most dominant and basic approach.

## Brute-force attacks
- attacks attempt every possible password variation, working systematically through every combination of letters, digits and special characters
- 101% success rate (unless they change the password during your cracking 😆), can theoretically bypass any ill-protected password-based authentication system
- time consuming, based on the length of the password
## Dictionary attacks
- used predefined list of possible most-used passwords (word lists)
- may fail if the actual password is not in the list.
- time is significantly faster than brute-force attacks ( based on the size of wordlist)

As with any dictionary attack, this generates a lot of noise and many events. If installed, a _Web Application Firewall_ (WAF) would block this activity quickly. Other brute force protection applications could also block this, such as _fail2ban_, which locks a user out after a set number of failed login attempts. However, web services aren't often afforded this type of protection, making this is a highly effective vector against those targets.

## Some Points
- It's worth noting that the format of the username also suggests that the company may use the first name of the user for account names. This information may assist us in later information gathering attempts.

## Cryptography, Encryption, and Password Cracking

**Encryption:**
- Encryption is a two-way function involving scrambling (encryption) and unscrambling (decryption) data with at least one key.
- Symmetric encryption uses the same key for both encryption and decryption, posing a security risk if intercepted during key exchange.
- Advanced Encryption Standard (AES) is an example of a symmetric encryption algorithm.
- Asymmetric encryption uses distinct key pairs (private and public keys) for encryption and decryption.
- Rivest–Shamir–Adleman (RSA) is a common asymmetric encryption algorithm.

**Hashing:**
- Hashing involves running variable-sized input data through a hash algorithm, resulting in a fixed-length hexadecimal value.
- Cryptographic hash functions, like MD5 and SHA1, are commonly used in information security.
- Hashing is utilized to secure passwords; user passwords are hashed and stored in databases.
- Hashing is a one-way function, making it challenging to retrieve plaintext from the hash.
- Password cracking involves decrypting encrypted passwords or determining plaintext from hashed passwords.

**Use in Information Security:**
- Hashing is employed to secure passwords in databases, ensuring plaintext passwords are not accessible.
- During a login attempt, the entered password is hashed and compared to the stored hashed value in the database.
- Password cracking attacks are performed to decrypt encrypted passwords or determine plaintext from hashed passwords.
- Password cracking is often executed on dedicated systems in parallel with other activities during penetration tests.

## Cracking methodology
```shell
1. Extract hashes
2. Format hashes
3. Calculate the cracking time
4. Prepare wordlist
5. Attack the hash
```
The process of cracking a hash involves several key steps:

1. **Hash Extraction:** Obtain hashes from various sources, such as a database system during a penetration test.
2. **Hash Formatting:** Identify the hashing algorithm and format the hashes for the cracking tool. Use tools like hash-identifier or hash-id on Kali to determine hash types.
3. **Cracking Time Calculation:** Evaluate the feasibility of the cracking attempt by considering the key-space, hash rate, and expected lifetime. Adjust the approach if the cracking time is too long.
4. **Wordlist Preparation:** Modify the wordlist and employ a rule-based attack, considering password policies, online leak sites, and other password vectors for a comprehensive approach.
5. **Hash Attack:** Start the cracking process with careful consideration of hash type and accuracy in copying and pasting hashes to avoid errors that could compromise efforts.

	Maintaining focus and structure throughout the process is crucial for improving results in this potentially lengthy endeavor, and upcoming demonstrations will reinforce key aspects of the methodology.