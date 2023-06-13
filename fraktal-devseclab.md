# Fraktal DevSecLab

Practical hands down approach to "OWASP Top-10" and Web security basics.

Shows what is wrong, how vulnerabilities can be exploited.
Hands down approach with embedded "browser" window of the application.
Suggestions of tools to use for testing (exploiting) like fuzzing.
Suggestions what you should do. Threat modeling, 
Do and Don't


Goop
gitleaks
fuzz
git filter-rep
sops

## Web Security Basics

In this course you’ll learn about:

- The most common vulnerabilities in web applications
- How to spot vulnerabilities in code review
- How to validate your findings with proof-of-concept exploits
- Secure coding practices that keep your systems safe

The course is split into seven independent topics. You can work through the unlocked topics at your own pace, in any order. Either start at the beginning and work your way to the end, or jump straight into a topic you find especially interesting. More topics will be unlocked as the course progresses.

### Cross-Site Scripting (XSS)

Hijacking a victim's browser by embedding JavaScript fragments in an application's inputs

Cross-Site Scripting (XSS) is a vulnerability that allows the attacker to inject their own JavaScript code into a vulnerable website. This gives the attacker full control of any browser tab that loads the exploit.

```
%3Cscript%3Ealert`1`%3B%3C%2Fscript%3E
<script src="alert`1`;"></script>
```

Dos and Don’ts

The following rules will help you avoid both reflected and stored XSS in the code you write. Breaking these rules does not make the code automatically vulnerable to XSS, but the more rules a piece of code breaks, the greater the chance of it being vulnerable.
Do

    Be alert when dealing with untrusted data
    Whenever you include untrusted data on a web page, make sure that you use safe methods to do so and/or sanitize the data appropriately.

    Be mindful of context
    The correct way to sanitize untrusted input always depends on the context. The string javascript:alert(1) is safe as a part of an HTML element’s text, but dangerous when treated as a URL.

    The OWASP XSS Prevention Cheat Sheet https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html is not very light reading, but it thoroughly covers the sanitization methods that should be used in different contexts.

    Know your tools and their limitations
    Whether you’re working with HTML on the server side or the DOM on the client-side, every rendering library and framework has features that are dangerous when used with untrusted data. You need to know what they are so you don’t misuse them.

    Validate on edge, convert to restrictive types
    Validate all incoming data as soon as it is received, and convert it to the most restrictive data type that can accommodate the valid data. There’s plenty of ways to inject an XSS payload into a string, but good luck doing the same with a boolean.

Don’t

    Do not rely on denylists 


There is an infinite amount of possible XSS exploits, and it’s impossible to catch all of them by comparing them to a list of “known bad” patterns.

swisskyrepo/PayloadsAllTheThings https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#filter-bypass-and-exotic-payloads

provides a nice overview of the variety of ways in which denylists can be bypassed.

Do not think of any data as “safe”
As discussed above, safety is contextual, and there is no such thing as data that is universally safe. Data that has been validated or sanitized for a particular context may be dangerous in another.


### SQL Injection

Accessing an application's database by embedding SQL fragments in its input

SQL injection (SQLi) is a vulnerability that allows the attacker to make arbitrary queries to an an application’s SQL database. Depending on circumstances, the attacker may be able to bypass access control, read data from the database, tamper with the records, or even gain remote code execution on the database server.

Dos and Don’ts

The following rules will help you avoid SQL injections in the code you write and spot them in the code you review.
Do

    Use parametrized queries
    Parametrized queries should be your primary way of referring to untrusted data in your SQL queries. Other methods should only be considered in the relatively rare cases where parametrized queries can’t be used.

    Parse and discard
    When handling untrusted data that represents one choice out of a fixed set of options, parse it to an enum type as soon as possible and discard the original value, which may be malicious.

    Build SQL queries out of constant fragments
    If you must build SQL queries dynamically, only use untrusted data to make a choice between known-safe, constant SQL fragments.

