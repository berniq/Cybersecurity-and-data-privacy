# Penetration Testing Report

***Version: Phase 2 of the 'Booking System'***

## 1. Introduction:

### Purpose and scope of the report.

This report is meant to showcase the vulnerabilities found in the user registration and login part of the booking system software.  The scope of the report will introduce the environment and methods used to extract the security flaws, while presenting the security issues found in the software. The testing was performed using gray-box approach and the findings are categorized and described into detail, why it’s an issue and how to mitigate the most critical ones. To support this report there will be attached generated ZAP report using zaproxy penetration software. 

### Testing schedule and environment.

Initial test was performed on 06.03.2025. The OS used to perform those tests was Kali Linux running on the virtual machine. The application was deployed locally using dockerization with the IP http:/0.0.0.0:8000. The registration element tested was behind the following endpoint: ‘/register’, while loging element was tested on endpoint: '/login', using automated penetration security scanning mixed with manual testing.

### Scope of testing.

The main focus of the penetration testing was placed of finding vulnerabilities in the user registration and login section of the application. The following aspects were checked:

#### 1. Authentication and Authorization:

-   Verifying that the user registration process is secure.
-   Verifying that the user login process is secure.
-   Verifying that the user roles and permissions are protected and cannot be bypassed.

#### 2. Input Validation and Injection Attacks:

-   Testing that all user inputs are properly validated and sanitized.
-   Looking for injection attacks, such as SQL Injection and Cross-Site Scripting.
-   Analyzing how the system processes user-provided data to detect path traversal risks.

#### 3. Error Handling and Logging:

-   Ensured that error messages do not expose sensitive system details to potential attackers.

#### 4. Security Headers and Browser Protections:

-   Checked for missing security headers, such as Content Security Policy (CSP) and anti-clickjacking measures.
-   Evaluated the presence of X-Content-Type-Options headers to prevent MIME-based attacks.

#### 5. Data Encryption:

-   Ensuring that all sensitive data is encrypted both in transit and at rest.

#### 6. Session Management:
-   Ensuring that sessions are secure and expire properly.
-   Checking that session tokens are sufficiently complex and cannot be guessed.

### Methods and tools used for testing.

To test the application, an automated penetration testing was performed using the ZAP (Zed Attack Proxy), including some manual testing. Due to mixed knowledge about the system, like providing information how to send a POST method, but with unknown data processing inside, the gray-box testing method was applied. The results were used as separate ZAP report, which was analyzed to identify potential security risks and to analyze their impact on the overall security of the application.

## 2. Summary:

### Key findings and recommendations.

#### User Agent Fuzzer (Informational/Low):

-   Finding – The system response differs depending on fuzzed User-Agent headers, initially there are no security issues detected, but it might potentially cause problems.
-   Impact – Different types of responses could lead to security bypass or unwanted behaviours, which might result of exposing vulnerabilities.
-   Recommendation:
    -   Ensuring of consistent response not impacted by the User-Agent headers.
    -   Validation and sanitization of User-Agent headers which would prevent manipulation.
    -   Implementation of rate-limiting or CAPTCHA   for suspicious User-Agent connections.

#### Authentication Request Identified (Informational/Low):

-   Finding – The application correctly handles the authentication requests, with parameters such as 'username' and 'password'. This is good practice, but it allows for deduction that authentication endpoints can be detected and might be used to perform brute-force attacks, or to gather more information about the system using enumeration techniques.
-   Impact – Exposing the authentication request patterns can guide the attackers to make more defined attacks, allowing them to follow with the brute force or credential stuffing attempts. If there is no extra security steps taken care of, this might lead to getting unauthorized access by the attackers.
-   Recommendation:
    -   Implementation of rate limiting and allowing for acount blockage mechanism to reduce the posibilities of brute-force attacks.
    -   Enabling Multi-Factor Authentication (MFA) to reduce the risk of stealing the credentials.
    -   Monitoring of authentication request for abnormal behaviors and patterns, including failed logins and places where it was taken.

### General security posture assessment.

Following all the findings, the application has only an informational alerts, with no significant high/medium-risk vulnerabilities. There are no security reasons to fix those issues. They server as informational purpose to look out for them in the future development phases, to ensure application consistency and reducing the potential risk to be exploited.
Due to non-existing any sign of vulnerabilities, it indicates that the current state of the application is in good security condition. To ensure that it will stay that way, regular security checks should be performed focusing on the informational alerts.

## 3. Findings and Categorization:

### Red (High-Risk Vulnerabilities).

None

### Yellow (Medium-Risk Vulnerabilities).

None

### Green (Low-Risk Vulnerabilities).

None

### Grey (Informational Vulnerabilities).

#### User Agent Fuzzer:

-   Description – A User-Agent fuzzer generates and tests a wide range of User-Agent strings to check how the application will handle them. This alone is not a vulnerability, but can lead to the problems if handled incorrectly.
-   Impact – As it doesn’t possess high impact security threat, improper handling of User-Agent strings can lead to inconsistencies that could be exploited. If left unchecked, different types of responses could lead to security bypass or unwanted behaviors, which might result of exposing vulnerabilities.

#### Authentication Request Identified:

-   Description – Authentication request identification is used to detect and analyze login related requests in the application. This alone is not a vulnerability, but can lead to the problems if handled incorrectly, allowing for more insight into operating structure of the authentication process.
-   Impact – As it doesn’t posses high impact security threat, it can provide useful information for security testers or attackers. If other security vulnerabilities exist within the application, such as username enumeration, weak credential policies, or improper error handling, they might be used to perform more specified attacks.

## 4. Appendices:

- ZAP report version 1: Checkmarx-ZAP-V1.md
