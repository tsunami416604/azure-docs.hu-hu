---
title: 'Az Azure AD Connect szinkronizálása: véletlen törlések megakadályozása |} A Microsoft Docs'
description: Ez a témakör ismerteti a megakadályozása véletlen törlések (véletlen törlések megakadályozása) szolgáltatást az Azure AD Connectben.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6a535d776da216029b905fb604ed378449a81082
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314628"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Az Azure AD Connect szinkronizálása: véletlen törlések megakadályozása
Ez a témakör ismerteti a megakadályozása véletlen törlések (véletlen törlések megakadályozása) szolgáltatást az Azure AD Connectben.

Amikor az Azure AD Connect telepítésének megakadályozása véletlen törlések alapértelmezés szerint engedélyezve van és nem engedélyezte a az export-több mint 500 törlést. A funkció célja, hogy meggátolja a konfiguráció és a helyszíni címtár olyan véletlen módosításait, amelyek nagy számú felhasználót és egyéb objektumot érintenek.

## <a name="what-is-prevent-accidental-deletes"></a>Mi az véletlen törlések megakadályozása
Gyakori forgatókönyvek, amikor megjelenik számos törlések tartalmazza:

* Vált [szűrés](how-to-connect-sync-configure-filtering.md) , ha egy teljes [OU](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) vagy [tartomány](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nincs bejelölve.
* Egy adott szervezeti egységben lévő minden objektum törlése.
* Egy szervezeti egység átnevezése, amelynek következtében a rendszer a benne lévő összes objektumot hatókörön kívülinek tekinti a szinkronizálás vonatkozásában.

A PowerShell használatával módosíthatja az alapértelmezett érték 500-objektumok használatával `Enable-ADSyncExportDeletionThreshold`, amely az AD-Szinkronizáló modul telepítve van az Azure Active Directory Connect része. Ezt az értéket a szervezet méretének megfelelően átméretezi kell konfigurálnia. Szinkronizálásütemező 30 percenként fut, mivel az érték 30 percen belül látható törlések száma.

Ha túl sok törlések előkészített nelze exportovat do Azure ad-ben, majd az Exportálás leállítja, és a egy e-mailt kap:

![E-mailek véletlen törlések megakadályozása](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hello (technikai kapcsolattartó). (Idő), az identitás-szinkronizálási szolgáltatás azt észlelte, hogy a törlések száma túllépte a beállított törlési küszöbértéke (szervezet neve). Teljes (szám) objektumok a identitásszinkronizálási, futtassa a törlésre lett elküldve. Ez elérte vagy túllépte a beállított törlési küszöbértéket (szám) objektumok. Meg kell adnia erősítse meg, hogy a törlés fel kell dolgozni fog a folytatás előtt. Tekintse át a hiba az e-mail üzenetben szereplő további információt a megakadályozó véletlen törlések.*
>
> 

Emellett megtekintheti az állapotát `stopped-deletion-threshold-exceeded` meg, így a **Synchronization Service Managert** a Export-profilhoz tartozó felhasználói felületen.
![Szinkronizálás a Service Manager felhasználói felületén véletlen törlések megakadályozása](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Ha ez nem várt, vizsgálja meg, és javítási műveleteket. Ha szeretné látni, mely objektumokat is törölve lesz, tegye a következőket:

1. Indítsa el **szinkronizálási szolgáltatás** a Start menüből.
2. Lépjen a **összekötők**.
3. Válassza ki az összekötő típusú **Azure Active Directory**.
4. A **műveletek** a jobb oldalon válassza ki a **keresési Összekötőterét**.
5. Az előugró alatt **hatókör**válassza **leválasztott óta** , és válassza ki egyszerre a múltban. Kattintson a **keresési**. Ezen a lapon megtekintheti az összes objektum törölve lesz. Minden elem kattintva kaphat további információt az objektum. Is **oszlop beállítás** lehet a rácsban látható további attribútumok hozzáadása.

![Keresési Összekötőterét](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

Összes törlése megfelelő vannak, majd tegye a következőket:

1. Az aktuális törlés küszöbértékének lekéréséhez futtassa a PowerShell-parancsmagot `Get-ADSyncExportDeletionThreshold`. Adja meg az Azure AD globális rendszergazdai fiókot és jelszót. Az alapértelmezett érték: 500.
2. Ideiglenes tiltsa le a védelmet, és ezeket a törlések halad át, és futtassa a PowerShell-parancsmag segítségével: `Disable-ADSyncExportDeletionThreshold`. Adja meg az Azure AD globális rendszergazdai fiókot és jelszót.
   ![Hitelesítő adatok](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Az Azure Active Directory-összekötő ki van jelölve, válassza a művelet **futtatása** válassza **exportálása**.
4. Kívánja újból engedélyezni a védelmet, futtassa a PowerShell-parancsmagot: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Azt észleltük, hogy az aktuális törlés küszöbértékének lekérésekor értékét cserélje le a 500. Adja meg az Azure AD globális rendszergazdai fiókot és jelszót.

## <a name="next-steps"></a>További lépések
**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: ismertetése, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
