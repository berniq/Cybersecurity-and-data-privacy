# Penetration Testing Report

***Version: Phase 1 of the 'Booking System'***

## 1. Introduction:

### Purpose and scope of the report.

This report is meant to showcase the vulnerabilities found in the user registration part of the booking system software.  The scope of the report will introduce the environment and methods used to extract the security flaws, while presenting the security issues found in the software. The testing was performed using gray-box approach and the findings are categorized and described into detail, why it’s an issue and how to mitigate the most critical ones. To support this report there will be attached generated ZAP report using zaproxy penetration software. 

### Testing schedule and environment.

Initial test was performed on 20.02.2025. The OS used to perform those tests was Kali Linux running on the virtual machine. The application was deployed locally using dockerization with the IP http:/0.0.0.0:8000. The registration element tested was behind the following endpoint: ‘/register’, using automated penetration security scanning mixed with manual testing.

**Updated 03.03.2025**

Testing was performed on updated version, using the same testing environment and tools as in initial test.

### Scope of testing.

The main focus of the penetration testing was placed of finding vulnerabilities in the user registration section of the application. The following aspects were checked:

#### 1. Authentication and Authorization:

-	Verifying that the user registration process is secure.
-	Verifying that the user roles and permissions are protected and cannot be bypassed.

#### 2. Input Validation and Injection Attacks:

-	Testing that all user inputs are properly validated and sanitized.
-	Looking for injection attacks, such as SQL Injection and Cross-Site Scripting.
-	Analyzing how the system processes user-provided data to detect path traversal risks.

#### 3. Error Handling and Logging:

-	Ensured that error messages do not expose sensitive system details to potential attackers.

#### 4. Security Headers and Browser Protections:

-	Checked for missing security headers, such as Content Security Policy (CSP) and anti-clickjacking measures.
-	Evaluated the presence of X-Content-Type-Options headers to prevent MIME-based attacks.

#### 5. Data Encryption:

-	Ensuring that all sensitive data is encrypted both in transit and at rest.

### Methods and tools used for testing.

To test the application, an automated penetration testing was performed using the ZAP (Zed Attack Proxy), including some manual testing. Due to mixed knowledge about the system, like providing information how to send a POST method, but with unknown data processing inside, the gray-box testing method was applied. The results were used as separate ZAP report, which was analyzed to identify potential security risks and to analyze their impact on the overall security of the application.

## 2. Summary:

### Key findings and recommendations.

#### 1. SQL Injection (High):

-	Finding – The application is exposed to the SQL attacks, allowing for the attackers to manipulate database queries and allowing for possibilities to extract, modify, or delete sensitive data. This is accessed via username Parameter.
-	Impact – Unauthorized access to database records, data corruption.
-	Recommendation:
    -	Use of PreparedStatement or CallableStatement and parametrizing queries to avoid injection attacks.
    -	Implementation of ‘allow list’ and input validation, which will restrict use of special characters in the input windows.
    -	Applying least privilege principles in the database access.

#### 2. Path Traversal (High):

-	Finding – System allows path traversal, which enables attackers to access files outside the web document root directory.
-	Impact – Unauthorized access to sensitive files, like configuration files, logs and source code.
-	Recommendation:
    -	Sanitization and validation of user-supplied file paths to prevent path traversal (../, ..\ etc.).
    -	Restriction of access to only necessary directories that will be used.
    -	Configuration of settings which will deny access to sensitive directories.

#### 3. Data Encryption (High):

-	Finding – User data that is stored in the database is not encrypted, which leaves it vulnerable to data breaches.
-	Impact – If the database is compromised, the data stored is accessible to the outside world, which violates GDPR compliances. Which would result in receiving a fine for data breach. 
-	Recommendation:
    -	Encryption of sensitive data at rest using encryption algorithms (AES-256).
    -	Usage of hashed and salted passwords (e.g., bcrypt, Argon2, PBKDF2, or SHA-256/SHA-512) instead of plaintext storage in database.
    -	Implementation of database access controls which will allow for minimalization of exposure.

**Updated 03.03.2025**

All previous problems have been resolved leaving only Informational level warning, which doesn’t particularly mean that there is critical vulnerability, but it should be looked into more and analyzed.

#### User Agent Fuzzer (Informational/Low):

-	Finding – The system response differs depending on fuzzed User-Agent headers, initially there are no security issues detected, but it might potentially cause problems.
-	Impact – Different types of responses could lead to security bypass or unwanted behaviors, which might result of exposing vulnerabilities.
-	Recommendation:
    -	Ensuring of consistent response not impacted by the User-Agent headers.
    -	Validation and sanitization of User-Agent headers which would prevent manipulation.
    -	Implementation of rate-limiting or CAPTCHA	 for suspicious User-Agent connections.

