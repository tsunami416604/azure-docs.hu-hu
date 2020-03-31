---
title: Az Azure Active Directory portálon kockázatra megjelölt felhasználók | Microsoft dokumentumok
description: Ismerje meg az Azure Active Directory portál kockázatosként megjelölt felhasználókról szóló biztonsági jelentését
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30d02c5484ea4cce2953eac6b1b7b26a17c142bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68989699"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Kockázatosként megjelölt felhasználók szervizelése az Azure Active Directory portálon

Az Azure Active Directoryban (Azure AD) található biztonsági jelentésekkel felmérheti a környezetben a feltört felhasználói fiókok valószínűségét. A kockázatosként megjelölt felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Microsoft elkötelezte magát az Ön környezeteinek védelmére. Ezen kötelezettségvállalás részeként a Microsoft folyamatosan monitorozza a szokatlan vagy az ismert támadási mintákat követő tevékenységeket. 

Ha olyan szokatlan tevékenységeket észlel, amelyek a felhasználók egyes fiókjaihoz való jogosulatlan hozzáférést jelezhetnek, értesítéseket kap, amelyek lehetővé teszik a cselekvést. Ez nem jelenti azt, hogy a Microsoft saját rendszerei veszélybe kerültek.

## <a name="access-the-users-flagged-for-risk-report"></a>A kockázatosként megjelölt felhasználókról szóló jelentés elérése

Tekintse át a kockázati kockázatnak kitett [felhasználók](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) által az Azure Portalon megjelölt felhasználókat. Ha nem rendelkezik Azure AD-vel, ingyenesen [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD)regisztrálhat a. 

A kockázati jelentéshez megjelölt felhasználók közül a következő műveleteket teheti meg minden felhasználó esetében:

- Ideiglenes jelszó létrehozása
- Biztonságos jelszóváltoztatás megkövetelése a felhasználótól a következő bejelentkezéskor
- A felhasználó kockázatos voltának figyelmen kívül hagyása szervizelési művelet nélkül.

További információ: [A Felhasználók a kockázatbiztonsági jelentéshez.](concept-user-at-risk.md)

### <a name="azure-ad-subscription-for-office-365-customers"></a>Azure AD-előfizetés Office 365-ügyfelek számára

Office 365-ös hitelesítő adataival is elérheti az **Azure Felügyeleti központot.** Miután aktiválta Azure AD-hozzáférését, a rendszer átirányítja az Azure AD portáljára. Az alapszintű előfizetési szinten a jelentésekben biztosított adatok mennyisége korlátozott. Az Azure Premium-előfizetők további adatokat és elemzéseket érhetnek el.

A Microsoft 365 Felügyeleti központban **a kockázati jelentésekhez megjelölt felhasználók** elérése:

1.  A bal oldali navigációs menüben válassza a **Felügyeleti központok lehetőséget.** 
2.  Válassza az **Azure AD**lehetőséget.
3.  Jelentkezzen be az **Azure Active Directory felügyeleti központba**.
4.  Ha egy szalagcím jelenik meg az oldal tetején, amely azt **mondja: Nézze meg az új portált,** válassza ki a hivatkozást.
4.  A bal oldali navigációs menüben válassza az **Azure Active Directory**lehetőséget. 
5.  A navigációs ablakban válassza a **Felhasználók** megjelölve a biztonság szakaszban a **kockázatmegjelölése** lehetőséget.

## <a name="remediation-actions"></a>Szervizelési műveletek

Végezze el a következő műveleteket az érintett fiókok javításához és a környezet biztonságossá tételéhez:

1.  [Ellenőrizze a megfelelő adatokat](https://aka.ms/MFAValid) a többtényezős hitelesítéshez és az önkiszolgáló jelszó-visszaállításhoz. 
2.  [Engedélyezze a többtényezős hitelesítést](https://aka.ms/MFAuth) minden felhasználó számára. 
3.  Ezt a [szervizelési parancsfájlt](https://aka.ms/remediate) minden érintett fiókhoz használhatja a következő lépések automatikus végrehajtásához: 

    a. Állítsa alaphelyzetbe a jelszót a fiók biztonságossá tétele és az aktív munkamenetek megölése érdekében.

    b. A postaláda meghatalmazottjainak eltávolítása.

    c. A külső tartományokra irányuló levéltovábbítási szabályok letiltása.

    d. A globális levéltovábbítási tulajdonság eltávolítása a postaládára vonatkozóan.

    e. MFA engedélyezése a felhasználó fiókján.

    f. Magas jelszóösszetettség beállítása a fiókhoz.

    g. A postaláda-naplózás engedélyezése.

    h. Naplót kell készítenie a rendszergazdának, hogy áttekintse.

4. Vizsgálja meg az Office 365-bérlőt és a többi informatikai infrastruktúrát, beleértve az összes bérlőbeállítást, felhasználói fiókokat és a felhasználónkénti konfigurációs beállításokat, hogy nem hajtottak-e rajtuk végre módosításokat. Keressen perzisztenciára utaló jeleket, valamint ellenőrizze, hogy egy esetleges támadónak a behatolási kísérlet során sikerült-e hozzáférnie a VPN-hitelesítőadatokhoz vagy más vállalati erőforrásokhoz. 

5.  A vizsgálat részeként fontolja meg, hogy értesítenie kell-e a kormányzati hatóságokat, beleértve a bűnüldözést is.

Ezenkívül el a következőket kell elvégeznie:

- Olvassa el és hajtsa végre ezt [az útmutatót a szokatlan tevékenységek kezelésére vonatkozóan.](https://aka.ms/fixaccount) 
- [Engedélyezze a naplózási folyamatot,](https://aka.ms/improvesecurity) hogy segítsen a bérlőben lévő tevékenység elemzésében. Miután elkészült, a naplózási tároló tevékenységnaplók feltöltése kezdődik. Ezen a ponton a [Biztonsági és Megfelelőségi központ keresési és vizsgálati erőforrását is használhatja.](https://aka.ms/sccsearch) 
- Ezzel a parancsfájllal engedélyezheti a [postaláda-naplózást](https://aka.ms/mailboxaudit1) az összes fiókjában. 
- Tekintse át az összes postafiók engedélyek delegálásával és levelek továbbításával kapcsolatos szabályait. Ezzel a [PowerShell-szkripttel](https://aka.ms/delegateforwardrules) végezheti el ezt a feladatot. 

## <a name="next-steps"></a>További lépések

* [Azure Active Directory identitásvédelem](../active-directory-identityprotection.md)
* [Kockázatosként megjelölt felhasználók](concept-user-at-risk.md)
