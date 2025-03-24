# Authorization testing

***Version: Phase 3 of the 'Booking System'***

## Table

| **Page / Feature** | **Guest** | **Reserver** | **Administrator** |
|:----|:----:|:----:|:----:|
| `/` (index)                | | | |
| └─ View resource form      | ✅ | ✅ | ✅ |
| └─ See login and register options     | ✅ | ❌ | ❌ |
| └─ See and access extra options     | ❌ | ✅ | ✅ |
| └─ Access Edit resources      | ❌ | ❌ | ✅ |
| └─ Access Change reservations      | ❌ | ❌/✅*2 | ✅ |
| `/login`                   | | | |
| └─ Can access panel      | ✅ | ✅⚠️*3 | ✅⚠️*3 |
| └─ Can login      | ✅ | ✅⚠️*3 | ✅⚠️*3 |
| `/register`                | | | |
| └─ Can access panel      | ✅ | ✅⚠️*3 | ✅⚠️*3 |
| └─ Can register      | ✅ | ✅⚠️*3 | ✅⚠️*3 |
| `/resources`               | | | |
| └─ Can access panel      | ✅⚠️*4 | ✅ | ✅ |
| └─ Can add resources      | ✅⚠️*4| ✅ | ✅ |
| └─ Can edit resources      | ❌ | ✅⚠️*5 | ✅ |
| `/reservation`             | | | |
| └─ Can access panel      | ❌ | ✅ | ✅ |
| └─ Can make reservation      | ❌ | ✅ | ✅ |
| └─ Can edit reservation      | ❌ | ✅⚠️*5 | ✅ |

**Symbols legend:**  
✅ Pass (a note can be added)  
❌ Fail (a note can be added)  
⚠️ Attention (a note can be added)

**Notes:**  
1. Loged in users with reserver role can access only edition of reservation of their own reservations, which is good in the context of the system expectations.
2. The best approach to the `/register` and `/login` endpoints while been already loged in, is redirection to the main/profile page. Current approach allows for loged in users to access those endpoints. 
3. This is critical issue, the guest can acces the resources endpoint and add new resources. This makes the system vulnerable, the `/resources` endpoint should check authorization of the requests.
4. The edition or reservations should be only possible for own reservations when it comes to the reserver role. But by been able to acces this pannel we can jump around other reservation accesing the ?=idx (where x is the number 1,2....,n representing index of reservation), allowing the reserver to access edition of reservation of other users.

## Application specifications analysis

1. The system is accessed via a web browser. ✅
- ✅ The system can be accessed via web browser.
2. Users can register and, after registration, log in to the system. ✅
- ✅ System allows for process of registration and login management.
3. A registered and logged-in user acts as either a resource reserver or an administrator. ✅/❌
- ✅ The system distinguish between reserver and administrator role, but ❌ the endpoints `/resources` and `/reservation` are not validating access allowing for the reserver to edit those information (for more information look at `note 5`). Same can be said about `/resources` alone where quests can access this panel and add new resources (for more information look at `note 4`).
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

- ZAP report version 1: xx