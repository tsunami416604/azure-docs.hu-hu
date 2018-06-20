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
ms.component: compliance-reports
ms.date: 05/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 60a77a8bee54c0582cbc9c88b9d517820a2760e0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221980"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Kockázatosként megjelölt felhasználók szervizelése az Azure Active Directory portálon

Az Azure Active Directory (Azure AD) biztonsági jelentéseivel megtudhatja, hogy a környezetben mekkora valószínűséggel sérült egyes felhasználói fiókok biztonsága. A [kockázatosként megjelölt felhasználó](active-directory-identityprotection.md#users-flagged-for-risk) egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Microsoft elkötelezte magát az Ön környezeteinek védelmére. Ezen kötelezettségvállalás részeként a Microsoft folyamatosan monitorozza a szokatlan vagy az ismert támadási mintákat követő tevékenységeket. 


Ha a rendszer olyan szokatlan tevékenységeket észlel, amelyek például az egyes felhasználói fiókok jogosulatlan hozzáférésére utalnak, értesítéseket küld Önnek, hogy megtehesse a szükséges lépéseket. Az értesítés nem jelenti azt, hogy a Microsoft saját rendszereinek biztonsága sérült volna.
 

## <a name="access-the-users-flagged-for-risk-report"></a>A kockázatosként megjelölt felhasználókról szóló jelentés elérése

A kockázatosként megjelölt felhasználókat a vonatkozó [jelentésben](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk) tekintheti meg az Azure Active Directoryban. Ha nem Azure AD-előfizető, ingyen végrehajthatja az egyszeri előfizetési folyamatot a [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) címen. Ezen a jelentésen különböző műveletek hajthatók végre. Például:

- Ideiglenes jelszó létrehozása
- Biztonságos jelszóváltoztatás megkövetelése a felhasználótól a következő bejelentkezéskor
- A felhasználó kockázatos voltának figyelmen kívül hagyása szervizelési művelet nélkül.

További információkat a [Kockázatosként megjelölt felhasználókról szóló biztonsági jelentés az Azure Active Directory portálon](active-directory-reporting-security-user-at-risk.md) című szakaszban talál.

### <a name="azure-ad-subscription-for-office-365-customers"></a>Azure AD-előfizetés Office 365-ügyfelek számára

Miután végzett, az Office 365 hitelesítő adataival érheti el az Azure Admin Centert. Miután aktiválta Azure AD-hozzáférését, a rendszer átirányítja az Azure AD portáljára. Az alapszintű előfizetési szinten a jelentésekben biztosított adatok mennyisége korlátozott. Az Azure Premium-előfizetők további adatokat és elemzéseket érhetnek el.


**A kockázatosként megjelölt felhasználókról szóló jelentések elérése az Office 365 felügyeleti központjában:**

1.  A bal oldali navigációs menüben kattintson a **Felügyeleti központok** elemre. 
2.  Kattintson az **Azure AD** elemre.
3.  Jelentkezzen be az **Azure Active Directory felügyeleti központba**.
4.  Ha egy szalagcím jelenik meg az oldal tetején, amely felkéri **az új portál megtekintésére**, kattintson a hivatkozásra.
4.  A bal oldali navigációs menüben kattintson az **Azure Active Directory** elemre. 
5.  A navigációs ablak **Biztonság** területén kattintson a **Kockázatosként megjelölt felhasználók** elemre.

Tekintse át az itt megjelenő információkat. Vissza kell állítania az összes itt szereplő fiók jelszavát. 

## <a name="remediation-actions"></a>Szervizelési műveletek

Végezze el a következő műveleteket az érintett fiókok javításához és a környezet biztonságossá tételéhez:

1.  [Ellenőrizze](http://aka.ms/MFAValid), hogy helyesek-e a többtényezős hitelesítés és az önkiszolgáló jelszó-visszaállítás adatai. 
2.  [Engedélyezze](http://aka.ms/MFAuth) a többtényezős hitelesítést minden felhasználó számára. 
3.  Ezzel a [szervizelési szkripttel](http://aka.ms/remediate) minden érintett fiók esetében automatikusan elvégezheti a következő lépéseket: 

    a. A jelszó visszaállítása a fiók biztonságossá tételéhez és az aktív munkamenetek megszakításához.

    b. A postaláda meghatalmazottjainak eltávolítása.

    c. A külső tartományokra irányuló levéltovábbítási szabályok letiltása.

    d. A globális levéltovábbítási tulajdonság eltávolítása a postaládára vonatkozóan.

    e. MFA engedélyezése a felhasználó fiókján.

    f. Magas jelszóösszetettség beállítása a fiókhoz.

    g. A postaláda-naplózás engedélyezése.

    h. Naplók létrehozása a rendszergazda általi áttekintésre.

4. Vizsgálja meg az Office 365-bérlőt és a többi informatikai infrastruktúrát, beleértve az összes bérlőbeállítást, felhasználói fiókokat és a felhasználónkénti konfigurációs beállításokat, hogy nem hajtottak-e rajtuk végre módosításokat. Keressen perzisztenciára utaló jeleket, valamint ellenőrizze, hogy egy esetleges támadónak a behatolási kísérlet során sikerült-e hozzáférnie a VPN-hitelesítőadatokhoz vagy más vállalati erőforrásokhoz. 

5.  A vizsgálat során fontolja meg, hogy értesítenie kell-e a kormányzati hatóságokat, beleértve a rendészeti szerveket is.

Ezenkívül el a következőket kell elvégeznie:

- Olvassa el és valósítsa meg a szokatlan tevékenységekkel foglalkozó ezen [útmutatóban](http://aka.ms/fixaccount) foglaltakat. 
- [Engedélyezze a naplózási folyamatot](http://aka.ms/improvesecurity), hogy könnyebben elemezhesse a bérlőkkel kapcsolatos tevékenységeket. Ha végzett, a naplótároló elkezd feltöltődni az összes tevékenységnaplóval. Ekkor kihasználhatja a [Biztonsági és megfelelőségi központ Keresés és Vizsgálat](http://aka.ms/sccsearch) eszközeit is. 
- Ezzel a [szkripttel](http://aka.ms/mailboxaudit1) engedélyezheti az összes fiók postafiókjának naplózását. 
- Tekintse át az összes postafiók engedélyek delegálásával és levelek továbbításával kapcsolatos szabályait. Ezzel a [PowerShell-szkripttel](http://aka.ms/delegateforwardrules) végezheti el ezt a feladatot. 



## <a name="next-steps"></a>További lépések

- Az Azure Active Directory Identity Protectionnel kapcsolatos további részletekért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

