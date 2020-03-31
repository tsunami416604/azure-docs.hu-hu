---
title: Az Azure AD Connect felhőalapú jogosultságkiosztási ügynök telepítése
description: Ez a cikk ismerteti, hogyan telepítheti az Azure AD Connect felhőalapú kiépítési ügynök.
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
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263347"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Az Azure AD Connect felhőalapú jogosultságkiosztási ügynök telepítése
Ez a dokumentum végigvezeti az Azure Active Directory (Azure AD) Connect létesítő ügynök telepítési folyamatán, és hogyan konfigurálhatja azt az Azure Portalon.

>[!IMPORTANT]
>A következő telepítési utasítások [feltételezik,](how-to-prerequisites.md) hogy az összes előfeltétel teljesült.

Az Azure AD Connect kiépítésének telepítése és konfigurálása a következő lépésekben történik:
    
- [Az ügynök telepítése](#install-the-agent)
- [Ügynök telepítésének ellenőrzése](#verify-agent-installation)


## <a name="install-the-agent"></a>Az ügynök telepítése
Az ügynök telepítéséhez kövesse az alábbi lépéseket.

1. Jelentkezzen be a vállalati rendszergazdai engedélyekkel használni kívánt kiszolgálóra.
1. Nyissa meg az Azure Portalt. A bal oldalon válassza az **Azure Active Directory**lehetőséget.
1. Válassza **a Kiépítés kezelése (előzetes verzió)** > **Az összes ügyintéző áttekintése**lehetőséget.
1. Töltse le az Azure AD Connect kiépítési ügynök az Azure Portalról.

   ![Helyszíni ügynök letöltése](media/how-to-install/install9.png)</br>
1. Futtassa az Azure AD Connect kiépítési telepítőt (AADConnectProvisioningAgent.Installer).
1. A **Microsoft Azure AD Connect kiépítési ügynökcsomag képernyőjén** fogadja el a licencelési feltételeket, és válassza a **Telepítés lehetőséget.**

   ![A Microsoft Azure AD Connect kiépítési ügynökcsomagjának képernyője](media/how-to-install/install1.png)</br>

1. A művelet befejezése után elindul a konfigurációs varázsló. Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.
1. Az **Active Directory csatlakoztatása** képernyőn válassza a **Címtár hozzáadása**lehetőséget. Ezután jelentkezzen be az Active Directory rendszergazdai fiókjával. Ez a művelet hozzáadja a helyszíni címtárat. Válassza a **Tovább lehetőséget.**

   ![Az Active Directory csatlakoztatása képernyő](media/how-to-install/install3.png)</br>

1. A **Konfiguráció teljes** képernyőjén válassza a **Megerősítés**lehetőséget. Ez a művelet regisztrálja és újraindítja az ügynököt.

   ![Konfiguráció teljes képernyő](media/how-to-install/install4.png)</br>

1. A művelet befejezése után látnia kell az értesítést **Az ügynök konfigurációjának sikeres ellenőrzése.** Válassza **a Kilépés**lehetőséget.

   ![Kilépés gomb](media/how-to-install/install5.png)</br>
1. Ha továbbra is megjelenik a **Microsoft Azure AD Connect kiépítési ügynökcsomag kezdeti képernyője,** válassza a **Bezárás**lehetőséget.

## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Ügynök ellenőrzése történik az Azure Portalon és a helyi kiszolgálón, amely az ügynök fut.

### <a name="azure-portal-agent-verification"></a>Az Azure Portal ügynökének ellenőrzése
Annak ellenőrzéséhez, hogy az ügynök az Azure-ban látható-e, kövesse az alábbi lépéseket.

1. Jelentkezzen be az Azure portálra.
1. A bal oldalon válassza az **Azure Active Directory** > **Azure AD Connect lehetőséget.** A központban válassza a **Kiépítés kezelése (előzetes verzió) lehetőséget.**

   ![Azure portál](media/how-to-install/install6.png)</br>

1.  Az **Azure AD-kiépítés (előzetes verzió)** képernyőn válassza **az Összes ügyintéző áttekintése**lehetőséget.

    ![Az összes ügyintézői beállítás áttekintése](media/how-to-install/install7.png)</br>
 
1. A **helyszíni létesítési ügynökök** képernyőn láthatja a telepített ügynököket. Ellenőrizze, hogy a kérdéses ügynök *active*létezik-e, és aktív-e.

   ![Helyszíni létesítési ügynökök képernyő](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>A port ellenőrzése
Annak ellenőrzéséhez, hogy az Azure figyel-e a 443-as porton, és hogy az ügynök kommunikálhat-e vele, kövesse az alábbi lépéseket.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ez a teszt ellenőrzi, hogy az ügynökök kommunikálhatnak-e az Azure-ral a 443-as porton keresztül. Nyisson meg egy böngészőt, és lépjen az előző URL-címre abból a kiszolgálóból, amelyen az ügynök telepítve van.

![A kikötői elérhetőség ellenőrzése](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>A helyi kiszolgálón
Az ügynök futásának ellenőrzéséhez kövesse az alábbi lépéseket.

1.  Jelentkezzen be a kiszolgálóra rendszergazdai fiókkal.
1.  **Nyissa meg** a Szolgáltatásokat úgy, hogy rá navigál, vagy a **Start** > **Run** > **Services.msc**.
1.  A **Szolgáltatások csoportban**győződjön meg arról, hogy a **Microsoft Azure AD Connect Agent Updater** és a **Microsoft Azure AD Connect provisioning Agent** ott van, és állapotuk *fut.*

    ![Szolgáltatások képernyő](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Az ügynök telepítve van, de konfigurálni és engedélyezni kell, mielőtt elkezdené a felhasználók szinkronizálását. Új ügynök konfigurálásához olvassa [el Az Azure AD Connect felhőalapú kiépítési konfigurációjának létrehozása című témakört.](how-to-configure.md)



## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
 
