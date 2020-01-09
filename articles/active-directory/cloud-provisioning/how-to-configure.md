---
title: Új ügynök konfigurációjának Azure AD Connect a felhőbe
description: Ez a cikk a felhőalapú kiépítés telepítését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853950cf441007eac0170f32f28f2d9c16a71292
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549431"
---
# <a name="azure-ad-connect-cloud-provisioning-new-agent-configuration"></a>Új ügynök konfigurációjának Azure AD Connect a felhőbe

Miután telepítette az ügynököt, be kell jelentkeznie a Azure Portalba, és konfigurálnia kell a Azure Active Directory (Azure AD) kapcsolódási felhőhöz való üzembe helyezését. Az ügynök engedélyezéséhez kövesse az alábbi lépéseket.

## <a name="configure-provisioning"></a>Kiépítés konfigurálása
A kiépítés konfigurálásához kövesse az alábbi lépéseket.

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza a **Azure ad Connect**lehetőséget.
1.  Válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.

    ![Kiépítés kezelése (előzetes verzió)](media/how-to-configure/manage1.png)

1.  Válassza az **Új konfiguráció**lehetőséget.
1.  A konfigurációs képernyőn a helyszíni tartomány előre fel van töltve.
1.  Adja meg az **értesítő e-mailt**. Az e-mail értesítést kap, ha a kiépítés állapota nem kifogástalan.
1.  Helyezze át a választót az **engedélyezéshez**, majd válassza a **Mentés**lehetőséget.

    ![Azure AD-kiépítés (előzetes verzió)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Hatókör-kiépítés adott felhasználókra és csoportokra
Az ügynök hatókörével a helyi Active Directory csoportok vagy szervezeti egységek használatával szinkronizálhatja az adott felhasználókat és csoportokat. Nem konfigurálhat csoportokat és szervezeti egységeket a konfiguráción belül. 

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza a **Azure ad Connect**lehetőséget.
1.  Válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
1.  A **konfiguráció**területen válassza ki a konfigurációt.

    ![Konfigurációs szakasz](media/how-to-configure/scope1.png)

1.  A **Konfigurálás**területen válassza a **minden felhasználó** lehetőséget a konfigurációs szabály hatókörének módosításához.

    ![Minden felhasználó lehetőség](media/how-to-configure/scope2.png)

1. A jobb oldalon úgy módosíthatja a hatókört, hogy csak biztonsági csoportokat tartalmazzon. Adja meg a csoport megkülönböztető nevét, majd kattintson a **Hozzáadás**gombra.

    ![Kiválasztott biztonsági csoportok beállítás](media/how-to-configure/scope3.png)

1.  Vagy módosíthatja a hatókört úgy, hogy csak bizonyos szervezeti egységeket tartalmazzon. Válassza a **kész** és a **Mentés**lehetőséget.

    ![Kiválasztott szervezeti egységek lehetőség](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Kiépítés újraindítása 
Ha nem szeretné megvárni a következő ütemezett futtatást, aktiválja a kiépítési futtatást az **Újraindítás kiépítés** gomb használatával. 
1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza a **Azure ad Connect**lehetőséget.
1.  Válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
1.  A **konfiguráció**területen válassza ki a konfigurációt.

    ![Konfiguráció kiválasztása a kiépítés újraindításához](media/how-to-configure/scope1.png)

1.  A felső részen válassza a **kiépítés újraindítása**lehetőséget.

## <a name="remove-a-configuration"></a>Konfiguráció eltávolítása
A konfiguráció törléséhez kövesse az alábbi lépéseket.

1.  Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
1.  Válassza a **Azure ad Connect**lehetőséget.
1.  Válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.
1.  A **konfiguráció**területen válassza ki a konfigurációt.

    ![Konfiguráció eltávolítására szolgáló konfigurációs beállítás](media/how-to-configure/scope1.png)

1.  A konfiguráció képernyő felső részén válassza a **Törlés**lehetőséget.

    ![Törlés gomb](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>A konfiguráció törlése előtt nincs megerősítés. Győződjön meg arról, hogy ezt a műveletet szeretné végrehajtani a **Törlés**kiválasztása előtt.


## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
