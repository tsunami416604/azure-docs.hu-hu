---
title: Az Azure Active Directory-jelentés adatmegőrzési |} Microsoft Docs
description: A jelentés adatokat az Azure Active Directoryban adatmegőrzési
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: fac160d2d5916097afcbb9825bb82a52789e4a89
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589195"
---
# <a name="azure-active-directory-report-retention-policies"></a>Az Azure Active Directory-jelentés adatmegőrzési szabályai


Ez a témakör a kérdésekre adott válaszok a leggyakrabban használt a különböző tevékenység jelentések az Azure Active Directoryban az adatok megőrzése mellett. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>K: hogyan férhetnek indítása tevékenység adatok gyűjtését?

**V:**

| Az Azure AD Edition | Gyűjtemény indítása |
| :--              | :--   |
| Prémium szintű Azure AD P1 <br /> Prémium szintű Azure AD P2 | Ha a regisztráláskor az előfizetéshez |
| Azure AD Free | Az első megnyitásakor a [Azure Active Directory panel](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) , vagy használja a [reporting API-k](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>K: mikor érhető el a tevékenységek adatai az Azure-portálon?

**V:**

- **Azonnal** – Ha már dolgozott jelentések segítségével az Azure-portálon
- **2 órán belül** – Ha még nem bekapcsolta a jelentéskészítés az Azure-portálon

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>K: hogyan férhetnek a gyűjtemény biztonsági jelek lépések?  

**V:** a biztonsági jelek, az adatgyűjtési folyamat kezdődik, amikor Ön részt a Identity Protection-központtól használja. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>K: mennyi ideig az összegyűjtött adatok tárolják?

**V:**

**Tevékenységjelentések**    

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--                 | :--                 |
| Címtárnaplózás        | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezési tevékenységek       | –           | 30 nap             | 30 nap             |
| Az Azure MFA használata        | 30 nap       | 30 nap             | 30 nap             |

**Biztonsági jelek**

| Jelentés         | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--            | :--           | :--                 | :--                 |
| Érintett felhasználók  | 7 nap        | 30 nap             | 90 nap             |
| Kockázatos bejelentkezések | 7 nap        | 30 nap             | 90 nap             |

---
