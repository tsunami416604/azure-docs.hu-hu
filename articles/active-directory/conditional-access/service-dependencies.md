---
title: Feltételes hozzáférésű szolgáltatások függőségei – Azure Active Directory
description: Ismerje meg, hogyan használják a feltételeket az Azure Active Directory feltételes hozzáférés egy szabályzat aktiválásához.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380012"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Mik azok a szolgáltatásfüggőségek az Azure Active Directory feltételes hozzáférésében? 

A feltételes hozzáférési házirendek segítségével hozzáférési követelményeket adhat meg a webhelyekhez és szolgáltatásokhoz. A hozzáférési követelmények közé tartozhat például a többtényezős hitelesítés (MFA) vagy a [felügyelt eszközök](require-managed-devices.md)megkövetelése. 

Amikor közvetlenül elér egy webhelyet vagy szolgáltatást, a kapcsolódó házirendek hatása általában könnyen felmérhető. Ha például olyan házirenddel rendelkezik, amelyhez a SharePoint Online többszintű szolgáltatása konfigurálásra van szükség, a rendszer minden egyes SharePoint-webportálra való bejelentkezéskor kényszeríti az MFA-t. Azonban nem mindig azonnal előre, hogy értékelje a szabályzat hatását, mert vannak olyan felhőalkalmazások, amelyek más felhőalapú alkalmazások tól függ. A Microsoft Teams például hozzáférést biztosíthat a SharePoint Online erőforrásaihoz. Így amikor a jelenlegi forgatókönyvben hozzáfér a Microsoft Teamshez, a SharePoint MFA-szabályzata is vonatkozik Önre.   

## <a name="policy-enforcement"></a>Szabályzatbetartatás 

Ha szolgáltatásfüggőség van konfigurálva, a házirend korai kötésű vagy késői kötésű kényszerítéssel is alkalmazható. 

- **A korai házirend-kényszerítés** azt jelenti, hogy a felhasználónak meg kell felelnie a függő szolgáltatásszabályzatnak a hívó alkalmazás elérése előtt. Például egy felhasználónak teljesítenie kell a SharePoint-házirendet, mielőtt bejelentkezne az MS Teams-be. 
- **A késői házirend-kényszerítés** azután következik be, hogy a felhasználó bejelentkezik a hívó alkalmazásba. Kényszerítés van késleltetett -hoz mikor hívás app requests, egy jogkivonat részére a alsóbb rétegbeli szolgáltatás. Ilyenek például a Planner eléréséhez hozzáférő MS Teams és Office.com sharepoint-hozzáférés. 

Az alábbi ábra az MS Teams szolgáltatás függőségeit mutatja be. A folytonos nyilak azt jelzik, hogy a Planner szaggatott nyílja korai kötése késői kötést jelez. 

![MS Teams szolgáltatás függőségei](./media/service-dependencies/01.png)

Ajánlott eljárásként lehetőség szerint közös szabályzatokat kell beállítania a kapcsolódó alkalmazásokban és szolgáltatásokban. Az egységes biztonsági helyzet a legjobb felhasználói élményt nyújtja. Ha például az Exchange Online, a SharePoint Online, a Microsoft Teams és a Skype vállalati verzió közös házirendje jelentősen leállítja a nem várt utasításokat, amelyek az alsóbb rétegbeli szolgáltatásokra alkalmazott különböző házirendekből erednek. 

Az alábbi táblázat további szolgáltatásfüggőségeket sorol fel, ahol az ügyfélalkalmazásoknak meg kell felelniük  

| Ügyfélalkalmazások         | Alsóbb rétegbeli szolgáltatás                          | Végrehajtási |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portál és API) | Korai kötött |
| Microsoft Tanterem | Exchange                                    | Korai kötött |
|                     | SharePoint                                  | Korai kötött |
| Microsoft Teams     | Exchange                                    | Korai kötött |
|                     | MS tervező                                  | Kései kötésű  |
|                     | SharePoint                                  | Korai kötött |
|                     | Skype Vállalati online verzió                   | Korai kötött |
| Office-portál       | Exchange                                    | Kései kötésű  |
|                     | SharePoint                                  | Kései kötésű  |
| Outlook-csoportok      | Exchange                                    | Korai kötött |
|                     | SharePoint                                  | Korai kötött |
| PowerApps           | Microsoft Azure Management (portál és API) | Korai kötött |
|                     | Microsoft Azure Active Directory              | Korai kötött |
| Project             | Dynamics CRM                                | Korai kötött |
| Skype Vállalati verzió  | Exchange                                    | Korai kötött |
| Visual Studio       | Microsoft Azure Management (portál és API) | Korai kötött |
| Microsoft Forms     | Exchange                                    | Korai kötött |
|                     | SharePoint                                  | Korai kötött |
| Microsoft To-Do     | Exchange                                    | Korai kötött |

## <a name="next-steps"></a>További lépések

A feltételes hozzáférés környezetben való megvalósításáról a [Feltételes hozzáférés telepítésének megtervezése az Azure Active Directoryban](plan-conditional-access.md)című témakörben olvashat.
