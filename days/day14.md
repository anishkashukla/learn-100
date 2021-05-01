### Authentication Bypass 
````
Authentication bypass vulnerability could allow attackers to perform various malicious operations by bypassing 
the device authentication mechanism.
````
### What's the issue 
````
Authentication bypass exploit is mainly due to a weak authentication mechanism.

Organizations failing to enforce strong access policy and authentication controls could allow an attacker to bypass authentication.

Many default applications and servers come with unsecured default folders.
Administrators fail to secure folders and servers with strong password protection.
Device users fail to reset the default passwords.
Sometimes, a protected application will include unprotected files. 
For instance, the application’s main folder will be secure, but other folders will be open without any protection.
Likewise, protected sites might include folders that lack authentication.
Most developers fail to test their systems prior to release thus leaving data open to attack.
Attackers look for unprotected files, gains access to those unsecured files, gathers information and then attempt to attack protected applications by bypassing the authentication system.
````
### Some good resources
 
 * [Medium Blog](https://medium.com/infosec/how-i-was-able-to-uniquely-bypass-authentication-while-web-pentesting-cd5d8d6a2837)
 * [Medium Blog](https://medium.com/cyberverse/authentication-bypass-with-x-path-injection-and-sql-injection-cyberverse-c5d8dd34ac9a)