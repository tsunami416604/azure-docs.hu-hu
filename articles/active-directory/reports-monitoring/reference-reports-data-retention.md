---
title: Mennyi ideig tart az Azure AD Store jelentéskészítési adatai? | Microsoft Docs
description: Megtudhatja, hogy az Azure milyen hosszú ideig tárolja a különböző jelentési adattípusokat.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80239546"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Mennyi ideig tart az Azure AD Store jelentéskészítési adatai?


Ebből a cikkből megtudhatja, hogyan használhatók az adatmegőrzési szabályzatok a Azure Active Directory különböző tevékenységi jelentéseihez. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Mikor kezdi az Azure AD az adatok gyűjtését?

| Azure AD-kiadás | Gyűjtemény kezdete |
| :--              | :--   |
| Prémium szintű Azure AD P1 <br /> Prémium szintű Azure AD P2 | Ha előfizetést regisztrál |
| Azure AD Free| Amikor először nyitja meg a [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) panelt, vagy a [jelentéskészítő API-kat](https://aka.ms/aadreports) használja  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Mikor érhetők el a tevékenységek a Azure Portal?

- **Azonnal** – ha már dolgozik a Azure Portal jelentéseivel.
- **2 órán belül** – ha nem kapcsolta be a jelentéskészítést a Azure Portalban.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Milyen hamar láthatom a tevékenységek információit a prémium szintű licenc beszerzése után?

Ha már rendelkezik a tevékenységek adataival az ingyenes licenccel, akkor azonnal megtekintheti a frissítést. Ha nem rendelkezik az adataival, a rendszer egy vagy két napot is igénybe vesz, hogy az adatai megjelenjenek a jelentésekben a prémium szintű licencre való frissítés után.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>A prémium szintű Azure AD-licenc beszerzése után láthatom a múlt havi adatszolgáltatást?

Ha nemrég áttért a prémium verzióra (beleértve a próbaverziót is), a kezdeti időszakban akár 7 napig is megtekintheti az adatvesztést. Az adatgyűjtés során az elmúlt 30 napban láthatja az adatmennyiséget.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Mikor kezdi meg az Azure AD a biztonsági szignálok adatgyűjtését?  

A biztonsági jelek esetében a begyűjtési folyamat elindul, amikor bekapcsolja az **Identity Protection centert**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Mennyi ideig tárolja az Azure AD az adattárolást?

**Tevékenységjelentések**    

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--                 | :--                 |
| Naplók             | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezések               | 7 nap        | 30 nap             | 30 nap             |
| Azure MFA-használat        | 30 nap       | 30 nap             | 30 nap             |

A naplózási és a bejelentkezési tevékenységek adatai hosszabbak maradnak, mint az alapértelmezett megőrzési időtartam, amelyet a fentiekben ismertetünk, ha Azure Monitor használatával irányítja át az Azure Storage-fiókba. További információ: [Azure ad-naplók archiválása Azure Storage-fiókba](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Biztonsági jelek**

| Jelentés         | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--            | :--           | :--                 | :--                 |
| Veszélyeztetett felhasználók  | 7 nap        | 30 nap             | 90 nap             |
| Kockázatos bejelentkezések | 7 nap        | 30 nap             | 90 nap             |

---
