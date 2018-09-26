---
title: Az Azure Active Directory-jelentések adatmegőrzési szabályzata |} A Microsoft Docs
description: Adatmegőrzési házirendek, a jelentés adatai az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 68028fd1ba116251860e5c370e9e9ce61fd314bb
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47107007"
---
# <a name="azure-active-directory-report-retention-policies"></a>Az Azure Active Directory-jelentés adatmegőrzési szabályai


A cikk ismerteti a különböző tevékenységre vonatkozó jelentések az Azure Active Directoryban az adatmegőrzési együtt a leggyakoribb kérdésekre kaphat választ. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>K: Hogyan szerezhetem be a tevékenység adatgyűjtés lépései?

**VÁLASZ:**

| Az Azure AD-kiadástól | Gyűjtemény indítása |
| :--              | :--   |
| Prémium szintű Azure AD P1 <br /> Prémium szintű Azure AD P2 | Amikor regisztrál egy előfizetést |
| Azure AD Free | Az első megnyitásakor a [Azure Active Directory panel](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) vagy használja a [reporting API-k](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>K: mikor érhető el az adatok az Azure Portalon?

**VÁLASZ:**

- **Azonnal** – Ha már dolgozott a jelentések az Azure Portalon.
- **2 órán belül** – Ha nem kapcsolta a jelentéskészítés az Azure Portalon.

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>K: Hogyan szerezhetem be a biztonsági jelek elindult a gyűjtemény?  

**V:** a biztonsági jelek, az adatgyűjtési folyamat akkor kezdődik, amikor Ön vehetnek részt az Identity Protection Centerben használja. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>K: mennyi ideig van az összegyűjtött adatok tárolva?

**VÁLASZ:**

**Tevékenységjelentések**    

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--                 | :--                 |
| Címtárnaplózás        | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezési tevékenységek       | –           | 30 nap             | 30 nap             |
| Az Azure MFA-használat        | 30 nap       | 30 nap             | 30 nap             |

**Biztonsági jelek**

| Jelentés         | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--            | :--           | :--                 | :--                 |
| Veszélyeztetett felhasználók  | 7 nap        | 30 nap             | 90 nap             |
| Kockázatos bejelentkezések | 7 nap        | 30 nap             | 90 nap             |

---
