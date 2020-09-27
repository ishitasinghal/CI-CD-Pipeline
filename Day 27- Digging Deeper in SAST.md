# Day 27- Digging Deeper in SAST

### Concept of Static Application Security Testing

SAST mainly focuses on the analysis of the static code to check whether the code is exposed to attacks and security vulnerabilities. The code is scanned before it is compiled. 

### Application Security Vulnerability

Any loopholes left in the code in which an attacker can attack can and cause any kind of harm in the application according to the OWASP, is application security vulnerability. These can be created such as any bugs and errors in the code which are left unaddressed which could result in networks and system prone to attack.

There are different types of software vulnerabilities such as:

* **Porous defenses**

  Shielding techniques entered in the program such as authentication for a particular plugin, or any third-party software involvement in the application are essential for the application if used wisely, but at the same time, plays at the edge of security if misused. These include missing or incorrect authentication,  incorrect permissions assignment, missing authorization and missing proper encryption.

  

* **Risky resource management**

  It involves involvement of different resources in the project such as creation, deletion, transfer of resources such as memory.  The vulnerabilities it brings with it is buffer overflow, functionality from an untrusted control sphere. We should be able to make out which whether the sources of input are secured or not. 

  

* **Insecure interaction between components**

  This type involves things like SQL injection, cross-site scripting. This is a very important threat that needs to be looked upon.

  

* **API abuse**

  This involves protection of APIs from bot attacks, which could lead to data loss. Going through these issues helps in eliminating fake account creation which could otherwise degrade user confidence.

  

* **Input Validation**

  This check prevents user from entering any improper input. An attack to input validation involves entering manually strange and unexpected information into a normal user input field. Therefore, this needs to be taken care of.

  

* **Session Management Vulnerability**

  A session is initiated by a user for a specific time bound which is identified by a session token which is delivered as cookie to the browser. That cookie must be protected as it could involve attacks known as session hijacking. Once the session token is accessible, all the conversations can be stolen happening between the user and the browser which would be prone to risks and leaking important information.



### Links Referred:

1. "https://www.synopsys.com/glossary/what-is-sast.html"
2. "https://www.synopsys.com/blogs/software-security/types-of-security-vulnerabilities/"
3. "https://owasp.org/www-project-top-ten/"
4. "https://cwe.mitre.org/top25/archive/2019/2019_cwe_top25.html"

