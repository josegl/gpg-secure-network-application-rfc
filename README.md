# Gpg based communications for network applications RFC
This document is under construction
## Table of contents.
<ul style="list-style-type:none">
  <li>1. Introduction.</li>
  <ul style="list-style-type:none">
    <li>1.1. Prelude.</li>
    <li>1.2. The problem.</li>
    <li>1.3. A brief of the proposed solution.<li/>
  </ul>
  <li>2. Definitions.</li>
  <ul style="list-style-type:none">
    <li>2.1. Core system parts</li>
    <ul style="list-style-type:none">
      <li>2.1.1. Auth server.</li>
      <li>2.1.2. Database.</li>
      <li>2.1.3. API.</li>
      <li>2.1.4. Hand shake.</li>
      <li>2.1.5. Job request.</li>
      <li>2.1.6. Job.</li>
      <li>2.1.7. Job result.</li> <li>2.1.8. Administrators.</li>
      <li>2.1.9. Developers.</li>
    </ul>
    <li>2.2. Accounts.</li>
    <ul style="list-style-type:none">
      <li>2.2.1. Developers accounts.</li>
      <li>2.2.2. Resources accounts.</li>
      <li>2.2.3. Resources administrator accounts.</li>
      <li>2.2.4. Resources creators and cosumers accounts. (A.K.A. users)</li>
    </ul>
    <li>2.3. Applications.</li>
    <ul style="list-style-type:none">
      <li>2.3.1. Developers accounts.</li>
      <li>2.3.1. Authorized applications.</li>
      <li>2.3.2. Identified instances of the authorized applications.</li>
    </ul>
  </ul>
  <li>3. Specification.</li>
</ul>

## <a name="2"/>2. Definitions.
This section will cover the definition for all the involved parts, so,
in the upcoming sections where the protocol is explained in very deep
detail, everything will be clear.

### <a name="2.1"/>2.1 Core system parts.
In this section we are defining the different parts that any system
that wants to be implemented using this kind of protocol, **must**
include.

### <a name="2.1.1"/>2.1.1. Auth Server.

The auth server is a service whose goal is to guarantee the IAAA.
IAAA stands for Identification, Authentication, Authorization and
Accountability.

Identification: The process used by an application or an user behind an
application to announce who they are, to access the system.

Authentication: The process used by an application or an user behind an
application to prove that they are who they say to be.
Usually this process is done by a sharing a secret that only know
the Auth Server and the application or the user behind the application.
This secret must be protected in order to avoid the usage by a third
party application or user behind the same application, or behind a
third party application.

Authorization: Once an application or an user behind an application is
identified and authenticated, may make requests in order to perform
operations over the data. The auth server will check if the requested
operation over the targeted resources is allowed for the user over the
application from where the request was made.

Accountability: Is to log everything an user, an application or an user
behind application did over what. The Auth server provides this
mechanism by saving every request, with all the data related to it:
the user who requested it, the application from the request was made,
a timestamp, the resource targeted, the operation requested, and the
results of the checks.

### <a name="2.1.2"/>2.1.2. Database.

The database is where all the data lives. It may be running in a single
server, a cluster, or distributed all around the globe. The access to
it must very very exclusive and only very limited users or applications
must have access to it.

### <a name="2.1.3"/>2.1.3. API.

The API **must** be the only application that can have access to the
database, it will expose only very few end points to applications. The
specification will be described in its own section. A brief description
could be that the API receibes Jobs that have to be done, resolves the
Jobs, keep the Jobs results for a little amount of time, and serves
only once the Job result to the identified, authenticated and
authorized user who made the initial request behind an identified,
authenticated and authorized application instance, and the Job result
request is made by the same user from the same application instance
from the initial Job request was made to the Auth Server, and the Job
already can be retrieved because it hasn't expired yet.

### <a name="2.1.4"/>2.1.4. Hand Shake.

