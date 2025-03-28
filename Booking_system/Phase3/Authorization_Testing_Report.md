# Authorization testing

***Version: Phase 3 of the 'Booking System'***

## Testing techinque: browser

#### Table

| **Page / Feature** | **Guest** | **Reserver** | **Administrator** |
|:----|:----:|:----:|:----:|
| `/` (index)                | | | |
| └─ View resource form      | ✅ | ✅ | ✅ |
| └─ See login and register options     | ✅ | ❌ | ❌ |
| └─ See and access extra options     | ❌ | ✅ | ✅ |
| └─ Access Edit resources      | ❌ | ❌ | ✅ |
| └─ Access Change reservations      | ❌ | ❌/✅*1 | ✅ |
| `/login`                   | | | |
| └─ Can access panel      | ✅ | ✅⚠️*2 | ✅⚠️*2 |
| └─ Can login      | ✅ | ✅⚠️*2 | ✅⚠️*2 |
| `/register`                | | | |
| └─ Can access panel      | ✅ | ✅⚠️*2 | ✅⚠️*2 |
| └─ Can register      | ✅ | ✅⚠️*2 | ✅⚠️*2 |
| `/resources`               | | | |
| └─ Can access panel      | ✅⚠️*3 | ✅ | ✅ |
| └─ Can add resources      | ✅⚠️*3| ✅ | ✅ |
| └─ Can edit resources      | ❌ | ✅⚠️*4 | ✅ |
| `/reservation`             | | | |
| └─ Can access panel      | ❌ | ✅ | ✅ |
| └─ Can make reservation      | ❌ | ✅ | ✅ |
| └─ Can edit reservation      | ❌ | ✅⚠️*4 | ✅ |

**Symbols legend:**  
✅ Accessible
❌ Access restricted
⚠️ Attention - this require changes

**Notes:**  
**1.** Loged in users with reserver role can access only edition of reservation of their own reservations, which is good in the context of the system expectations.
**2.** The best approach to the `/register` and `/login` endpoints while been already loged in, is redirection to the main/profile page. Current approach allows for loged in users to access those endpoints. 
**3.** This is critical issue, the guest can acces the resources endpoint and add new resources. This makes the system vulnerable, the `/resources` endpoint should check authorization of the requests.
**4.** The edition or reservations should be only possible for own reservations when it comes to the reserver role. But by been able to acces this pannel we can jump around other reservation accesing the ?=idx (where x is the number 1,2....,n representing index of reservation), allowing the reserver to access edition of reservation of other users.

---

## Testing technique: ZAP

**Pages found by Zap spider scan:**
- /static/reservationsForm.js
- /static/resourceForm.js
- /static/tailwind.css
- /api/reservations/id
- /api/resources
- /api/resources/id
- /api/users

#### Extended table for ZAP findings
| **Accessible Pages** | **Danger** |
|:----|:----:|
| `/static`                | |
| └─ `/reservationsForm.js`      | ✅*5 |
| └─ `/resourceForm.js`      | ✅*5 |
| └─ `/tailwind.css`      | ✅*5 |
| `/api`                | |
| └─ `/reservations/id`      | ❌*6 |
| └─ `/resources`      | ❌*6 |
| └─ `/users`      | ❌*7 |

**Symbols legend:**  
✅ Access is fine 
❌ Access is possible but insecure or problematic

**Notes:**  
**5**. Access to the static files (`.js`,`.css`) is typicaly fine to be public, the only concern would be making sure that there is no sensitive data exposed in the JavaScript files.
**6.** Access to the api endpoints (`/reservations/id`,`/resources`) should not be openly accessible, the access control to those resources should be enforced to ensure only authorized users can access them.
**7.** The sensitive information like user tokens, usernames and roles are not properly secured and are visible for everyone. User tokens can be used to abuse authentication and authorization. Usernames can be used to make brute force atacks. Roles can be used to aim at the administrator accounts for privilege escalation.

**Extra information after Zap testing**
- Ensure **access control** on API endpoints (especially for authenticated resources).  
- Implement **data protection** practices for user information (e.g., tokens, roles).
- **Format String Error (Medium)**
    - **Summary:** A format string vulnerability was detected in the resource_name parameter when performing a POST request to /resources. This vulnerability occurs when user input is evaluated as a format string, which could lead to unintended command execution or information disclosure. The application needs to handle inputs securely by sanitizing and validating them before processing.
    - **Solution:** Implement proper input validation and prevent format string errors by using a safe way to handle user input. This may involve re-compiling background processes to correctly handle special characters in input.
- **Cross Site Scripting Weakness (Persistent in JSON Response) (Low)**
    - **Summary:**  XSS (Cross-Site Scripting) vulnerabilities were found in the resource_description and resource_name parameters during GET requests to /api/resources. These issues occur when unsanitized user input is reflected in a JSON response, which could be exploited by an attacker to inject malicious scripts.
    - *Solution:*  Sanitize and encode user inputs before returning them in responses. This includes implementing output encoding and employing frameworks that protect against XSS, such as OWASP ESAPI.

---

## Testing technique: wfuzz and http

#### Common words approach
command - `wfuzz -c -w /usr/share/wordlists/dirb/common.txt --hc 404 http://localhost:8000/FUZZ`

**Table**
| ID         | Response | Lines | Word | Chars | Payload         |
|------------|----------|-------|------|-------|-----------------|
| 000000001  | <span style="color:green">200</span>      | 45 L  | 176 W | 2749 Ch | "http://localhost:8000/" |
| 000003341  | <span style="color:green">200</span>      | 44 L  | 202 W | 2978 Ch | "register"      |
| 000003404  | <span style="color:green">200</span>      | 38 L  | 157 W | 2235 Ch | "resources"     |
| 000003395  | <span style="color:red">401</span>      | 0 L   | 1 W   | 12 Ch  | "reservation"   |
| 000002362  | <span style="color:blue">302</span>      | 0 L   | 0 W   | 0 Ch   | "logout"        |
| 000002347  | <span style="color:green">200</span>      | 32 L  | 137 W | 1967 Ch | "login"         |

