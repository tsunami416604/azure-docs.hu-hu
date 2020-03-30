---
title: Mennyi ideig tárolja az Azure AD a jelentési adatokat? | Microsoft Docs
description: Ismerje meg, hogy az Azure mennyi ideig tárolja a különböző típusú jelentési adatokat.
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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54636600c208f8f5df9fa2e25460c63dd9f46e85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239546"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Mennyi ideig tárolja az Azure AD a jelentési adatokat?


Ebben a cikkben az Azure Active Directoryban a különböző tevékenységjelentések adatmegőrzési szabályzatait ismerteti. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Mikor kezdi el az Azure AD az adatok gyűjtését?

| Azure AD Kiadás | Gyűjtemény kezdete |
| :--              | :--   |
| Prémium szintű Azure AD P1 <br /> Prémium szintű Azure AD P2 | Amikor feliratkozik egy előfizetésre |
| Azure AD Free| Az Azure Active [Directory panel](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) első megnyitásakor vagy a [jelentéskészítő API-k használatakor](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Mikor érhetők el a tevékenységadatok az Azure Portalon?

- **Azonnal** – Ha már dolgozott a jelentések az Azure Portalon.
- **2 órán belül** – Ha még nem kapcsolta be a jelentést az Azure Portalon.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Milyen hamar láthatom a tevékenységek adatait a prémium licenc megszerzése után?

Ha már rendelkezik tevékenységi adatokkal az ingyenes licencével, akkor azonnal láthatja a frissítést. Ha nem rendelkezik adatokkal, akkor egy vagy két napba telik, amíg az adatok megjelennek a jelentésekben a prémium licencre való frissítés után.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Láthatom a múlt havi adatokat, miután megkaptam az Azure AD prémium licencét?

Ha nemrég váltott prémium verzióra (beleértve a próbaverziót is), kezdetben legfeljebb 7 napig láthatja az adatokat. Amikor az adatok felhalmozódnak, láthatja az elmúlt 30 nap adatait.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Mikor kezdi el az Azure AD a biztonsági jeladatok gyűjtését?  

A biztonsági jelek esetében a gyűjtési folyamat akkor kezdődik, amikor engedélyezi az **Identitásvédelmi központ**használatát. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Mennyi ideig tárolja az Azure AD az adatokat?

**Tevékenységjelentések**    

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--                 | :--                 |
| Naplók             | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezések               | 7 nap        | 30 nap             | 30 nap             |
| Az Azure MFA-használat        | 30 nap       | 30 nap             | 30 nap             |

A naplózási és bejelentkezési tevékenység adatait hosszabb ideig őrizheti meg, mint a fent vázolt alapértelmezett megőrzési időszak, ha az Azure Monitor használatával egy Azure storage-fiókba irányítja azt. További információ: [Archive Azure AD logs to an Azure storage-fiók.](quickstart-azure-monitor-route-logs-to-storage-account.md)

**Biztonsági jelek**

| Jelentés         | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--            | :--           | :--                 | :--                 |
| Veszélyeztetett felhasználók  | 7 nap        | 30 nap             | 90 nap             |
| Kockázatos bejelentkezések | 7 nap        | 30 nap             | 90 nap             |

---
