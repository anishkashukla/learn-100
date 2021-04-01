Index | Technique
--- | ---
**1** | Note Taking
**2** | Why is it important ?
**3** | How you can do that properly ?
**4** | Tools that you can use for note keeping 
**5** | Resources 

### Note Taking
````
Note taking is very important for beginners, especially when you are still not creative, when you have not fully begun to think 
like a hacker. Making notes and creating a methodology doesn’t guarantee immediate success, but it greatly amplifies your speed 
of becoming a better researcher/hacker. 
````
### Why is it important ?
````
* Makes learning easy
* Helps you create and adhere to a methodology
````
### How you can do that properly ?
````
Lets say you want to start with attack vectors. How do you start? 
For best results, pick a vulnerability class and learn as much as possible from many different resources even if it is reading about the same stuff. Dedicate at least 2–3 days (minimum)to learn a particular attack. Read as many articles and watch as many videos as you can. 
The next step is to make notes after (or while) reading, organizing them and trying to recognize different attack patterns. Let me tell you what will happen if you will do this.

Let's start with the most simplest of attacks → trying to tamper with the “Forgot password” functionality (this of course can be classified as an attack based on functionality rather than a particular bug class. Make notes this way because it helps in real world bug hunting)

After reading many reports and articles on this, I made notes and organized them. What I got out of this was different patterns of attacks.

Leaking of password reset token in the response (immediately after requesting a password reset via “forgot password”).

Getting the password reset token to the attacker’s email address via HTTP parameter pollution.

Host header poisoning (setting the Host header to the attacker’s server)

IDOR → changing the victim email or user ID to attacker email or user ID

Password reset token not expiring

Brute forcing password reset tokens

Logic flaws in “forgot password” functionality

You see, now while testing I would have a checklist. 
This would become a part of my methodology. This is of course, 
apart from any testing that would be required for the specific 
web application tested.

You can also follow the security researchers on twitter, instagram or Youtube where they
keep on sharing some tips and tricks. That can also help you a lot.

I read this in a blog and have found this to be really simple yet useful. You can check resources for more information.
````
### Tools that you can use for note keeping 

* [OneNote](https://www.microsoft.com/en-in/microsoft-365/onenote/digital-note-taking-app?ms.url=onenotecom&rtc=1)
* [Evernote](https://evernote.com/)

### Resources 

* [BugHunterMethodology](https://www.bugcrowd.com/blog/the-importance-of-notes-session-tracking-bug-bounty-hunter-methodology/)
* [TheneedforNoteMaking](https://sankethsharath.medium.com/the-need-for-note-making-and-an-organized-methodology-in-bug-bounty-hunting-f4d23c7db4bf)

### Will keep updating the resouces so keep checking that in future as well :smile: :v: