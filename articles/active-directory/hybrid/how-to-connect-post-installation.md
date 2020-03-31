---
title: 'Azure AD Connect: A következő lépések és az Azure AD Connect kezelése | Microsoft dokumentumok'
description: Ismerje meg, hogyan bővítheti az Azure AD Connect alapértelmezett konfigurációs és üzemeltetési feladatait.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261293"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>A következő lépések és az Azure AD Connect kezelése
A cikkben található működési eljárások segítségével testreszabhatja az Azure Active Directory (Azure AD) Connectet a szervezet igényeinek és követelményeinek megfelelően.  

## <a name="add-additional-sync-admins"></a>További szinkronizálási rendszergazdák hozzáadása
Alapértelmezés szerint csak a telepítést végző felhasználó és a helyi rendszergazdák kezelhetik a telepített szinkronizálási motort. Ha további személyek is hozzáférhetnek a szinkronizálási motorhoz, keresse meg az ADSyncAdmins nevű csoportot a helyi kiszolgálón, és adja hozzá őket a csoporthoz.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Licencek hozzárendelése az Azure AD Premium és enterprise mobility suite-felhasználókhoz
Most, hogy a felhasználók szinkronizálva lettek a felhővel, hozzá kell rendelnie nekik egy licencet, hogy elindulhassanak a felhőalapú alkalmazások, például az Office 365 számára.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Azure AD Premium vagy Enterprise Mobility Suite licenc hozzárendelése

1. Jelentkezzen be az Azure Portalon rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az **Active Directory** lapon kattintson duplán arra a könyvtárra, amelybe a beállítani kívánt felhasználókat szeretné beállítani.
4. A könyvtárlap tetején válassza a **Licencek** elemet.
5. A **Licencek** lapon válassza az **Active Directory Premium** vagy az Enterprise Mobility **Suite**lehetőséget, majd kattintson a **Hozzárendelés gombra.**
6. A párbeszédpanelen válassza ki azokat a felhasználókat, akikhez licenceket szeretne rendelni, majd kattintson a pipa ikonra a módosítások mentéséhez.

## <a name="verify-the-scheduled-synchronization-task"></a>Az ütemezett szinkronizálási feladat ellenőrzése
Az Azure Portal használatával ellenőrizze a szinkronizálás állapotát.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Az ütemezett szinkronizálási feladat ellenőrzése
1. Jelentkezzen be az Azure Portalon rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. A bal oldalon válassza az **Azure AD Connect lehetőséget**
4. A lap tetején jegyezze fel az utolsó szinkronizálást.

![Könyvtár szinkronizálási ideje](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Ütemezett szinkronizálási feladat indítása
Ha szinkronizálási feladatot kell futtatnia, ezt a következők szerint teheti meg:

1. Kattintson duplán az Azure AD Connect asztali parancsikonjára a varázsló elindításához.
2. Kattintson a **Configure** (Konfigurálás) elemre.
3. A feladatok képernyőn jelölje be a **Szinkronizálás testreszabása jelölőnégyzetet,** és kattintson a **Tovább** gombra.
4. Adja meg Azure AD hitelesítő adatait
5. Kattintson a **Tovább** gombra. Kattintson a **Tovább** gombra.  Kattintson a **Tovább** gombra.
5.  A **Konfigurálás ra** képernyőn győződjön meg arról, hogy **a szinkronizálásindítása a konfiguráció befejezésekor** jelölőnégyzet be van jelölve.
6.  Kattintson a **Configure** (Konfigurálás) elemre.

Az Azure AD Connect szinkronizálásütemezőről az [Azure AD Connect ütemező című](how-to-connect-sync-feature-scheduler.md)témakörben talál további információt.

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Az Azure AD Connect további feladatai
Az Azure AD Connect kezdeti telepítése után bármikor újraindíthatja a varázslót az Azure AD Connect kezdőlapjáról vagy az asztali parancsikonról.  Észre fogja venni, hogy megy keresztül a varázsló ismét nyújt néhány új lehetőséget formájában további feladatokat.  

Az alábbi táblázat ezeknek a tevékenységeknek az összefoglalását és az egyes tevékenységek rövid leírását tartalmazza.

![További feladatok listája](./media/how-to-connect-post-installation/addtasks2.png)

| További feladat | Leírás |
| --- | --- |
|**Adatvédelmi beállítások**|Tekintse meg, milyen telemetriai adatok at osztanak meg a Microsofttal.|
|**Az aktuális konfiguráció megtekintése**|Tekintse meg a jelenlegi Azure AD Connect-megoldást.  Ez magában foglalja az általános beállításokat, a szinkronizált könyvtárakat és a szinkronizálási beállításokat. |
| **Szinkronizálási beállítások testreszabása** |Módosítsa az aktuális konfigurációt, például adjon hozzá további Active Directory-erdőket a konfigurációhoz, vagy engedélyezve a szinkronizálási beállításokat, például a felhasználó, a csoport, az eszköz vagy a jelszó visszaírását. |
|**Eszközbeállítások konfigurálása**|A szinkronizáláshoz rendelkezésre álló eszközbeállítások|
|**Könyvtárséma frissítése**|Lehetővé teszi új helyszíni címtárobjektumok hozzáadását a szinkronizáláshoz|
|**Átmeneti mód konfigurálása** |Olyan fázisadatok, amelyek nincsenek azonnal szinkronizálva, és nem exportálva az Azure AD vagy a helyszíni Active Directoryba.  Ezzel a funkcióval megtekintheti a szinkronizálásokat, mielőtt azok bekövetkeznének. |
|**A felhasználói bejelentkezés módosítása**|A felhasználók által a bejelentkezéshez használt hitelesítési módszer módosítása|
|**Összevonás kezelése**|Az AD FS-infrastruktúra kezelése, tanúsítványok megújítása és AD FS-kiszolgálók hozzáadása|
|**Elhárítása**|Segítség az Azure AD Connecttel kapcsolatos problémák elhárításához|

## <a name="next-steps"></a>További lépések
További információ [a helyszíni identitások azure-active Directoryval való integrálásáról.](whatis-hybrid-identity.md)