Consider

    Consider using an SQL query builder or an ORM
    If your application needs to build a lot of queries dynamically, consider using a library to make this easier and safer. Don’t blindly rely on the library to keep you safe, though. You need to understand how it inserts its inputs into the queries it generates.

Don’t

    Do not add untrusted data to your SQL queries
    Keep data and query structure separate with the approaches described in this course.

    Do not attempt to clean up untrusted data
    If you try to remove unsafe characters or keywords to make the input safe to include in an SQL query, you are almost guaranteed to miss something. Instead, use approaches that eliminate any chance of SQLi.

    Do not pass around SQL query fragments
    The wider you spread the code that can affect the resulting SQL query string, the harder it is to ensure that no untrusted data will end up in the query. Build your queries just before executing them to make it obvious that they only contain known-safe, constant SQL fragments.

    Do not ignore any instance of SQLi, no matter how harmless it seems
    Every SQLi vulnerability gives the attacker at least read access with all of the database user’s privileges.


### Broken Authentication

Weak or missing user identity checks on sensitive data or functionality

Broken authentication is the common name for all vulnerabilities and design errors that make it possible for attackers to impersonate other users. Due to the breadth of the topic and the wide variety of ways in which authentication can fail, it’s impossible to make an exhaustive list of all possible flaws.

- Bypass poorly implemented 2FA
- Suggestion of secure algorithms
- Break weak hashes, e.g. "Use the following password hashes dumped from the database to log into user account"
- plenty of other ways in which authentication can fail

Dos and Don’ts

Following these rules will help you keep the users of your application safe.
Do

    Outsource authentication if possible
    As you’ve seen, authentication is a complex topic with a wide range of potential threats to consider. If possible, it’s best to outsource it to experts with the knowledge and resources to handle it safely and efficiently.

    Use libraries that are simple, popular, and well-regarded
    As a special case of outsourcing, when you do need to write code that deals with authentication, session management, or encryption, do it using libraries that are popular (and thus audited and battle-tested), considered safe by security professionals, easy to use safely, and hard to misuse.

    Reject weak passwords
    Reject passwords that either the Pwned Passwords 

API or zxcvbn https://github.com/dropbox/zxcvbn

consider leaked or easily guessable.

Use threat modeling to discover relevant threats
Use regular threat modeling sessions to discover the threats that are relevant to your application. You can find a simple and concise guide to threat modeling https://martinfowler.com/articles/agile-threat-modelling.html

    on martinfowler.com.

    Include credential stuffing in your threat model
    If you use password-based authentication, prepare for the fact that users will reuse their passwords for other services, and some of those services will be breached.

Consider

    Consider learning the basics of cryptography
    If your application needs more cryptography than just password hashing and verification, consider learning the basics of cryptography to ensure that you know how to choose the correct cryptographic primitives and use them appropriately. I recommend the book Real-World Cryptography 

    (2021), which provides a clear and up-to-date introduction to the subject.

Don’t

    Don’t forget about infrastructure
    It’s not enough for your own application to be safe in isolation. Be mindful of the trust you place in other systems and consider the ways in which they can be compromised or spoofed. Remember that other applications running on the same domain can be used to attack your application.

    Don’t trust any data coming from the client
    If you need to store data on a per-client basis, only store a randomly generated key on the client and keep the actual data on the server. Alternatively, use a Message-Authentication Code (and, depending on your needs, encryption) to prevent the client from tampering with the data, but be wary of replay attacks 

.

### Broken Authorization

Weak or missing permission checks on sensitive data or functionality

Broken authorization is the common name for all vulnerabilities that allow the attacker, using their own identity, to access information or functionality that should be off-limits to them.