**Note**
**8.** No new pages found


#### Api folder scaning
command - `wfuzz -c -w /usr/share/wordlists/dirb/common.txt --hc 404 http://localhost:8000/api/FUZZ`

**Table**
| Response | Lines | Word | Chars | Payload         |
|----------|-------|------|-------|-----------------|
| <span style="color:green">200</span> | 0 L   | 3085 W | 137476 Ch | "resources"    |
| <span style="color:red">401</span>  | 0 L   | 1 W   | 12 Ch   | "session"       |
| <span style="color:green">200</span> | 0 L   | 1 W   | 619 Ch  | "users"         |

**Note**
**9.** The `/api/session` endpoint was found, but access is restricted for guest users. Logged-in users do not see any confidential information from this endpoint.

#### Reservation folder scaning for extra pages
command - `wfuzz -c -z range,1-1000 --hc 404 http://localhost:8000/api/reservations/FUZZ`

**Table**
| Response | Lines | Word | Chars | Payload         |
|----------|-------|------|-------|-----------------|
| <span style="color:green">200</span> | 0 L   | 1 W   | 184 Ch | "1"            |
| <span style="color:green">200</span> | 0 L   | 1 W   | 184 Ch | "3"            |
| <span style="color:green">200</span> | 0 L   | 1 W   | 186 Ch | "36"           |


**Note**
**10.** Thanks to the wfuzz command the id of the reservations were idenfitied, which are `1`, `3`, `36`.

#### Http test of reservations page found endpoint
command - `http http://localhost:8000/api/reservations/36`

**Response**
```http
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 147
content-security-policy: default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self'; frame-ancestors 'none'; form-action 'self';
content-type: application/json
date: Mon, 24 Mar 2025 13:07:57 GMT
vary: Accept-Encoding
x-content-type-options: nosniff
x-frame-options: DENY

{
    "reservation_end": "2025-03-29T09:09:00.000Z",
    "reservation_id": 36,
    "reservation_start": "2025-03-24T09:09:00.000Z",
    "reserver_token": "8da76bf0-606a-498e-b967-7aa65efc533e",
    "resource_id": 38
}
```

**Note**
**11.** The API response exposes a reserver_token, which is a security risk as it may allow unauthorized access to reservations. This token should not be included in public responses. Instead, it should be sent only to authenticated users who have the appropriate permissions. Implementing access controls and restricting API responses will help mitigate potential security vulnerabilities.

---

## Application specifications analysis

1. The system is accessed via a web browser. ✅
- ✅ The system can be accessed via web browser.
2. Users can register and, after registration, log in to the system. ✅
- ✅ System allows for process of registration and login management.
3. A registered and logged-in user acts as either a resource reserver or an administrator. ✅/❌
- ✅ The system distinguish between reserver and administrator role, but ❌ the endpoints `/resources` and `/reservation` are not validating access allowing for the reserver to edit those information (for more information look at `note 4`). Same can be said about `/resources` alone where quests can access this panel and add new resources (for more information look at `note 3`).
4. The administrator can add, remove, and modify resources and reservations. ✅/❌
- ✅ The system allows for the administrator role users to add and modify the resources and reservations, but ❌ delition of the resources is not implemented.
5. The administrator can delete the reserver. ❌
- ❌ Not implemented yet 
6. A reserver can book a resource if they are over 15 years old. ✅/⚠️❌
- ✅ Resources can be only booked by users that are over 15 years old, but ⚠️❌ due to not secured `/reservation` endpoint the underaged accounts can 'steal' other people reservations.
7. Resources can be booked on an hourly basis. ✅/⚠️
- ✅ Resources can be booked on a hourly basis, but ⚠️ the option is a bit bugged and sometimes required manualy inputing the hours by keyboard and not by selection panel. 
8. The booking system displays booked resources without requiring login, but does not show the reserver's identity. ✅
- ✅ The system shows booked resources for quests and the identity of the users is hiden.

---

## Suggested extra changes

#### Fix Authorization Issues
- Restrict guest access to `/resources` to prevent unauthorized modifications.
- Ensure that only the reserver who made the booking can edit their reservation.
- Prevent unauthorized users from accessing and modifying other users' reservations via URL manipulation.

#### Secure API Endpoints
- Implement role-based access control (RBAC) for `/api/resources` and `/api/reservations`.
- Ensure that sensitive API responses (e.g., `reserver_token`) are only visible to authenticated users.
- Limit exposure of user roles and usernames in `/api/users` to prevent brute-force attacks.

#### Fix Authentication Flaws
- Redirect logged-in users away from the `/login` and `/register` pages (to the dashboard or profile).
- Implement session management to prevent unauthorized users from staying logged in indefinitely.

#### Input Validation & Security Improvements
- Sanitize all user inputs to prevent format string errors in `/resources`.
- Implement output encoding for JSON responses to prevent Cross-Site Scripting (XSS) attacks.

#### Enhance Logging & Monitoring
- Implement logging mechanisms to track unauthorized access attempts.
- Add rate limiting to prevent brute-force attacks on login endpoints.

#### User Management Features
- Add an option for the administrator to delete resourcers (reservers).
- Implement resource deletion functionality for administrators.

#### Fix UI/UX Issues
- Improve the hour selection mechanism in the booking system to prevent users from manually typing incorrect values.
- Ensure proper error messages and feedback when users input invalid data.

---

## Appendices:

- ZAP report version 1: Checkmarx-ZAP-V1.md
