---
title: Azure AD Connect Cloud kiépítési ügynök telepítése
description: Ez a témakör részletesen ismerteti a kiépítési ügynök telepítését.
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
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846232"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>Azure AD Connect Cloud kiépítési ügynök telepítése
Ez a dokumentum végigvezeti a Azure AD Connect üzembe helyezési ügynök telepítési folyamatán, és azt, hogy miként lehet először konfigurálni a Azure Portalban.

>[!IMPORTANT]
>A következő telepítési utasítások feltételezik, hogy az összes [előfeltétel](how-to-prerequisites.md) teljesült.

Azure AD Connect kiépítés telepítése és konfigurálása a következő lépések végrehajtásával történik:
    
- [Az ügynök telepítése](#install-the-agent)
- [Ügynök telepítésének ellenőrzése](#verify-agent-installation)


## <a name="install-the-agent"></a>Az ügynök telepítése

1. Jelentkezzen be a-kiszolgálóra, amelyet a vállalati rendszergazdai engedélyekkel fog használni.
2. Navigáljon a Azure Portalhoz, és válassza a bal oldalon a Azure Active Directory lehetőséget.
3. Kattintson a **kiépítés kezelése (előzetes verzió)** elemre, és válassza **az összes ügynök áttekintése**lehetőséget.
3. Töltse le a Azure AD Connect kiépítési ügynököt a Azure Portalból.
![Üdvözlőképernyő](media/how-to-install/install9.png)</br>
3. Az Azure AD Connect kiépítés futtatása (AADConnectProvisioningAgent. Installer)
3. A splash képernyőn **fogadja el** a licencelési feltételeket, majd kattintson a **telepítés**gombra.</br>
![Üdvözlőképernyő](media/how-to-install/install1.png)</br>

4. A művelet befejezése után elindul a konfigurációs varázsló.  Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.
5. A **Active Directory összekapcsolása** képernyőn kattintson a **könyvtár hozzáadása** lehetőségre, majd jelentkezzen be a Active Directory rendszergazdai fiókjával.  Ezzel a művelettel a helyszíni címtárat fogja felvenni.  Kattintson a **Tovább** gombra.</br>
![Üdvözlőképernyő](media/how-to-install/install3.png)</br>

6. A **konfiguráció kész** képernyőn kattintson a **Confirm (megerősítés**) gombra.  Ez a művelet regisztrálja és újraindítja az ügynököt.</br>
![Üdvözlőképernyő](media/how-to-install/install4.png)</br>

7. Miután a művelet befejeződik, meg kell jelennie arról, hogy **a sikeres ellenőrzést** észlelte.  Kattintson a **Kilépés**lehetőségre.</br>
![Üdvözlőképernyő](media/how-to-install/install5.png)</br>
8. Ha továbbra is megjelenik a kezdeti splash képernyő, kattintson a **Bezárás**gombra.


## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Az ügynök ellenőrzése a Azure Portal és az ügynököt futtató helyi kiszolgálón történik.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése
Az alábbi lépéseket követve ellenőrizheti, hogy az ügynök látja-e az Azure-t:

1. Jelentkezzen be az Azure portálra.
2. A bal oldalon válassza a **Azure Active Directory**, majd a **Azure ad Connect** , és a központban válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.</br>
![Azure Portalra](media/how-to-install/install6.png)</br>

3.  Az **Azure ad-kiépítés (előzetes verzió)** képernyőn kattintson az **összes ügynök áttekintése**elemre.
Azure AD-kiépítés ![](media/how-to-install/install7.png)</br>
 
4. A helyszíni **kiépítési ügynökök képernyőjén** látni fogja a telepített ügynököket.  Ellenőrizze, hogy a szóban forgó ügynök **aktív**-e, és hogy van-e megjelölve.
![kiépítési ügynökök](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>A port ellenőrzése
Annak ellenőrzéséhez, hogy az Azure figyel-e a 443-es porton, és hogy az ügynök képes-e kommunikálni vele, a következőket használhatja:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ez a teszt azt ellenőrzi, hogy az ügynökök képesek-e kommunikálni az Azure-ban a 443-as porton keresztül.  Nyisson meg egy böngészőt, és navigáljon a fenti URL-címre abban a kiszolgálón, ahol az ügynököt telepítették.
![Szolgáltatások](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>A helyi kiszolgálón
Az ügynök futtatásának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal
2.  Nyissa meg a szolgáltatásokat vagy navigáljon a **szolgáltatáshoz** , vagy indítsa el a Start/Run/Services. msc parancsot.
3.  A **szolgáltatások** területen győződjön meg arról, hogy **Microsoft Azure ad az ügynök frissítése** és a **Microsoft Azure ad kapcsolat létesítése ügynök** van ott, és az állapota **fut**.
![Szolgáltatások](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Az ügynök telepítve van, de konfigurálni és engedélyezni kell a felhasználók szinkronizálásának megkezdése előtt.  Új ügynök konfigurálásához tekintse meg az [új ügynök konfigurációjának Azure ad Connect üzembe](how-to-configure.md)helyezését ismertető témakört.



## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
 
