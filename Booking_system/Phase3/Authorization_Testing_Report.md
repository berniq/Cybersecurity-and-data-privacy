# Authorization testing

***Version: Phase 3 of the 'Booking System'***

## Testing techinque

### Table

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
1. Loged in users with reserver role can access only edition of reservation of their own reservations, which is good in the context of the system expectations.
2. The best approach to the `/register` and `/login` endpoints while been already loged in, is redirection to the main/profile page. Current approach allows for loged in users to access those endpoints. 
3. This is critical issue, the guest can acces the resources endpoint and add new resources. This makes the system vulnerable, the `/resources` endpoint should check authorization of the requests.
4. The edition or reservations should be only possible for own reservations when it comes to the reserver role. But by been able to acces this pannel we can jump around other reservation accesing the ?=idx (where x is the number 1,2....,n representing index of reservation), allowing the reserver to access edition of reservation of other users.

## Testing technique: ZAP

**Pages found by Zap spider scan:**
- /static/reservationsForm.js
- /static/resourceForm.js
- /static/tailwind.css
- /api/reservations/id
- /api/resources
- /api/resources/id
- /api/users

### Extended table for ZAP findings
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
5. Access to the static files (`.js`,`.css`) is typicaly fine to be public, the only concern would be making sure that there is no sensitive data exposed in the JavaScript files.
6. Access to the api endpoints (`/reservations/id`,`/resources`) should not be openly accessible, the access control to those resources should be enforced to ensure only authorized users can access them.
7. The sensitive information like user tokens, usernames and roles are not properly secured and are visible for everyone. User tokens can be used to abuse authentication and authorization. Usernames can be used to make brute force atacks. Roles can be used to aim at the administrator accounts for privilege escalation.

**Extra information after Zap testing**
- Ensure **access control** on API endpoints (especially for authenticated resources).  
- Implement **data protection** practices for user information (e.g., tokens, roles).
- **Format String Error (Medium)**
    - **Summary:** A format string vulnerability was detected in the resource_name parameter when performing a POST request to /resources. This vulnerability occurs when user input is evaluated as a format string, which could lead to unintended command execution or information disclosure. The application needs to handle inputs securely by sanitizing and validating them before processing.
    - **Solution:** Implement proper input validation and prevent format string errors by using a safe way to handle user input. This may involve re-compiling background processes to correctly handle special characters in input.
- **Cross Site Scripting Weakness (Persistent in JSON Response) (Low)**
    - **Summary:**  XSS (Cross-Site Scripting) vulnerabilities were found in the resource_description and resource_name parameters during GET requests to /api/resources. These issues occur when unsanitized user input is reflected in a JSON response, which could be exploited by an attacker to inject malicious scripts.
    - *Solution:*  Sanitize and encode user inputs before returning them in responses. This includes implementing output encoding and employing frameworks that protect against XSS, such as OWASP ESAPI.

## Testing technique: wfuzz and http



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

## Sugested extra changes



## Appendices:

- ZAP report version 1: Checkmarx-ZAP-V1.md
