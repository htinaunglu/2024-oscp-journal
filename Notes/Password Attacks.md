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