The Hand Sake is the process used by an application, an application
instance, and a user behind an application instance anounces itself to
the system, and exchange some secret encrypted data in order to
identify and authenticate the first time.

### <a name="2.1.5"/>2.1.5. Job Request.

A Job Request is a request made by an identified and authenticated user
behind an identified and authorized application instance to perform a 
CRUD operation. In the request a description of the CRUD operation is
sent to the Auth server. The specification of this will be descripted
in its own section of this document.

### <a name="2.1.6"/>2.1.6. Job.

A Job is a resource generated from the Job Request defined in the
section above. So, for the Job generation the Job Request must be have
accepted by the Auth Server. The Job has it's own identifier, the
CRUD operation to be performed, and extra data, like the user
signature, the application instance signature, and a timestamp.
All the Jobs generated are sent to the API, who will resolve the Job
and will keep that result for a little amount of time. All the Jobs
are stored in order to keep track of the Accountability. The
specification will be described in its own section

### <a name="2.1.7"/>2.1.7. Job Result.

A Job Result is the data retrieved from the database after perform the
CRUD operation described in the 











I've been thinking for a while about this:

A secure way to authenticate an user. This user will perform CRUD operations over protected resources, so we must know that the user is who he/she pretends to be.

Once the user is authenticated, as I said before, he/she will perform CRUD operations over the data, so first of all, before to perform the operation, we must ensure that the user has permissions enough to perform the requested operation.

No matter if the operation is allowed or not by the user permission schema, all the user activity over the data is logged.

This is something that is done everyday, but I would like to add, how to do it in a completely insecure network.

Note that all what is going to be described below are just drafts and ideas which I have no idea if are good or not, and that's why I want to share them, in order to know if it is a good approach.

**1. The application authentication.**

**1.1. Allowed applications**
The allowed applications must be registered in the system manually, when you register an application, an allowed-application gpg key pair is generated. The application must be shipped with its associated private key and the Auth server's public key.

This is only to identify an allowed application, but not to identify an instance on the application. This is critical an attacker with the same application could kidnap the communications.

**1.2 The instance identification.**
1.2.1. This shipped private key, can be used only to identify the application itself, in order to identify the application instance, when the application is launched for first time, it will send a "hello" message to the Auth server.

1.2.2. This "hello" message contains a random generated code in the client instance that is going to be used to identify the application instance. The message is signed by the application secret key and encrypted with Auth server's public key.

1.2.3. The Auth server will receive the previous "hello" message and will check its signature to see if it is a registered application. If it is, then it will decrypt the message obtaining the code and saving it for a very small amount of time. After this, the Auth server will respond to the client with just an "I've received the code" message with no further information. This message is signed with the Auth server's private key

1.2.4. When the client receives the confirmation from the Auth server, it will check the signature, and if it is O.k. it generates its own gpg key pair. **This is the pair that will identify the allowed client application instance.**
Once the key pair is generated, a new message is sent to the Auth server. This new message contains the previously random generated code which is the one used as allowed client instance identifier, and the new public key generated.
This message is signed with the shipped client private key, and encrypted with the Auth server's public key.

1.2.5. When the Auth server receives this message checks the signature, if the signature is O.K., then is decrypts the message, and checks the instance identifier, and if it has not expired yet. If every check is valid, then the public key is saved in the system and the identifier is deleted, sending a "I've received your public key" message to the client, which is signed by Auth server's private key, and encrypted with the just received public key.

1.2.6. The client receives the message, checks the signature, and  if the signature is valid (it can trust that is the genuine Auth server is who is responding even though you are in a M.I.T.M.) and can decrypt the message, then Application instance can be used to register new users, or perform CRUD without fear of another allowed client can kidnap the requests.

**2. User authentication**
The users can only be authenticated from an identified instance of an allowed application.

**2.1 The user registration**
When the user is not registered in the system it must register in it. In order to do it, there 2 options:

