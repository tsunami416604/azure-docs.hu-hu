---
title: Az Azure AD Power BI-tartalomcsomag telepítése | Microsoft Docs
description: Az Azure AD Power BI-tartalomcsomag telepítésének módja
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f088c8b49fa6485a21b630738149078b7ff45b7d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988106"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Gyors útmutató: Azure Active Directory Power BI csomag telepítése

|  |
|--|
|Az Azure AD Power BI-tartalomcsomag jelenleg az Azure AD Graph API-jaival kéri le az adatokat az Azure AD-bérlőről. Ezért a tartalomcsomagban elérhető adatok, valamint a [jelentéskészítési Microsoft Graph API-k](concept-reporting-api.md) által lekért adatok között lehet némi eltérés. |
|  |

A Azure Active Directory (Azure AD) Power BI tartalomkezelő szolgáltatása lehetővé teszi a jelentéskészítési adatok megjelenítését a környezetből. Az előre összeállított tartalomcsomag letöltésével jelentést készíthet a címtárban zajló tevékenységekről a Power BI gazdag vizualizációs képességeivel. Emellett létrehozhat saját irányítópultot is, és könnyedén megoszthatja szervezete bármely tagjával. 

Ebből a rövid útmutatóból elsajátíthatja a tartalomcsomag telepítését.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* Egy Power BI-fiók. Ez megegyezik az O365- vagy Azure AD-fiókjával. 
* Az Azure AD-bérlő azonosítója. Ez a címtár **címtár-azonosítója**, amely az Azure Portal [tulajdonságok oldalán](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) található.
* Egy Azure AD Prémium- (P1/P2) licenc. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört.

## <a name="install-azure-ad-power-bi-content-pack"></a>Az Azure AD Power BI-tartalomcsomag telepítése 

1. Jelentkezzen be a [Power BI-ba](https://app.powerbi.com/groups/me/getdata/services) a Power BI-fiókjával. Ez megegyezik az O365- vagy Azure AD-fiókjával.

2. Keressen rá az **Azure Active Directory-tevékenységnaplók** kifejezésre az **Alkalmazások** lapon, és válassza a **Letöltés** lehetőséget. 

   ![Azure Active Directory Power BI-tartalomcsomag](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. A felugró ablakban írja be Azure AD-bérlőazonosítóját, a lekérdezett napok számaként adja meg a **7** értéket, majd válassza a **Tovább** lehetőséget.
    
   ![Azure Active Directory Power BI-tartalomcsomag](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Miután létrejött az Azure Active Directory-tevékenységnaplók irányítópultja, válassza ki.

   ![Azure Active Directory Power BI-tartalomcsomag](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>További lépések

* [A Power BI-tartalomcsomag használata](howto-power-bi-content-pack.md).
* [A tartalomcsomag hibáinak elhárítása](troubleshoot-content-pack.md).
