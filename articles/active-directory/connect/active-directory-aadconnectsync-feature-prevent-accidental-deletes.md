---
title: "Azure AD Connect szinkronizálása: véletlen törlések megakadályozása |} Microsoft Docs"
description: "Ez a témakör az Azure AD Connectben megakadályozása véletlen törlések (véletlen törlések megakadályozása) szolgáltatást ismerteti."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 72283424ab750d10f9a0739347650d0a9eee1520
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Az Azure AD Connect szinkronizálása: véletlen törlések megakadályozása
Ez a témakör az Azure AD Connectben megakadályozása véletlen törlések (véletlen törlések megakadályozása) szolgáltatást ismerteti.

Ha az Azure AD Connect telepítése megakadályozása véletlen törlések alapértelmezés szerint engedélyezve, nem engedélyezi az export-legfeljebb 500 törlést. A szolgáltatás célja elleni véletlen konfigurációs és a változásokat a helyszíni címtár, amely hatással lenne a sok felhasználóval és egyéb objektumok.

## <a name="what-is-prevent-accidental-deletes"></a>Mi az véletlen törlések megakadályozása
Gyakori helyzetek, amikor megjelenik a sok törlések tartalmazza:

* Vált [szűrés](active-directory-aadconnectsync-configure-filtering.md) Amennyiben egy teljes [OU](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) vagy [tartomány](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) nincs bejelölve.
* Egy szervezeti egység összes objektum törlődnek.
* A szervezeti egység összes objektumára, szinkronizálás hatókörén kívül kell tekinteni, új neve.

A PowerShell használatával módosíthatja az alapértelmezett érték 500 objektumok használatával `Enable-ADSyncExportDeletionThreshold`, amely az AD-Szinkronizáló modul telepítve az Azure Active Directory Connect része. Ez az érték a szervezet méretének megfelelően konfigurálni kell. Mivel a szinkronizálásütemező 30 percenként fut, a értéke 30 percen belül látható törlések száma.

Ha túl sok törli exportálható az Azure AD előkészített, majd az exportálás nem és egy e-mailt ehhez hasonló:

![E-mailek véletlen törlések megakadályozása](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hello (technikai kapcsolattartó). (Idő), az identitás-szinkronizálási szolgáltatás észlelte, hogy a törlések száma meghaladta a konfigurált törlési küszöbét (szervezet neve). A (szám) teljes objektumok küldtek az identitások szinkronizálására, futtassa a törlésre. Ez elérte vagy túllépte a beállított törlés küszöbértéket (szám) objektum. Igazolnia kell, hogy erősítse meg, hogy a törlés fel kell dolgozni, mielőtt folytatódik a azt. Tekintse meg az e-mailben felsorolt hibákról bővebben megakadályozásához véletlen törlések.*
>
> 

Megtekintheti a állapota is `stopped-deletion-threshold-exceeded` mikor célszerű figyelni a **Synchronization Service Managert** az exportált profil felhasználói felülete.
![Szinkronizálás a Service Manager felhasználói felületén véletlen törlések megakadályozása](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Ha ez nem várt, vizsgálja meg, és tegye intézkedéseket. Mely objektumoknak van törölve lesz, tegye a következőket:

1. Start **szinkronizálási szolgáltatás** a Start menüből.
2. Ugrás a **összekötők**.
3. Válassza ki az összekötő típusú **Azure Active Directory**.
4. A **műveletek** jobb, válassza ki a **Összekötőtér keresési**.
5. Az előugró alatt **hatókör**, jelölje be **leválasztása óta** , válassza ki a dátuma a múltban használni. Kattintson a **keresési**. Ezen a lapon jeleníti meg az összes objektum törölve lesz. Minden elem kattintva kaphat további információt az objektum. Is **oszlop beállítás** lehet a rácsban látható további attribútumok hozzáadása.

![Összekötőtér keresése](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

A törlés van szükség, majd tegye a következőket:

1. Az aktuális törlési küszöbét lekéréséhez futtassa a PowerShell-parancsmagot `Get-ADSyncExportDeletionThreshold`. Adja meg az Azure AD globális rendszergazdai fiókot és jelszót. Az alapértelmezett érték 500.
2. Ideiglenes tiltsa le a védelmet, és segítségével azokat törli keresztül halad, futtassa a PowerShell-parancsmagot: `Disable-ADSyncExportDeletionThreshold`. Adja meg az Azure AD globális rendszergazdai fiókot és jelszót.
   ![Hitelesítő adatok](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
3. Az Azure Active Directory-összekötő kijelölését, és válassza ki a **futtatása** válassza **exportálása**.
4. Kívánja újból engedélyezni a védelmet, futtassa a PowerShell-parancsmagot: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Cserélje le az első fellépése beolvasása közben az aktuális törlési küszöbét érték 500. Adja meg az Azure AD globális rendszergazdai fiókot és jelszót.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
