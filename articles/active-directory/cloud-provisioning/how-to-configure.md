---
title: Az Azure AD Connect felhőalapú kiépítése új ügynökkonfiguráció
description: Ez a cikk a felhőkiépítés telepítését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620972"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Új konfiguráció létrehozása az Azure AD Connect felhőalapú kiépítéséhez

Miután telepítette az ügynököt, be kell jelentkeznie az Azure Portalon, és konfigurálnia kell az Azure Active Directory (Azure AD) Connect felhőalapú kiépítést. Az ügynök engedélyezéséhez kövesse az alábbi lépéseket.

## <a name="configure-provisioning"></a>Kiépítés konfigurálása
A kiépítés konfigurálásához kövesse az alábbi lépéseket.

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza **az Azure AD Connect lehetőséget.**
1.  Válassza **a Kiépítés kezelése (előzetes verzió)** lehetőséget.

    ![Kiépítés kezelése (előzetes verzió)](media/how-to-configure/manage1.png)

1.  Válassza az **Új konfiguráció lehetőséget**.
1.  A konfigurációs képernyőn a helyszíni tartomány előre ki van töltve.
1.  Adja meg **az értesítési e-mailt**. Ez az e-mail értesítést kap, ha a kiépítés nem kifogástalan.
1.  Helyezze át a választót az **Engedélyezés**beállításba, és válassza a **Mentés**lehetőséget.

    ![Azure AD-kiépítés (előzetes verzió)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Hatókör-kiépítés adott felhasználók és csoportok számára
Az ügynök hatóköre adott felhasználók és csoportok szinkronizálására helyszíni Active Directory-csoportok vagy szervezeti egységek használatával. A konfiguráción belül nem konfigurálhat csoportokat és szervezeti egységeket. 

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza **az Azure AD Connect lehetőséget.**
1.  Válassza **a Kiépítés kezelése (előzetes verzió)** lehetőséget.
1.  A **Konfiguráció csoportban**válassza ki a konfigurációt.

    ![Konfigurációs szakasz](media/how-to-configure/scope1.png)

1.  A **Konfigurálás**csoportban válassza a **Minden felhasználó** lehetőséget a konfigurációs szabály hatókörének módosításához.

    ![Minden felhasználó beállítás](media/how-to-configure/scope2.png)

1. A jobb oldalon módosíthatja a hatókört, hogy csak a biztonsági csoportokat tartalmazza. Írja be a csoport megkülönböztető nevét, és válassza a **Hozzáadás gombot.**

    ![Kijelölt biztonsági csoportok beállítás](media/how-to-configure/scope3.png)

1.  Vagy módosíthatja a hatókört úgy, hogy csak adott szervezeti egységeket tartalmazzon. Válassza a **Kész** és **mentés lehetőséget.**  
2.  Miután módosította a hatókört, újra kell indítania a [kiépítést](#restart-provisioning) a módosítások azonnali szinkronizálásának kezdeményezéséhez.

    ![Kijelölt szervezeti egységek beállítás](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Kiépítés újraindítása 
Ha nem szeretné megvárni a következő ütemezett futtatást, indítsa el a kiépítés t az **Újraépítés** gomb használatával. 
1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza **az Azure AD Connect lehetőséget.**
1.  Válassza **a Kiépítés kezelése (előzetes verzió)** lehetőséget.
1.  A **Konfiguráció csoportban**válassza ki a konfigurációt.

    ![A kiépítés újraindításához kiválasztott konfiguráció](media/how-to-configure/scope1.png)

1.  A lap tetején válassza a **Kiépítés újraindítása**lehetőséget.

## <a name="remove-a-configuration"></a>Konfiguráció eltávolítása
Konfiguráció törléséhez kövesse az alábbi lépéseket.

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza **az Azure AD Connect lehetőséget.**
1.  Válassza **a Kiépítés kezelése (előzetes verzió)** lehetőséget.
1.  A **Konfiguráció csoportban**válassza ki a konfigurációt.

    ![Konfigurációválasztás a konfiguráció eltávolításához](media/how-to-configure/scope1.png)

1.  A konfigurációs képernyő tetején válassza a **Törlés**lehetőséget.

    ![Törlés gomb](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>A konfiguráció törlése előtt nincs megerősítés. A **Törlés (Törlés)** lehetőséget választva győződjön meg arról, hogy ezt a műveletet szeretné végrehajtani.


## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
