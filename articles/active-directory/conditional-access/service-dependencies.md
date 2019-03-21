---
title: Mik a szolgáltatásfüggőségek az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Ismerje meg, hogyan feltételek használatával az Azure Active Directory feltételes hozzáférési szabályzat aktiválása.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e15dd12da2fbd03d342b4eb2a32f34db958b6ed
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259468"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Mik a szolgáltatásfüggőségek az Azure Active Directory feltételes hozzáférés? 


Feltételes hozzáférési szabályzatok megadhatja a hozzáférési követelmények webhelyek és-szolgáltatások. A hozzáférési követelmények lehet például a többtényezős hitelesítés (MFA) megkövetelésével vagy [felügyelt eszközök](require-managed-devices.md). 


Amikor közvetlenül hozzáférni egy webhelyre vagy szolgáltatásba, kapcsolódó szabályzat hatásának felmérése általában könnyebbé vált. Például ha egy szabályzatot, amely többtényezős Hitelesítést követel meg a SharePoint online-hoz konfigurált, MFA kényszerítése az egyes jelentkezzen be a SharePoint webes portálján. Ez viszont nem mindig a szabályzat hatásának értékelése, mert van más függőségekkel rendelkező felhőalapú alkalmazások lépünk. Ha például Microsoft Teams kihasználja a SharePoint online. Ezért ha az aktuális helyzethez éri el a Microsoft Teams, Önre is SharePoint többtényezős hitelesítési szabályzat.   


## <a name="policy-enforcement"></a>Szabályzatbetartatás 

Ha egy szolgáltatásfüggőség konfigurálva van, a szabályzat alkalmazható korai kötésű vagy késői kötéssel használatával. 

**Korai kötésű házirend betartatása** azt jelenti, hogy a felhasználó a hívó alkalmazás megnyitása előtt meg kell felelniük a függő szolgáltatások házirend. Például az a felhasználó meg kell felelniük az SharePoint-szabályzat az MS Teams szolgáltatásba aláírása előtt. 

**Késői kötéssel házirend betartatása** után a felhasználó jelentkezik be a hívó alkalmazás kerül sor. Kényszerítési van halasztva alkalmazás kérések, egy tokent az alárendelt szolgáltatás hívása során. Ilyenek például az MS Teams Planner és a SharePoint elérése Office.com eléréséhez. 

Az alábbi ábra szemlélteti az MS Teams függőségei. Szilárd nyilak jelzik korai kötésű kényszerítési szaggatott nyílra, a Planner azt jelzi, hogy késői kötéssel kényszerítési. 



![Az MS Teams függőségei](./media/service-dependencies/01.png)



  

Ajánlott eljárásként kell beállítani, gyakran használt szabályzatok különböző kapcsolódó alkalmazásokat és szolgáltatásokat, amikor csak lehetséges. Egy egységes biztonsági állapotáról kellene biztosítja a legjobb felhasználói élmény. Például általános házirend beállítása között az Exchange Online, SharePoint online-hoz, az MS Teams és a Skype vállalati verzió jelentősen csökkenti váratlan utasításokat, amelyet a különböző szabályzatok alárendelt szolgáltatásokkal. 

Az alábbi táblázat felsorolja a további függőségei, ahol meg kell felelniük az ügyfélalkalmazások  

| Ügyfélalkalmazások         | Alsóbb rétegbeli szolgáltatás                          | Kényszerítési |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | A Microsoft Azure Management (portál és API-t) | Korai kötésű |
| A Microsoft tantermet | Exchange                                    | Korai kötésű |
|                     | SharePoint                                  | Korai kötésű  |
| Microsoft Teams     | Exchange                                    | Korai kötésű |
|                     | MS Planner                                  | Késői kötéssel  |
|                     | SharePoint                                  | Korai kötésű |
|                     | Skype Vállalati online verzió                   | Korai kötésű |
| Office-portálon       | Exchange                                    | Késői kötéssel  |
|                     | SharePoint                                  | Késői kötéssel  |
| Outlook-csoportok      | Exchange                                    | Korai kötésű |
|                     | SharePoint                                  | Korai kötésű |
| PowerApps           | A Microsoft Azure Management (portál és API-t) | Korai kötésű |
|                     | Windows Azure Active Directory              | Korai kötésű |
| Project             | Dynamics CRM                                | Korai kötésű |
| Skype Vállalati verzió  | Exchange                                    | Korai kötésű |
| Visual Studio       | A Microsoft Azure Management (portál és API-t) | Korai kötésű |



## <a name="next-steps"></a>További lépések

Feltételes hozzáférés alkalmazása a környezetben, lásd: [az Azure Active Directoryban a feltételes hozzáférés üzembe helyezésének megtervezése](plan-conditional-access.md).
