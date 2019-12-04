---
title: Új ügynök konfigurációjának Azure AD Connect a felhőbe
description: Ez a témakör a felhőalapú kiépítés telepítését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794301"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect a felhő üzembe helyezésének új konfigurációját

Miután telepítette az ügynököt, be kell jelentkeznie a Azure Portalba, és konfigurálnia kell a kiépítés konfigurálását.  Az ügynök engedélyezéséhez kövesse az alábbi lépéseket.

## <a name="configure-provisioning"></a>Kiépítés konfigurálása
A kiépítés konfigurálásához kövesse az alábbi lépéseket:

1.  Az Azure AD-portálon kattintson **Azure Active Directory**
2.  Kattintson **Azure ad Connect**
3.  Válassza a **felügyelet kiépítés (előzetes verzió)** 
![](media/how-to-configure/manage1.png)

4.  Kattintson az **Új konfiguráció**elemre.
5.  A konfigurációs képernyőn a helyszíni tartomány előre fel van töltve
6. Adja meg az **értesítő e-mailt**. Az e-mail értesítést kap 
7. Ha a kiépítés nem kifogástalan állapotú.  
8. Helyezze át a választót az **engedélyezéshez** , majd kattintson a **Mentés**gombra.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Az adott felhasználókra és csoportokra való kiépítés hatóköre
Ha azt szeretné, hogy az ügynök hatóköre csak bizonyos felhasználókra és csoportokra legyen szinkronizálva, ezt megteheti. A hatókör a helyszíni AD-csoportok vagy szervezeti egységek használatával végezhető el. Konfiguráción belül nem konfigurálhat csoportokat és szervezeti egységeket. 

1.  Az Azure AD-portálon kattintson **Azure Active Directory**
2.  Kattintson **Azure ad Connect**
3.  Válassza ki a **kiépítés kezelése (előzetes verzió) lehetőséget.**
4.  A **konfiguráció** alatt kattintson a konfigurációra.  
![](media/how-to-configure/scope1.png)

5.  A **Konfigurálás**területen válassza a **minden felhasználó** lehetőséget a konfigurációs szabály hatókörének módosításához.
![](media/how-to-configure/scope2.png)

6. A jobb oldalon a csoport megkülönböztető nevének megadásával, majd a **Hozzáadás**gombra kattintva módosíthatja a hatókört úgy, hogy csak biztonsági csoportokat tartalmazzon.
![](media/how-to-configure/scope3.png)

7. Vagy módosítsa úgy, hogy csak bizonyos szervezeti egységeket tartalmazzon. Kattintson a **kész** és a **Mentés**gombra.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Kiépítés újraindítása 
Ha nem szeretné megvárni a következő ütemezett futtatást, aktiválhatja a kiépítési futtatást az újraindítás kiépítés gomb használatával. 
1.  Az Azure AD-portálon kattintson **Azure Active Directory**
2.  Kattintson **Azure ad Connect**
3.  Válassza ki a **kiépítés kezelése (előzetes verzió) lehetőséget.**
4.  A **konfiguráció** alatt kattintson a konfigurációra.  
![](media/how-to-configure/scope1.png)

5.  A felső részen kattintson a **kiépítés újraindítása**elemre.

## <a name="removing-a-configuration"></a>Konfiguráció eltávolítása
Ha törölni szeretne egy konfigurációt, az alábbi lépések végrehajtásával teheti meg.

1.  Az Azure AD-portálon kattintson **Azure Active Directory**
2.  Kattintson **Azure ad Connect**
3.  Válassza ki a **kiépítés kezelése (előzetes verzió) lehetőséget.**
4.  A **konfiguráció** alatt kattintson a konfigurációra.  
![](media/how-to-configure/scope1.png)

5.  A felső részen kattintson a **Törlés**gombra.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>A konfiguráció törlése előtt nincs megerősítés, ezért győződjön meg arról, hogy ez az a művelet, amelyet a **Törlés**gombra való kattintás előtt el szeretne végezni.


## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
