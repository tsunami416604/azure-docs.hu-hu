---
title: "Az Azure AD Connect: További lépések és kezelése az Azure AD Connect |} Microsoft Docs"
description: "Ismerje meg, hogyan terjeszthető ki az alapértelmezett konfiguráció és a működtetési feladatok az Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e898ee203276b072605fe0f21af633ecfdaae1fe
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Következő lépések és az Azure AD Connect kezelése
Ebben a cikkben a műveleti eljárások segítségével testre szabhatja az Azure Active Directory (Azure AD) Csatlakozás a szervezeti igények és követelmények.  

## <a name="add-additional-sync-admins"></a>További szinkronizálási rendszergazdák hozzáadása
Alapértelmezés szerint csak a felhasználó adta meg a telepítési és a helyi rendszergazdák képesek a telepített szinkronizálási motor kezeléséhez. A további személyek férhessen hozzá és felügyelhesse a szinkronizálási motor keresse meg a csoport ADSyncAdmins nevű a helyi kiszolgálón, és adja hozzá ezt a csoportot.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Licencek hozzárendelése az Azure AD Premium és nagyvállalati mobilitási csomag számára
Most, hogy a felhő szinkronizálta a felhasználókat, rendeljen hozzájuk licencet, a felhőalapú alkalmazások, például az Office 365 használatbavétele szeretné.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Az Azure AD Premium vagy Enterprise Mobility Suite licenc hozzárendelése

1. Rendszergazdaként jelentkezzen be az Azure-portálon
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az a **Active Directory** lapon, kattintson duplán a könyvtár, amely rendelkezik a beállítani kívánt felhasználókat.
4. A könyvtárlap tetején válassza a **Licencek** elemet.
5. Az a **licencek** lapon jelölje be **Active Directory Premium** vagy **nagyvállalati mobilitási csomag**, és kattintson a **hozzárendelése**.
6. A párbeszédpanelen válassza ki azokat a felhasználókat, akikhez licenceket szeretne rendelni, majd kattintson a pipa ikonra a módosítások mentéséhez.

## <a name="verify-the-scheduled-synchronization-task"></a>Az ütemezett szinkronizálás task ellenőrzése.
Az Azure portál segítségével tekintse meg a szinkronizálást.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Az ütemezett szinkronizálás task ellenőrzése.
1. Rendszergazdaként jelentkezzen be az Azure-portálon
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az a **Active Directory** lapon, kattintson duplán a könyvtár, amely rendelkezik a beállítani kívánt felhasználókat.
4. A címtár lap tetején jelölje ki a **címtár-integráció**.
5. A **helyi active directory integrációja a**, vegye figyelembe a legutóbbi szinkronizálás ideje.

<center>![Címtár-szinkronizálás ideje](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Egy beütemezett szinkronizálási feladat indítása
Ha szeretne futtatni egy szinkronizálási feladat, ehhez az Azure AD Connect varázsló ismételt futtatásával.  Meg kell adnia az Azure AD hitelesítő adatait.  A varázslóban válassza a **testre szabhatja a szinkronizálási beállítások** feladat, és kattintson a **tovább** helyezhető át, a varázsló használatával. A végén győződjön meg arról, hogy a **, amint a kezdeti konfigurálás befejeződik, indítsa el a szinkronizálási folyamat** be van jelölve.

<center>![A szinkronizálás megkezdéséhez](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Az Azure AD Connect szinkronizálási Feladatütemező további információkért lásd: [az Azure AD Connect Feladatütemező](active-directory-aadconnectsync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Az Azure AD Connectben elérhető további feladatok
Az Azure AD Connect a kezdeti telepítés után is mindig a varázsló ismételt futtatása a Azure AD Connect start lap vagy az asztal parancsikonnal.  Megfigyelheti, hogy a varázsló újra áthaladás lehetőségeket is kínál néhány új további feladatok formájában.  

A következő táblázat a feladatok összefoglalása, és minden feladat rövid leírása.

![További feladatok listája](./media/active-directory-aadconnect-whats-next/addtasks.png)

| További feladatok | Leírás |
| --- | --- |
| **A választott forgatókönyv megtekintése** |A jelenleg az Azure AD Connect megoldás megtekintéséhez.  Ez magában foglalja az általános beállítások szinkronizálása könyvtárak, és a szinkronizálási beállítások. |
| **Szinkronizálási beállítások testreszabása** |Például további Active Directory-erdők hozzáadása a konfigurációt, vagy a szinkronizálási beállítások, például felhasználó, csoport, eszköz vagy a jelszóvisszaírás engedélyezése a jelenlegi konfiguráció módosítása. |
| **Az átmeneti környezetű üzemmód engedélyezése** |Szakasz információkat, amelyek nem azonnal szinkronizálja, és nem az Azure ad Szolgáltatásba exportálni vagy a helyszíni Active Directory.  Ezzel a szolgáltatással mielőtt bekövetkeznének megtekintheti a szinkronizálást. |

## <a name="next-steps"></a>Következő lépések
További információ [a helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
