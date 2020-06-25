---
title: Az Azure AD Connect felhőalapú jogosultságkiosztási ügynök telepítése
description: Ez a cikk ismerteti, hogyan telepítheti a Azure AD Connect Cloud kiépítési ügynököt.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f06e9ad03face5082950ecedd3f73b97dc6ae4
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85360928"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Az Azure AD Connect felhőalapú jogosultságkiosztási ügynök telepítése
Ez a dokumentum végigvezeti a Azure Active Directory-(Azure AD-) kapcsolat létesítési ügynökének telepítési folyamatán, és azt, hogy miként konfigurálható először a Azure Portal.

>[!IMPORTANT]
>A következő telepítési utasítások feltételezik, hogy az összes [előfeltétel](how-to-prerequisites.md) teljesült.

Azure AD Connect kiépítés telepítése és konfigurálása a következő lépések végrehajtásával történik:
    
- [Az ügynök telepítése](#install-the-agent)
- [Ügynök telepítésének ellenőrzése](#verify-agent-installation)


## <a name="install-the-agent"></a>Az ügynök telepítése
Az ügynök telepítéséhez kövesse az alábbi lépéseket.

1. Jelentkezzen be arra a kiszolgálóra, amelyet vállalati rendszergazdai engedélyekkel fog használni.
1. Nyissa meg az Azure Portalt. A bal oldalon válassza a **Azure Active Directory**lehetőséget.
1. Válassza a **felügyelet létesítése (előzetes verzió)**  >  **minden ügynök áttekintése**elemet.
1. Töltse le a Azure AD Connect kiépítési ügynököt a Azure Portalból.

   ![Helyszíni ügynök letöltése](media/how-to-install/install9.png)</br>
1. Futtassa az Azure AD Connect kiépítési telepítőt (AADConnectProvisioningAgent. Installer).
1. A **Microsoft Azure ad a kiépítési ügynök csomagjának összekapcsolása** képernyőn fogadja el a licencelési feltételeket, és válassza a **telepítés**lehetőséget.

   ![Microsoft Azure AD a kiépítési ügynök csomagjának összekapcsolására szolgáló képernyő](media/how-to-install/install1.png)</br>

1. A művelet befejeződése után elindul a konfigurációs varázsló. Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.
1. A **Active Directory összekapcsolása** képernyőn válassza a **könyvtár hozzáadása**lehetőséget. Ezután jelentkezzen be Active Directory rendszergazdai fiókjával. Ez a művelet hozzáadja a helyszíni címtárat. Válassza a **Tovább** lehetőséget.

   ![Active Directory képernyő összekötése](media/how-to-install/install3.png)</br>

1. A **konfiguráció befejezése** képernyőn válassza a **Confirm (megerősítés**) lehetőséget. Ez a művelet regisztrálja és újraindítja az ügynököt.

   ![Konfiguráció kész képernyő](media/how-to-install/install4.png)</br>

1. A művelet befejezése után látnia kell, hogy az **ügynök konfigurációjának ellenőrzése sikeres volt.** Válassza a **Kilépés**lehetőséget.

   ![Kilépés gomb](media/how-to-install/install5.png)</br>
1. Ha továbbra is megjelenik a kezdeti Microsoft Azure AD a létesítési **ügynök csomagjának** kiosztása képernyő, válassza a **Bezárás**lehetőséget.

## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Az ügynök ellenőrzése a Azure Portal és az ügynököt futtató helyi kiszolgálón történik.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése
Az alábbi lépéseket követve ellenőrizheti, hogy az ügynök látható-e az Azure-ban.

1. Jelentkezzen be az Azure portálra.
1. A bal oldalon válassza a **Azure Active Directory**  >  **Azure ad Connect**lehetőséget. A központban válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.

   ![Azure Portal](media/how-to-install/install6.png)</br>

1.  Az **Azure ad-kiépítés (előzetes verzió)** képernyőn válassza az **összes ügynök áttekintése**lehetőséget.

    ![Az összes ügynök lehetőség áttekintése](media/how-to-install/install7.png)</br>
 
1. A helyszíni **kiépítési ügynökök** képernyőjén láthatja a telepített ügynököket. Ellenőrizze, hogy a szóban forgó ügynök *aktív*-e, és hogy van-e megjelölve.

   ![Helyszíni kiépítési ügynökök képernyő](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>A helyi kiszolgálón
Az ügynök futtatásának ellenőrzéséhez kövesse az alábbi lépéseket.

1.  Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal.
1.  Nyissa meg a **szolgáltatásokat** vagy navigáljon a szolgáltatásba, vagy **indítsa el**a  >  **Run**  >  **Services. msc**parancsot.
1.  Győződjön meg arról, hogy a **szolgáltatások**területen **Microsoft Azure ad a összekapcsolási ügynök frissítése** és a **Microsoft Azure ad kapcsolat létesítése ügynök** van, és az állapota *fut*.

    ![Szolgáltatások képernyő](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Az ügynök telepítve van, de konfigurálni és engedélyezni kell a felhasználók szinkronizálásának megkezdése előtt. Új ügynök konfigurálásához tekintse meg az [Új konfiguráció létrehozása Azure ad Connect felhőalapú kiépítés számára](how-to-configure.md)című témakört.



## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
 
