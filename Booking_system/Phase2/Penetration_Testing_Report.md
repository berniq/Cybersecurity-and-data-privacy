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

-	Verifying that the user registration process is secure.
-   Verifying that the user login process is secure.
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

None

### Yellow (Medium-Risk Vulnerabilities).

None

### Green (Low-Risk Vulnerabilities).

None

### Grey (Informational Vulnerabilities).

#### User Agent Fuzzer:

-	Description – A User-Agent fuzzer generates and tests a wide range of User-Agent strings to check how the application will handle them. This alone is not a vulnerability, but can lead to the problems if handled incorrectly.
-	Impact – As it doesn’t posses high impact security threat, improper handing of User-Agent strings can lead to inconsistencies that could be exploited. If left unchecked, different types of responses could lead to security bypass or unwanted behaviors, which might result of exposing vulnerabilities.


## 4. Appendices:

- ZAP report version 1: Checkmarx-ZAP-V1.md