```
const PUBLIC_PATH_PREFIXES = [
  '/public/',
  '/authentication/'
];


function isPublicResource(request) {
  for (const prefix of PUBLIC_PATH_PREFIXES) {
    if request.path.startsWith(prefix) {
      return true;
    }
  }
  return false;
}


function authenticationFilter(request, nextHandler) {
  // If the request points to a public resource, pass it
  // on for further handling.
  if (isPublicResource(request)) {
    return nextHandler(request);
  }

  // If the user is authenticated, look up their details
  // and pass the request on for further handling.
  if (request.session.userId) {
    request.user = getUser(request.session.userId);
    return nextHandler(request);
  }

  // The requested URL is non-public and the user is
  // unauthenticated. Redirect them to authentication.
  return redirect('/authentication/');
}
```

The following example has explicit access control checks defined for requests to URLs with a path that looks like /org/<org-id>/invoice/<invoice-id>.

```
function isAuthorized(request) {
  const pathParts = request.path.split('/');

  // Requests to public resources are always allowed.
  if (isPublicResource(request)) {
    return true;
  }

  // Requests to paths like /org/<org-id>/*
  else if (pathParts[1] === 'org') {
    const orgId = pathParts[2];

    // Only admins and the members of an organization can
    // access the organization's information.
    if (request.user.isAdmin ||
        request.user.orgId === orgId) {

      // Requests to paths like
      // /org/<org-id>/invoice/<invoice-id>
      if (pathParts[3] === 'invoice') {

        // Only admins and people in the organization
        // with the roles 'owner' or 'billing' are
        // allowed to access invoices.
        if (request.user.isAdmin ||
            request.user.role === 'owner' ||
            request.user.role === 'billing') {
          return true;
        }
      }
    }
  }

  // All requests are considered unauthorized by default.
  return false;
}


function authorizationFilter(request, nextHandler) {
  // If the request was explicitly authorized, handle it.
  if (isAuthorized(request)) {
    nextHandler(request);
  }

  // Otherwise, reject it.
  return response(403);
}
```

```
describe('isAuthorized()', function(){

  it("users with the 'billing' role can access invoices", function(){
    const request = {
      user: {
        orgId: 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa',
        role: 'billing',
        isAdmin: false
      },
      path: '/org/aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa/invoice/7fd2c601-6c89-481a-b3d3-29aaafd96c7a'
    };

    assert.isTrue(isAuthorized(request));
  });


  it("users can't access other orgs' invoices", function(){
    const request = {
      user: {
        orgId: 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa',
        role: 'billing',
        isAdmin: false
      },
      // User is in org A, invoice is for org B.
      path: '/org/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/invoice/7fd2c601-6c89-481a-b3d3-29aaafd96c7a'
    };

    assert.isFalse(isAuthorized(request));
  });

  // ... and more tests for other rules ...

});
```

- Enforcing access control checks
- Don’t expose IDs
- Use randomly generated identifiers
- Write thorough unit tests for access control checks
- Implement audit logging


Dos and Don’ts

The following rules should help you minimize the risk of authorization-related vulnerabilities and the damage they can do.

Do

    Perform access control checks for every incoming request
    Instead of implementing access control checks in every HTTP endpoint separately, use the features of your HTTP framework – usually called filters or middleware – to perform authentication and authorization for every incoming request.

    Deny by default
    Reject requests unless the access control rules explicitly allow them.

    Unit test your access control code thoroughly
    Access control checks may contain complicated logic that is easy to get wrong. Unit test your access control code thoroughly to ensure it handles incoming requests in the intended way.

    Use random UUIDs
    Use random UUIDs (version 4) 

as your resource identifiers whenever possible, and ensure that your UUID library uses a secure

    random number generator.

Consider

    Consider audit logging for the most sensitive operations
    An audit log 

    may help you to investigate potential breaches.

Don’t

    Don’t perform access control checks on the client
    Client-side privilege checks should never be considered a security feature. They are at most a usability improvement that, for example, hides buttons that wouldn’t work with the user’s privileges. All access control checks should happen on the server.

    Don’t trust any data coming from the client
    If you need to store data on a per-client basis, only store a randomly generated key on the client and keep the actual data on the server. Alternatively, use a Message Authentication Code (MAC) 

(and, depending on your needs, encryption) to prevent the client from tampering with the data, but be wary of replay attacks