### General security posture assessment.

Following all the findings, the application has a lot of vulnerabilities that could be used to exploit to access the user data and affect system integrity. The main focus in current phase should be placed on the possibility of injection attacks (SQLi, Path Traveral) and lack of data encryption. The main steps to follow should be focused on stronger access control from the outside, comply to the rules of secure coding, and ensuring stronger data protection, which should allow for enhanced overall security of the application.

## 3. Findings and Categorization:

### Red (High-Risk Vulnerabilities).

#### SQL Injection:

-	Description – SQL Injection happens when the user input is not properly sanitized, which allows the attackers to inject malicious SQL queries into database. When the user input can directly intervene with SQL queries without proper validation or escaping, this method can be used to penetrate the system.
-	Impact – When exploited, attackers can gain unauthorized access to sensitive data, allowing for modification, extraction and deletion. This can lead to data breaches also resulting in losing public picture and been faced with fine for not following GDPR compliances.

#### Path Traversal:

-	Description – Path Traversal attack technique allows an attacker access to files, directories, and commands that potentially reside outside the web document root directory. An attacker may manipulate a URL in such a way that the web site will execute or reveal the contents of arbitrary files anywhere on the web server. Any device that exposes an HTTP-based interface is potentially vulnerable to Path Traversal.
-	Impact – Attackers can use path traversal to access sensitive files like configuration files, logs and source code. Which can result into giving more insight into how the server/application operates allowing for escalated attacks in the future.

#### Data Encryption:

-	Description – Data Encryption vulnerability happens when sensitive data such as passwords or personal information, is transferred or stored without proper encryption. This might make the sensitive data been easily visible to the attacker or taken over during transmission. 
-	Impact – Lack of the data encryption can lead to sensitive data been leaked, leading to the unauthorized access to user accounts, sensitive information, or to identity theft.

### Yellow (Medium-Risk Vulnerabilities).

#### CSP Header Not Set:

-	Description – Content Security Policy (CSP) is an added layer of security that helps to detect and mitigate certain types of attacks, including Cross Site Scripting (XSS) and data injection attacks. These attacks are used for everything from data theft to site defacement or distribution of malware.
-	Impact – Scripts can be injected into the application, which can be executed in users application, causing possibility of data stealing or performance of action on behalf of the user.

#### Format String Error:

-	Description – A Format String error occurs when the submitted data of an input string is evaluated as a command by the application. Which can allow the attackers to control the output.
-	Impact – This can allow for the memory corruption, code execution, or even allowing for the full system take over.

#### Missing Anti-clickjacking Header:

-	Description – This is the technique where the attacker is trying to trick the user into clicking something different than it is really perceived. If there isn’t any anti-clickjacking header, this allows the application to be vulnerable to this kind of attacks.
-	Impact – Attackers can manipulate users into performing actions that were not meant by them, its done by embedding the site with a malicious iframes.

### Green (Low-Risk Vulnerabilities).

#### X-Content-Type-Options Header Missing:

-	Description – Not setting the X-Content-Type-Options header to the ‘nosniff’, can allow to interpret files as a different MIME types. This can allow for execution/interpretation of filles as different types, which can result in allowing of the unwanted scripts execution.
-	Impact – At this stage it is a low-risk issue, but missing the header can lead to more severe problems. As wrong interpretation of the files can potentially lead into Cross Site Scripting (XSS) vulnerability or execution of unwanted content.

### Grey (Informational Vulnerabilities).

#### User Agent Fuzzer:

-	Description – A User-Agent fuzzer generates and tests a wide range of User-Agent strings to check how the application will handle them. This alone is not a vulnerability, but can lead to the problems if handled incorrectly.
-	Impact – As it doesn’t posses high impact security threat, improper handing of User-Agent strings can lead to inconsistencies that could be exploited. If left unchecked, different types of responses could lead to security bypass or unwanted behaviors, which might result of exposing vulnerabilities.

**Updated 03.03.2025**

All critical vulnerabilities have been resolved in updated versions. Leaving only possibility for User-Agent headers manipulation. In current state this doesn’t show significant security issue, but in future updates it might provide for potential risk of inconsistency.

## 4. Appendices:

- ZAP report version 1: Checkmarx-ZAP-V1.md

**Updated 03.03.2025**

- ZAP report version 2: Checkmarx-ZAP-V2.md
