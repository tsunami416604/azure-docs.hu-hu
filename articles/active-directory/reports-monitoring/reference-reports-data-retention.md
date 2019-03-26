---
title: Az Azure Active Directory-jelentések adatmegőrzési szabályzata |} A Microsoft Docs
description: Adatmegőrzési házirendek, a jelentés adatai az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 126eee1664d810850f63e702abe78b241649d665
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438818"
---
# <a name="azure-active-directory-report-retention-policies"></a>Az Azure Active Directory-jelentés adatmegőrzési szabályai

Ebben a cikkben megismerkedhet a különböző tevékenységre vonatkozó jelentések az Azure Active Directoryban adatmegőrzési házirendek. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Ha nem az Azure AD megkezdi az adatok gyűjtését?

| Azure AD Edition | Gyűjtemény indítása |
| :--              | :--   |
| Prémium szintű Azure AD P1 <br /> Prémium szintű Azure AD P2 | Amikor regisztrál egy előfizetést |
| Azure AD Free <br /> Azure AD Basic | Az első megnyitásakor a [Azure Active Directory panel](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) vagy használja a [reporting API-k](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Amikor a tevékenységek adatai érhető el az Azure Portalon?

- **Azonnal** – Ha már dolgozott a jelentések az Azure Portalon.
- **2 órán belül** – Ha nem kapcsolta a jelentéskészítés az Azure Portalon.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hogy mikor is tevékenységek adatainak megtekintéséhez prémium licencre első után?

Ha már rendelkezik az ingyenes licenc tevékenységek adatokat, majd tekintheti meg azonnal a frissítés. Ha nem rendelkezik semmilyen adatot, majd vesz igénybe egy vagy két napot megjelenjen a jelentésekben, prémium licencre történő frissítés után az adatok.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Ha prémium szintű Azure AD-licenccel is láthatók múlt havi adatokat?

Ha nemrég egy prémium szintű verzió (beleértve a próbaverzió) váltott, látható adatok mentése és 7 nap kezdetben. Amikor gyűlnek az adatok, láthatja a adatok az elmúlt 30 nap során.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Ha nem az Azure AD adatgyűjtés megkezdéséhez biztonsági jel?  

A biztonsági jelek, az adatgyűjtési folyamat akkor kezdődik, amikor, jóváhagyja a használja a **Identity Protection Centerben**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Mennyi ideig Azure ad-ben az adatok tárolása?

**Tevékenységjelentések**    

| Jelentés                 | Azure AD Free | Azure AD Basic | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Címtárnaplózás        | 7 nap        |  7 nap        | 30 nap             | 30 nap             |
| Bejelentkezési tevékenységek       | –           |  –           | 30 nap             | 30 nap             |
| Azure MFA Usage        | 30 nap       |  30 nap       | 30 nap             | 30 nap             |

A naplózási és a bejelentkezési tevékenységre vonatkozó adatok által átirányítására egy Azure storage-fiókot az Azure Monitor használatával a fent vázolt alapértelmezett megőrzési idejénél hosszabb őrizheti meg. További információkért lásd: [archiválása az Azure AD-naplók az Azure storage-fiókba](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Biztonsági jelek**

| Jelentés         | Azure AD Free | Azure AD Basic | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Veszélyeztetett felhasználók  | 7 nap        | 7 nap         | 30 nap             | 90 nap             |
| Kockázatos bejelentkezések | 7 nap        | 7 nap         |  30 nap            | 90 nap             |

---
