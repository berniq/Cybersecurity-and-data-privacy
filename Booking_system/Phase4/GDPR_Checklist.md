# GDPR Compliance Checklist – Web-based Booking System

| **Result** | **Personal data mapping and minimization** |
| :----: | :--- |
| &nbsp;✅&nbsp; | The registration form collects the following personal data: email and age. |
| &nbsp;✅&nbsp; | The bare minimum personal data is colected, the email to differentiate the users and age to validate the restriction to the application. |
| &nbsp;✅&nbsp; | The registration form prevents users under the age of 15 from registering by disabling date selections that would indicate a younger age. The system appears to enforce the age restriction at registration level. Since this evaluation is based solely on the web interface, backend-level enforcement of this rule cannot be verified. |

---

| **Result** | **User registration and management** |
| :----: | :--- |
| &nbsp;✅&nbsp; | The registration form (page) include GDPR-compliant consent for processing personal data as a visible acceptance of the privacy policy check-box. |
| &nbsp;❌&nbsp; | User can only view their personal data, the edition and deletion of the personal data is not possible. |
| &nbsp;❌&nbsp; | The administrator dont have an option to delete the users alowing them for the "right to be forgotten". |
| &nbsp;✅&nbsp; | The underage users cannot complete the  registration process restricting the acces to the booking functionality of the system. |

---

| **Result** | **Booking visibility** |
| :----: | :--- |
| &nbsp;✅&nbsp; | The personal data of users is not been displayed to the guest users. |
| &nbsp;❌/⚠️&nbsp; | The public data as email is visible to other users, as there is no posibility for the user to hide this information, the system should use something like username to identify the users not their personal data or at least allow them to chose if they are fine with exposing their email. |

--- 

| **Result** | **Access control and authorization** |
| :----: | :--- |
| &nbsp;❌/⚠️&nbsp; | The resources can be only created and edited there is no posibility for deletion. At the same time every user can create and edit any resource in the application. |
| &nbsp;✅/⚠️&nbsp; | System distinguish the normal user and administrator, but the functionality of the application doesnt follow those principles and it looks like everyone have the same posibilities. |
| &nbsp;✅&nbsp; | Administrator privileges are limited to ensure GDPR compliance. |

---

| **Result** | **Privacy by Design Principles** |
| :----: | :--- |
| &nbsp;✅&nbsp; | The minimum colection of private data is implemented. |
| &nbsp;✅&nbsp; | Logs are storing only necesary data which is the ip of the user and user token. |
| &nbsp;✅&nbsp; | The forms are structured with data protection in mind. |

---

| **Result** | **Data security** |
| :----: | :--- |
| &nbsp;✅&nbsp; | The CSRF, XSS, and SQL injection protections are implemented, tested using Zap |
| &nbsp;✅&nbsp; | Passwords are securely hashed using bcrypt. |
| &nbsp;⚠️&nbsp; | I have no information if the data backups and recoveries are allowed in the system following the GDPR-compliant. |
| &nbsp;✅&nbsp; | The personal data is stored on my pc located in the EU |

---

| **Result** | **Data anonymization and pseudonymization** |
| :----: | :--- |
| &nbsp;✅/⚠️&nbsp; | The data is anonymized only to the guest uesrs, but there is probably more posibiliteis within app to make anonumization more efective. |
| &nbsp;✅&nbsp; | The user data is protected in some places using pseudonymization techniques, tanks to the introduction of the user_token which can be connected to certain user. |

---

| **Result** | **Data subject rights** |
| :----: | :--- |
| &nbsp;❌&nbsp; | There is no posibility for the user to  download or request all personal data related to them. |
| &nbsp;❌&nbsp; | there is no interface or process for users to request the deletion of their personal data. |
| &nbsp;❌&nbsp; | Users have no posibility to withdraw their consent of data processing. |

---

| **Result** | **Documentation and communication** |
| :----: | :--- |
| &nbsp;✅&nbsp; | There is privacy policy available to users during registration and is easily accessible at the bottom of the page. |
| &nbsp;❌/⚠️&nbsp; | Its unknown for me if the administrators and developers are provided with documented data protection practices and processing activities. |
| &nbsp;❌/⚠️&nbsp; | There is no data breach response process document aviable or the aviability is unknown to me. |

---

**Symbols used:**  
✅ Pass (a note can be added)  
❌ Fail (a note can be added)  
⚠️ Attention (a note can be added)