.

Don’t read resource IDs from the request unnecessarily
Only ask the client for the resource ID if it can’t be determined in another way. For example, instead of a URL path like
/user/<id>/profile/edit, use /user/my-profile/edit and assume that the user whose profile is being edited is always the authenticated user.



### Sensitive Data Exposure

Accidentally leaving secrets or confidential information publicly accessible

Sensitive data exposure, also known as information disclosure, happens when the application accidentally exposes sensitive data via its APIs, shared static files, front-end source code, or other channel.

Dos and Don’ts

Follow these guidelines to ensure that your applications don’t expose sensitive data.
Do

    Keep your deployment packages minimal
    Keep the packages you use for deployment (Docker images, NPM packages, binaries, .jar, .rpm, or .deb files, etc.) as minimal as possible. Omit all files that are used for development, especially the .git directory.

    Keep source code and configuration separate
    Never hard code 

    secrets into source code. Separate configuration from code, and use your deployment platform’s features to store the configuration.

Consider

    Consider using gitleaks https://github.com/zricethezav/gitleaks
    Gitleaks 

    can prevent you from committing secrets (when used as a pre-commit hook) and let you know as soon as someone pushes a commit containing secrets (when used in a CI/CD pipeline).

Don’t

    Don’t store unencrypted secrets in version control
    If you want to store secrets in version control, use a tool like Sops https://github.com/mozilla/sops

to encrypt them first.

### Cross-Site Request Forgery (CSRF)

Tricking a victim's browser into performing actions on another website

Cross-Site Request Forgery (CSRF or XSRF) is a vulnerability in applications that use cookies

for storing session identifiers or session state. To be attacked by CSRF, the victim must visit an attacker-controlled website.

The attacker can either use social engineering to trick the victim into visiting the website containing the exploit, or they may exploit a vulnerability (such as XSS or subdomain takeover) to hijack a legitimate website and wait for the victim to visit it. The attacker-controlled website can then make requests to the vulnerable application using the victim’s credentials.

Dos and Don’ts

The following rules will help you to protect your application against CSRF.
Do

    Use your web framework’s CSRF protection
    Practically all web frameworks have CSRF protection either built in or available as a library, but you may need to enable it.

    Mark your cookies explicitly as SameSite=Lax
    SameSite=Lax is becoming the new default setting, but you should use it explicitly until all major browsers have changed their default to Lax.

    Keep your CORS configuration minimal
    CORS disables protections that prevent websites from attacking each other. Consider the security implications carefully before adding new Access-Control-Allow-... rules.

Consider

    Consider using multiple domains
    If unrelated web content is moved to its own registrable domain (e.g. example.blog instead of blog.example.com) it becomes its own site from the browser’s point of view and the SameSite attribute will prevent it from making credentialed requests to the main domain and its subdomains (and vice versa).

    Consider using SameSite=Strict
    If you don’t need a cookie to be transmitted when a user clicks cross-site link to your application, you can use SameSite=Strict for it to protect against cross-site CSRF attacks that use the GET method.

Don’t

    Don’t use safe HTTP methods for write operations or actions
    Safe HTTP methods 

(e.g. GET or HEAD) should only be used for read-only operations that don’t perform any actions (such as sending emails), as they are typically excluded from CSRF protection.

Don’t serve applications on different paths of a single domain
If you configure a reverse proxy to serve two applications at https://example.com/app1 and https://example.com/app2, they have the same origin so the same-origin policy won’t protect them from each other.

Don’t use SameSite=None without a good reason
SameSite=Lax (or Strict) makes CSRF attacks significantly harder in case your main CSRF protection fails. You should have a good reason before disabling this extra layer of protection.

Don’t rely on SameSite as your only defense against CSRF
SameSite=Lax and SameSite=Strict prevent cross-site CSRF, but other subdomains of the same registrable domain can still attack your application.


### Server-Side Request Forgery (SSRF)

Tricking a backend server into performing HTTP requests on the attacker's behalf.

