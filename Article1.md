# Setting Up Own Login vs Login via External Services

Before we delve into the comparitive study on various methods of authenticating the users; it is necessary to answer one primary question: Why does a website, app or service need to have login flow? Is it impossible to cater to the userbase without it? With User Experience playing pivotal role in retention of users, the login flow needs to be seamless and intuitive. At the same time, data privacy and security cannot be compromised with. With so many factors playing a role in the login and signup component of the service, which might seem as a minor component initially, turns into a major component.

## Need for Authentication
* Any solution today on the internet today consists interactive content, like commenting on articles or booking an appointment. All these interactions do one of four primary CRUD operations on a database(Create, Read, Update and Delete). Out of these, except read-operations, should be authenticated at the minimum (You may do it otherwise if the model of service is based on full anonymity. Even in such cases, you should incorporate rate-limiting or other methods to prevent abuse). Read-operations also should be authenticated to some extent, for example, if I order something online, only the concerned vendor should be able to see my mobile number and address, where as viewing this article does not need authentication.
* Suppose you get 10k comments on one of the posts on your service, or you get 1k orders within hours of launch on your product in e-commerce website. These events are updating the database, which if done without authentication can lead to one or more of these.
  * Unable to map the comments/orders to respective user who did it.
  * Partial or complete Denial of Service (DoS). These updates could have been done by a malicious script which in case of limited quantity products in e-commerce(and similar) services render them to not be in state to cater to relevant users.
  
## Basic Setup for Login-Signup Flow
 Login-Signup is the first line of security for any service. If this is breached, a malicious user can access data which is otherwise not allowed. The login interface should also verify for user access level during the process- vendors, delivery boy, buyer etc in case of e-commerce or editor,author,viewer in case of a blog and similarly for other services. At the bare minimum, the authentication should be on username/email with password, which can be secured even further with multi-factor authentication. To ensure privacy and protection, passwords must be hashed at all stages(even before sending to server via API). 
 
### Some pointer to keep in mind while hashing are: 
* Use hash function, not encryption, no matter how strong the encrpytion is. There is no feature or facility required for login and signup flow that is not provided by hashing methodology which encryption provides.
* Use powerful hashing methods with salt(random salt preferably; having same salt for all passwords makes salting go in vain). Hashing algorithms like MD5 are too weak for present day computational power, hence comparatively insecure.
* Additionally, older standards of hashing have multiple known collision pairs. A collision is an event where two distinct inputs give same hash as output. 
* If you are using API and passing hashed password in request(hashed passwords always better than plain-text), then do not directly store this has in server. If you directly store the hash, then your entire database if compromised in multiple ways.  
  * Any internal employee with access to database can copy hash from database and use API to login.
  * If database gets breached by hacker, they no longer need to get plain-text password to login as API can do it directly
 
 Hence, you should hash passwords at each levels of transmission, multiple times if needed.

### Additional Ways to Secure
* Go Slow. In this age of high-speed internet, how can going slow be beneficial.
  * A wrong password entry should have a cool-off time before next attempt. This can be done from first attempt itself(few hundred millisecond) or after few attempts(maybe three of five). The cool-down time would be decided after coming to a common ground between User Experience and Security standpoints. This cool-down should be done on server end and not client end as client side scripts are easier to manipulate. Additionally, is done client side, a script can have multiple instances of client running parallely which will bypass client-side protection.
  * IP Rate Limiting. This should be used to protect bogus signups from same IP. Additionally, multiple users trying to login from same IP would also be an indication of compromised accounts.
* Have only verified accounts. All users who sign-up must be verified via phone(SMS or call) or email. Without a verified account, any user who knows a random person's email or phone(which is fairly easy to obtain) can make account using their credentials.
* Doing Reset Password the correct way
  * The reset password link generated must be unique and single use. Let us imagine a scenario where-in single-use is not enforced, then I can grab my friend's phone and if I see any reset password link, I can go ahead and change password again
  * Have a time limit within which the reset request is valid.
