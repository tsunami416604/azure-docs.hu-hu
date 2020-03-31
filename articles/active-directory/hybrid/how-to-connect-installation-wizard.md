---
title: Az Azure AD Connect telepítési varázsló jának ismételt futtatása | Microsoft dokumentumok
description: A telepítővarázsló második futtatásakor a program ismerteti a működését.
keywords: Az Azure AD Connect telepítővarázslólehetővé teszi a karbantartási beállítások konfigurálását a második futtatáskor
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261332"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect szinkronizálás: A telepítővarázsló második futtatása
Az Azure AD Connect telepítővarázsló első futtatásakor végigvezeti a telepítés konfigurálásán. Ha ismét futtatja a telepítővarázslót, karbantartási lehetőségeket kínál.

>[!IMPORTANT]
>Ne feledje, hogy a telepítővarázsló nem futtatható, amíg szinkronizálás van folyamatban.  Ellenőrizze, hogy nem fut-e a szinkronizálás a varázsló elindítása előtt.

A telepítővarázsló az **Azure AD Connect**nevű Start menüben található.

![Start menü](./media/how-to-connect-installation-wizard/startmenu.png)

A telepítővarázsló indításakor megjelenik egy lap a következő beállításokkal:

![Lap további feladatok listájával](./media/how-to-connect-installation-wizard/additionaltasks.png)

Ha telepítette az ADFS-t az Azure AD Connecttel, még több lehetősége van. Az ADFS-hez rendelkezésre áll további lehetőségeket az [ADFS-kezelés dokumentálja.](how-to-connect-fed-management.md#manage-ad-fs)

Jelölje ki az egyik tevékenységet, és a folytatáshoz kattintson a **Tovább** gombra.

> [!IMPORTANT]
> Amíg a telepítővarázsló meg van nyitva, a szinkronizálási motor összes művelete fel van függesztve. A konfigurációs módosítások befejezése után zárja be a telepítővarázslót.
>
>

## <a name="view-current-configuration"></a>Az aktuális konfiguráció megtekintése
Ezzel a beállítással gyorsan megtekintheti a jelenleg konfigurált beállításokat.

![Lap az összes beállítás és állapotuk listájával](./media/how-to-connect-installation-wizard/viewconfig.png)

A visszalépéshez kattintson az **Előző** gombra. Ha a Kilépés lehetőséget **választja,** bezárja a telepítővarázslót.

## <a name="customize-synchronization-options"></a>Szinkronizálási beállítások testreszabása
Ezzel a beállítással módosíthatja a szinkronizálási konfigurációt. Az egyéni konfigurációtelepítési elérési út beállításainak egy részhalmaza jelenik meg. Ez a lehetőség akkor is megjelenik, ha kezdetben expressz telepítést használt.

* [További könyvtárak hozzáadása](how-to-connect-install-custom.md#connect-your-directories). A címtár eltávolításáról az [Összekötő törlése](how-to-connect-sync-service-manager-ui-connectors.md#delete)című témakörben található.
* [A Tartomány és a szervezeti egység szűrésének módosítása](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Csoportszűrés eltávolítása.
* [A választható funkciók módosítása](how-to-connect-install-custom.md#optional-features).

A kezdeti telepítés többi beállítása nem módosítható, és nem érhető el. Ezek a lehetőségek a következők:

* Módosítsa a userPrincipalName és sourceAnchor attribútumát.
* Módosítsa a különböző erdőből származó objektumok illesztési módszerét.
* Csoportalapú szűrés engedélyezése.

## <a name="refresh-directory-schema"></a>Könyvtárséma frissítése
Ez a beállítás akkor használatos, ha a sémát a helyszíni AD DS-erdők egyikében módosította. Előfordulhat például, hogy telepítette az Exchange-et, vagy eszközobjektumokkal rendelkező Windows Server 2012 sémára frissített. Ebben az esetben meg kell utasítania az Azure AD Connectet, hogy olvassa el újra a sémát az AD DS-ből, és frissítse a gyorsítótárat. Ez a művelet a szinkronizálási szabályokat is újragenerálja. Ha hozzáadja az Exchange-sémát, példaként az Exchange szinkronizálási szabályai kerülnek a konfigurációba.

Ha ezt a beállítást választja, a konfigurációban lévő összes könyvtár megjelenik a listában. Megtarthatja az alapértelmezett beállítást, és frissítheti az összes erdőt, vagy kiléphet közülük.

![Lap a környezetben lévő összes könyvtár listájával](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Átmeneti mód konfigurálása
Ez a beállítás lehetővé teszi az átmeneti mód engedélyezését és letiltását a kiszolgálón. Az átmeneti módról és annak használatáról további információ az [Operations](how-to-connect-sync-staging-server.md)területen található.

A beállítás azt mutatja, hogy az előkészítés jelenleg engedélyezve van-e vagy le van tiltva:  
![Az átmeneti üzemmód aktuális állapotát is megjelenítő beállítás](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Az állapot módosításához jelölje be ezt a beállítást, és jelölje be a jelölőnégyzetet, vagy törölje belőle a jelet.  
![Az átmeneti üzemmód aktuális állapotát is megjelenítő beállítás](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>A felhasználói bejelentkezés módosítása
Ez a beállítás lehetővé teszi a felhasználói bejelentkezési módszer módosítását a jelszókivonat-szinkronizálásra, az átadó hitelesítésre vagy az összevonásra. Nem módosítható úgy, hogy **ne konfigurálja**a beállítást.

Erről a beállításról a [felhasználói bejelentkezés című témakörben](plan-connect-user-signin.md#changing-the-user-sign-in-method)talál további információt.

## <a name="next-steps"></a>További lépések
* Tudjon meg többet az Azure AD Connect szinkronizálása által használt konfigurációs modellről [a deklaratív kiépítés ismertetése című](concept-azure-ad-connect-sync-declarative-provisioning.md)témakörben.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
