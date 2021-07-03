### SMTP Header Injection 
````
SMTP header injection vulnerabilities arise when user input is placed into email headers without adequate sanitization, allowing an attacker to inject additional headers with arbitrary values. 
This behavior can be exploited to send copies of emails to third parties, attach viruses, deliver phishing attacks, and often alter the content of emails. 
It is typically exploited by spammers looking to leverage the vulnerable company's reputation to add legitimacy to their emails.

This issue is particularly serious if the email contains sensitive information not intended for the attacker, such as a password reset token.
````
### Remediation
````
Validate that user input conforms to a whitelist of safe characters before placing it into email headers. 
In particular, input containing newlines and carriage returns should be rejected. Alternatively, consider switching to an email library that automatically prevents such attacks.
````