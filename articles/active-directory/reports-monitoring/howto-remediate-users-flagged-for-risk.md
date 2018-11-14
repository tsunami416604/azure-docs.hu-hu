---
title: Kockázatosként megjelölt felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon | Microsoft Docs
description: Ismerje meg az Azure Active Directory portál kockázatosként megjelölt felhasználókról szóló biztonsági jelentését
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c43497a69b3188b4bc38e91b6ff9b1cc8b6bd21a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624148"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Kockázatosként megjelölt felhasználók szervizelése az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) biztonsági jelentéseivel a környezetben mekkora valószínűséggel sérült egyes felhasználói fiókok is mérőműszer. Kockázatosként megjelölt felhasználók egy mutató a egy felhasználói fiókot, előfordulhat, hogy sérült a biztonsága.

A Microsoft elkötelezte magát az Ön környezeteinek védelmére. Ezen kötelezettségvállalás részeként a Microsoft folyamatosan monitorozza a szokatlan vagy az ismert támadási mintákat követő tevékenységeket. 

Ha, előfordulhat, hogy egyes felhasználói fiókok jogosulatlan hozzáférés szokatlan tevékenységeket észlel, kap értesítéseket, lehetővé téve a műveletet. Ez nem jelenti azt, hogy Microsoft saját rendszereinek biztonsága sérült.

## <a name="access-the-users-flagged-for-risk-report"></a>A kockázatosként megjelölt felhasználókról szóló jelentés elérése

A kockázatosként megjelölt felhasználók áttekintheti a [veszélyeztetett felhasználókról jelentés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk) az Azure Portalon. Ha nem rendelkezik Azure ad-ben, feliratkozhat az ingyenes [ https://aka.ms/AccessAAD ](https://aka.ms/AccessAAD). 

Megjelölt felhasználókról szóló jelentés a felhasználóktól minden felhasználó számára a következőket teheti:

- Ideiglenes jelszó létrehozása
- Biztonságos jelszóváltoztatás megkövetelése a felhasználótól a következő bejelentkezéskor
- A felhasználó kockázatos voltának figyelmen kívül hagyása szervizelési művelet nélkül.

További információkért lásd: [megjelölt felhasználókról szóló biztonsági jelentés az](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Azure AD-előfizetés Office 365-ügyfelek számára

Használhatja az Office 365 hitelesítő adatok eléréséhez a **Azure Admin Centert**. Miután aktiválta Azure AD-hozzáférését, a rendszer átirányítja az Azure AD portáljára. Az alapszintű előfizetési szinten a jelentésekben biztosított adatok mennyisége korlátozott. Az Azure Premium-előfizetők további adatokat és elemzéseket érhetnek el.

Hozzáférés a **kockázatosként megjelölt felhasználók** jelentések az Office 365 felügyeleti központjában:

1.  Válassza a bal oldali navigációs menüben **felügyeleti központok**. 
2.  Válassza ki **Azure ad-ben**.
3.  Jelentkezzen be az **Azure Active Directory felügyeleti központba**.
4.  Ha egy szalagcím jelenik meg arról, hogy az oldal tetején lévő **tekintse meg az új portál**, jelölje ki a kapcsolatot.
4.  A bal oldali navigációs menü, válassza ki a **Azure Active Directory**. 
5.  A navigációs panelen válassza ki **kockázatosként megjelölt felhasználók** származó a **biztonsági** szakaszban.

## <a name="remediation-actions"></a>Szervizelési műveletek

Végezze el a következő műveleteket az érintett fiókok javításához és a környezet biztonságossá tételéhez:

1.  [Ellenőrizze a megfelelő információkkal](http://aka.ms/MFAValid) a multi-factor authentication és az önkiszolgáló jelszó alaphelyzetbe állítása. 
2.  [Többtényezős hitelesítés engedélyezése](http://aka.ms/MFAuth) az összes felhasználó számára. 
3.  Ezzel [szervizelési parancsfájl](http://aka.ms/remediate) minden érintett fiók automatikusan hajtsa végre az alábbi lépéseket: 

    a. A fiók biztonságát, és az aktív munkamenetek kill jelszó alaphelyzetbe állítása.

    b. A postaláda meghatalmazottjainak eltávolítása.

    c. A külső tartományokra irányuló levéltovábbítási szabályok letiltása.

    d. A globális levéltovábbítási tulajdonság eltávolítása a postaládára vonatkozóan.

    e. MFA engedélyezése a felhasználó fiókján.

    f. Magas jelszóösszetettség beállítása a fiókhoz.

    g. A postaláda-naplózás engedélyezése.

    h. A rendszergazda számára, tekintse át az auditnapló előállításához.

4. Vizsgálja meg az Office 365-bérlőt és a többi informatikai infrastruktúrát, beleértve az összes bérlőbeállítást, felhasználói fiókokat és a felhasználónkénti konfigurációs beállításokat, hogy nem hajtottak-e rajtuk végre módosításokat. Keressen perzisztenciára utaló jeleket, valamint ellenőrizze, hogy egy esetleges támadónak a behatolási kísérlet során sikerült-e hozzáférnie a VPN-hitelesítőadatokhoz vagy más vállalati erőforrásokhoz. 

5.  A vizsgálat részeként vegye figyelembe, hogy értesítse-e kormányzati hatóságokat, beleértve a bűnüldöző hatóságokat.

Ezenkívül el a következőket kell elvégeznie:

- Olvassa el és megvalósításának [útmutató azoknak a szokatlan tevékenységek](http://aka.ms/fixaccount). 
- [Engedélyezze a naplózási folyamatot](http://aka.ms/improvesecurity) , amelyek segítségével elemezheti a tevékenységeket, a bérlőben. Ha elkészült, a naplótároló elkezd a vizsgálati naplók feltöltése. Ezen a ponton is használhatja a [biztonsági és megfelelőségi központ keresés és vizsgálat erőforrás](http://aka.ms/sccsearch). 
- Ezzel [postaláda-naplózás engedélyezéséhez parancsfájl](http://aka.ms/mailboxaudit1) az összes fiók. 
- Tekintse át az összes postafiók engedélyek delegálásával és levelek továbbításával kapcsolatos szabályait. Ezzel a [PowerShell-szkripttel](http://aka.ms/delegateforwardrules) végezheti el ezt a feladatot. 

## <a name="next-steps"></a>További lépések

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [Kockázatosként megjelölt felhasználók](concept-user-at-risk.md)