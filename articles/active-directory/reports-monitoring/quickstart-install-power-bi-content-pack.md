---
title: Az Azure AD Power BI-tartalomcsomag telepítése | Microsoft Docs
description: Az Azure AD Power BI-tartalomcsomag telepítésének módja
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: cd5da2a3c5fa3d9cd67715493eb649926badb694
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622534"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Rövid útmutató: Az Azure Active Directory Power BI-tartalomcsomag telepítése

|  |
|--|
|Az Azure AD Power BI-tartalomcsomag jelenleg az Azure AD Graph API-jaival kéri le az adatokat az Azure AD-bérlőről. Ezért a tartalomcsomagban elérhető adatok, valamint a [jelentéskészítési Microsoft Graph API-k](concept-reporting-api.md) által lekért adatok között lehet némi eltérés. |
|  |

Az Azure Active Directory (Azure AD) a Power BI-tartalomcsomag teszi lehetővé a környezet a jelentési adatok vizualizálásához. Az előre összeállított tartalomcsomag letöltésével jelentést készíthet a címtárban zajló tevékenységekről a Power BI gazdag vizualizációs képességeivel. Emellett létrehozhat saját irányítópultot is, és könnyedén megoszthatja szervezete bármely tagjával. 

Ebből a rövid útmutatóból elsajátíthatja a tartalomcsomag telepítését.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* Egy Power BI-fiók. Ez megegyezik az O365- vagy Azure AD-fiókjával. 
* Az Azure AD-bérlő azonosítója. Ez a címtár **címtár-azonosítója**, amely az Azure Portal [tulajdonságok oldalán](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) található.
* Egy Azure AD Prémium- (P1/P2) licenc. 

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
