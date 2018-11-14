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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 601169cc62a99438f661adc06ab166b545606edb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624590"
---
# <a name="azure-active-directory-report-retention-policies"></a>Az Azure Active Directory-jelentés adatmegőrzési szabályai

Ebben a cikkben megismerkedhet a különböző tevékenységre vonatkozó jelentések az Azure Active Directoryban adatmegőrzési házirendek. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Ha nem az Azure AD megkezdi az adatok gyűjtését?

| Az Azure AD-kiadástól | Gyűjtemény indítása |
| :--              | :--   |
| Prémium szintű Azure AD P1 <br /> Prémium szintű Azure AD P2 | Amikor regisztrál egy előfizetést |
| Azure AD Free | Az első megnyitásakor a [Azure Active Directory panel](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) vagy használja a [reporting API-k](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Amikor a tevékenységek adatai érhető el az Azure Portalon?

- **Azonnal** – Ha már dolgozott a jelentések az Azure Portalon.
- **2 órán belül** – Ha nem kapcsolta a jelentéskészítés az Azure Portalon.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Ha nem az Azure AD adatgyűjtés megkezdéséhez biztonsági jel?  

A biztonsági jelek, az adatgyűjtési folyamat akkor kezdődik, amikor, jóváhagyja a használja a **Identity Protection Centerben**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Mennyi ideig Azure ad-ben az adatok tárolása?

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