**2.1.1. Manual registration by an administrator.**
An already in the system administrator registers the user. The registration data is just a random ID that is generated automatically. Then, a random one time use code is generated and saved associated with the user ID just created. This code is saved along with a time stamp, because it will be valid just for a very limited amount of time. This code must be communicated to he new user. No matter the chosen method. Email, sms, 2 factor authentication application... But a 2 factor authentication application with the OTP standard and so on would be desired.

**2.1.2. Automatic registration by the user.**
The user requests to register in the system from an authorized application instance. In order to do it, the user follows this steps:

2.1.2.1. The user selects one method to receive a code. It could be a user's email, sms, or via a One Time Password application or even others I haven't think yet.
When the user selects the method, a signed with application instance secret key, and encrypted with the Auth server's public key request is sent to the Auth server. This request has:
+ The user selected method
+ The payload (the user email, phone, OTP data to generate it).
+ A randomly generated code to identify the request.
+ A timestamp.

2.1.2.2. The Auth server receives the request, checks the signature, and if it is good, decrypts the message, and generates the One time use code which is associated with the request identification code. Also the timestamp could be checked in order to invalidate requests that for any reason has took too long to arrive.
The code generated for the given request is stored along the following metadata:
+ The code itself.
+ The request ID.
+ Time stamp.
+ The authorized application instance signature ID.

This data is stored in a temporary way.

The generated code is sent as plain text, no encryption or signed messages are required here, although it would be desirable if the chosen method is not the TOTP method, which is a special case (Timer One Time Password) which MUST implement the [RFC-6238][1]
, and whose flow won't be detailed in this document.

2.1.2.3. The user has received the code or is able to generate a TOTP with a TOTP compliant application.
So the user has a valid code, no matter the method selected, so he/she must go back to the application, and write down the code in the form prompted in the application.
Then the code is sent in a request which has the following data:
+ The code itself
+ The request ID
+ Time stamp

This message is encrypted with the Auth server's public key and signed with the authorized application instance private key, and then sent to the Auth server.

2.1.2.4. The Auth server receives the signed and encrypted message from the previous section 2.1.2.3.Checks the signature, if its good then it decrypts the message to extract the code and the request ID and checks if there is a request with such ID, if exists, the timestamp is checked, if the request has not expired, then the signatures are checked. If they are the same, then the codes are checked, and this last check is passed then a new user can be created, but it is not created at this step.
So, the Auth server sends back a response which will something like this:
+ A code, or a message saying: Ok, you are who you said you are.
+ A time stamp
+ The code generated
+ The request id.

This message is sent encrypted with the authorized application instance public key and signed with the Auth server's private key.

2.1.2.5. The authorized application instance receives the Auth server's response, and makes all the checks: It checks the signature, decrypts the message and makes the checks for the code, the request and the timestamp.
Any application MUST do these checks in order to guarantee that the response comes from the legitimated server, and that hasn't been a significant delay in the communication.

When all the checks have passed, then a new gpg key pair is generated, in this case for the just authenticated user. In this case, the new private key MUST be protected by a pass phrase that the user MUST remember somehow. The application MUST NOT store the password or any data that could be used to restore the password.

The application MAY integrate some mechanism in order to manage the users gpg keys, from where the private key passwords can be reset by the key owner, or update some data like the user's e-mail, phone or other personal data which helps to identify the person behind the user.

When the new gpg key pair has ben generated for the current user, then a new message with this data is generated:
<ul>
<li>The request code generated in 2.1.2.3</li>
<li>A timestamp</li>
<li>The new gpg public key</li>
</ul>

This message is encrypted with the Auth server's public key and signed with the allowed application instance private key, and sent to The Auth server.

<h3>2.1.2.6. The Auth server receives the new user's public key</h3>
When the Auth server receives the message from 2.1.2.5, it MUST check the signature, decrypt the message, check the timestamp and the request ID. 
If all the checks pass, then 


  [1]: https://tools.ietf.org/html/rfc6238