Server-side request forgery (SSRF) is a vulnerability that allows the attacker to trick a vulnerable server into making HTTP requests on the attacker’s behalf. This may give the attacker indirect access to data and services that they can’t access directly.

Resources:
- https://lock.cmpxchg8b.com/rebinder.html

```
async function makeRequest(urlString) {
  // Parse the URL string.
  const url = new URL(urlString);

  let ip;

  try {
    // Try to parse the URL's hostname as an IP address.
    ip = new Address4(url.hostname);
  } catch {
    // If it's not a valid IP address, try to resolve
    // it to one.
    ip = new Address4((await dns.resolve4(url.hostname))[0]);
  }

  // Check if the IP points to localhost, a link-local
  // address, or a private network.
  const privateSubnets = [
    new Address4('0.0.0.0/32'),
    new Address4('127.0.0.0/8'),
    new Address4('169.254.0.0/16'),
    new Address4('10.0.0.0/8'),
    new Address4('172.16.0.0/12'),
    new Address4('192.168.0.0/16')
  ];

  for (privateSubnet of privateSubnets) {
    if (ip.isInSubnet(privateSubnet)) {
      throw Error('Not on my watch!');
    }
  }

  // Create a new URL containing only the parts of the
  // input URL that we care about to minimize the
  // attack surface for parser inconsistency attacks.
  const sanitizedUrl = new URL('https://placeholder');
  sanitizedUrl.hostname = url.hostname;
  sanitizedUrl.pathname = url.pathname;

  // Serialize the sanitized URL to a new URL string.
  sanitizedUrlString = sanitizedUrl.href;

  // Perform the request using the sanitized URL string.
  response = await axios.get(sanitizedUrlString, {

    // Disable redirects.
    maxRedirects: 0,

    // Use a custom DNS lookup function, which returns
    // the known-good IP address.
    httpsAgent: new https.Agent({
      lookup: (hostname, options, callback) => {
        callback(null, ip.address, 4);
      }
    })

  });

  // Do something with the response.
  console.log(response.status);
}
```

Dos and Don’ts

The following rules will help you to protect your application against SSRF.
Do

    Validate the destination address before each HTTP request
    A hostname that initially points to an allowed destination can at any time start pointing to a forbidden destination.

    Use real parsers for URLs and IP addresses
    Use libraries designed for parsing and inspecting URLs and IP addresses.

    Protect against HTML injection
    Applications that run a browser on the server – for example, to produce a PDF document – may be vulnerable to SSRF via HTML injection. If you need to insert untrusted input into an HTML document, ensure it’s done in a safe way.

    Authenticate all requests
    Authenticate all requests, even if the application is supposedly only accessible by trusted users or services. The attacker may gain access to the application by exploiting SSRF in some other application on the same server or network.

    Use firewalls and network policies to restrict network access
    Only allow network connections between entities that have a legitimate need to communicate.

    For AWS EC2, use IMDSv2
    If you use AWS EC2, disable the older version, v1, of its Instance Metadata Service (IMDS) and only use v2, which has additional protections 

    against being accessed via SSRF.

Don’t

    Don’t let your HTTP client do DNS lookups
    Ensure that each HTTP request is made to a known and validated IP address.

    Don’t let your HTTP client follow redirects
    Configure the HTTP client to not follow redirects. If following them is a requirement, follow them manually and validate each URL’s destination before making a request to it.

    Don’t try to validate URLs or IP addresses with string pattern matching
    Attackers can use various tricks to bypass validation that is based on matching strings against known-bad patterns.

    Don’t read URLs from input unless it’s an explicit requirement
    If you can avoid it, don’t access URLs that have been read from untrusted input. Ideally URLs should come from the application’s configuration.

    Don’t store or pass on URL strings received as input
    Use parsed and validated URL objects whenever possible. Only use the parts of the URL that are allowed to vary in the input and use constants for the rest. If you need a URL string, serialize the validated URL object – do not re-use the original URL string received as input.
