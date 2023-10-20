

# Hydra Brute force

## Context summary
The website at this level contains a basic authentication system (**login**, **password**).

## Vulnerability


There are several vulnerabilities, the first is that there is no test limit for passwords, in other words, we can brute force the password.

The second Vuln is the type of request, by observing in the browser's network exchanges we see that a **GET** request is used to log in instead of **POST**.

***GET:*** Data is visible in the URL, which may pose security and privacy concerns because it may be saved in browser history and server logs.

***POST:*** The data is not visible in the URL, making it more suitable for sensitive information.

As a result we have a serious problem of visibility in the exchange of data. We will focus on the brute force vulnerability, the vulnerability mentioned recently is mainly a problem for someone who wishes to intercept certain packets exchanged through your network.

## Attack Design
We will use **Hydra** (Hydra is to online-cracking of passwords, what John The Ripper is to offline-cracking of password hashes) for brute force, this tool is a parallelized login cracker that supports many attack protocols.

The list of supported protocols is impressive : Asterisk, *AFP, Cisco AAA, Cisco authentication, Cisco activation, CVS, Firebird, FTP, HTTP-FORM-GET, HTTP-FORM-POST, HTTP-GET, HTTP-HEAD, HTTP-POST, HTTP-PROXY, HTTPS-FORM -GET, HTTPS-FORM-POST, HTTPS-GET, HTTPS-HEAD, HTTPS-POST, HTTP proxy, ICQ, IMAP, IRC, LDAP, MS-SQL, MYSQL, NCP , NNTP, Oracle listener, Oracle SID, Oracle , PC-Anywhere, PCNFS, POP3, POSTGRES, RDP, Rexec, Rlogin, Rsh, RTSP, SAP/R3, SIP, SMB, SMTP, Enum SMTP, SNMP v1 + v2 + v3 , SOCKS5, SSH (v1 and v2), SSHKEY, Subversion, Teamspeak (TS2), Telnet, VMware-Auth, VNC and XMPP*. 

### Hydra walkthrough

To install hydra : **`sudo apt install hydra`**

Familiarize yourself with the environment : **`hydra -h`**

Basically, to build your command line you need to specify two things :

1.  Where the username and password go.
2.  How to determine success/failure of the request.

The way that Hydra determines if an attempt was successful or not is by comparing strings found in successful and unsuccessful return pages.

The general syntax for us is :

**`hydra -L [USERNAME_LIST].txt -P [ROCKYOU].txt [IP_ADDR] http-get-form "/index.php:page=signin&username=^USER^&password=^PASS^&Login=Login:F=images/WrongAnswer.gif"`**

Let's dissect above command-line :

- **`hydra`**: This is the name of the command, which is the penetration testing tool Hydra.
    
- **`-P [].txt`** and  **`-L [].txt`**: The **`-P`** and **`-L`** option specifies the password / username file that Hydra will use for login attempts. In this example, "rockyou.txt" is the password file containing a list of passwords and top_usernames.txt for usernames.
    
- **`192.168.56.103`**: This is the IP address of the target host or server that you want to perform the brute-force attack on.
    
- **`http-get-form`**: This part of the command specifies the method for the attack, which is HTTP GET with a form submission.
    
- **`"/index.php:page=signin&username=^USER^&password=^PASS^&Login=Login:F=images/WrongAnswer.gif"`**: This part defines the HTTP GET request to be sent. It includes the URL path ("/index.php") and form parameters ("username" and "password") to be brute-forced. The `^USER^` and `^PASS^` placeholders will be replaced with the actual username and password during the attack. "Login=Login" is the login button or form field name, and `"F=images/WrongAnswer.gif"` specifies the expected failure response, which is a URL that indicates a failed login attempt.
- **`-t 64`** : This flag in Hydra specifies the number of threads or concurrent connections during a brute force attack. It sets the program to use **64 threads** simultaneously for password cracking.



## Remediation
1) Migrate **GET** authentication logic to **POST**. The causes are explained in the [Vulnerability](##Vulnerability) section.

2) Choose a secure password, since dictionaries allow us to crack it, just DM me if your want a secure password, I will send you.

3) Set an authentication attempt limit, such as account lockout or other.
4) Use **HTTPS** protocol for authentication, data transmitted over **HTTP** is not encrypted.


> Flag : `B3A6E43DDF8B4BBB4125E5E7D23040433827759D4DE1C04EA63907479A80A6B2`
