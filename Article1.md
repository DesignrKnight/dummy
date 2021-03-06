# Setting Up Own Login vs Login via External Services

Before we delve into the comparative study on various methods of authenticating the users; it is necessary to answer one primary question: Why does a website, app or service need to have login flow? Is it impossible to cater to the userbase without it? With User Experience playing a pivotal role in the retention of users, the login flow needs to be seamless and intuitive. At the same time, data privacy and security cannot be compromised with. With so many factors playing a role in the login and sign-up component of the service, which might seem as a minor component initially, turns into a significant component.

## Need for Authentication
* Any solution today on the internet today consists of interactive content, like commenting on articles or booking an appointment. All these interactions do one of four primary CRUD operations on a database(Create, Read, Update and Delete). Out of these, except read-operations, should be authenticated at the minimum (You may do it otherwise if the model of service is based on full anonymity. Even in such cases, you should incorporate rate-limiting or other methods to prevent abuse). Read-operations also should be authenticated to some extent, for example, if a user orders something online, only the concerned vendor should be able to see the user's mobile number and address, whereas viewing this article does not need authentication.
* Suppose you get 10k comments on one of the posts on your service, or you get 1k orders within hours of launch on your product on an e-commerce website. These events are updating the database, which, if done without authentication, can lead to one or more of these.
  * Unable to map the comments/orders to the respective user who did it.
  * Partial or complete Denial of Service (DoS). These updates could have been done by a malicious script which in case of limited quantity products in e-commerce(and similar) services render them to not be in a state to cater to relevant users.
  
## Basic Setup for Login-Signup Flow
 Login-Signup is the first line of security for any service. If this is breached, a malicious user can access data which is otherwise not allowed. The login interface should also verify for user access level during the process- vendors, delivery boy, buyer etc. in case of e-commerce or editor, author, a viewer in case of a blog and similarly for other services. At the bare minimum, the authentication should be on username/email with the password, which can be secured even further with multi-factor authentication. To ensure privacy and protection of the users, passwords must be hashed at all stages(even before sending to the server via API). 
 
### Some pointers to keep in mind while hashing are: 
* Use hash function, not encryption, no matter how secure the encryption is. There is no feature or facility required for login and sign-up flow that is not provided by hashing methodology which encryption provides.
* Use powerful hashing methods with salt(random salt preferably; having the same salt for all passwords makes salting go in vain). Hashing algorithms like MD5 are too weak for present-day computational power, hence comparatively insecure.
* Additionally, older standards of hashing have multiple known collision pairs. A collision is an event where two distinct inputs give the same hash as output. 
* If you are using API and passing hashed password in request(hashed passwords always better than plain-text), then do not directly store this has in server. If you directly store the hash, then your entire database if compromised in multiple ways.  
  * Any internal employee with access to the database can copy hash from the database and use API to login.
  * If the database gets breached by a hacker, they no longer need to get a plain-text password to login as API can do it directly
 
 Hence, you should hash passwords at each level of transmission, multiple times if needed.

### Additional Ways to Secure
* Go Slow. In this age of high-speed internet, how can going slow be beneficial?
  * A wrong password entry should have a cool-off time before next attempt. This can be done from first attempt itself(a few hundred milliseconds) or after few attempts(maybe three of five). The cool-down time would be decided after coming to a common ground between User Experience and Security standpoints. This cool-down should be done on the server end and not client end as client-side scripts are easier to manipulate. Additionally, is done client-side, a script can have multiple instances of a client running parallelly, which will bypass client-side protection.
  * IP Rate Limiting. This should be used to protect bogus sign-ups from the same IP. Additionally, multiple users trying to log in from the same IP would also be an indication of compromised accounts.
* Have only verified accounts—all users who sign-up must be verified via phone(SMS or call) or email. Without a verified account, any user who knows a random person's email or phone(which is relatively easy to obtain) can make an account using their credentials.
* Doing Reset Password the correct way
  * The reset password link generated must be unique and single-use. Let us imagine a scenario where-in single-use is not enforced, then I can grab my friend's phone, and if I see any reset password link, I can go ahead and change the password again
  * Have a time limit within which the reset request is valid.

## Using External Service for Login
There are various external services(like Google, GitHub) that can use used for the Login and SignUp flow of the service. This mitigates the resources for security of userbase, and these are managed by these external services. These are done by the OAuth2 protocol for authorization which is an industry-standard as of today. Having an external service manage the login and sign-up comes with a lot of lucrative features, but there may be some use-cases which benefit without it. 

### Benefits of OAuth via External Service
* The sign-up user experience is enhanced as majority users just need to click and authorize to use that service to sign-up, and the need to fill the form is removed.
* Password hashing, IP rate limiting, password reset and other done out of the box with OAuth2
* Users gain more trust with your service as you are not saving their sensitive login info, but it is instead managed by a reputed external service

### Some pointers regarding External Service
* With the external service playing a pivotal role in user management, the sign-up and login flow will have some brand element of theirs and not solely your brand. 
* You may still need a sign-up form to take additional info which is not provided by the service.
* Though rare, but an outage of this external service would render the authentication on your service to fail even for legit users on your service
* During peak usage, these external services may not be able to deliver to immense load as they have rate-limiter for each service using their OAuth(Some services have a paid option to lift this rate limit)
* Do make sure you have a proper privacy policy on the service using OAuth, and this plays a role in obtaining the API-key permission to use OAuth

## Final Note
Having gained an insight into how the login and sign-up mechanism is managed for a service, I hope you are better equipped to manage user authentication of your service. To gain more detailed insight, I will put some keywords here which you can do a web search on and get further info. Kindly make sure you are reading recent articles always when making these decisions as the security standards and protocols are regularly updated
Keywords: OAuth, JWT, Hashing and Salt, SHA-2, "Adobe encryption password breach", Brute-force attack, Rainbow Tables, IP Rate Limiting
