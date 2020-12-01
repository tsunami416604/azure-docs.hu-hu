---
title: Az Azure AD Connect felhőalapú jogosultságkiosztási ügynök telepítése
description: Ez a cikk ismerteti, hogyan telepítheti a Azure AD Connect Cloud kiépítési ügynököt.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4ba11dabc8e805f3604afc2fc4388840ac67cf
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348534"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Az Azure AD Connect felhőalapú jogosultságkiosztási ügynök telepítése
Ez a dokumentum végigvezeti a Azure Active Directory-(Azure AD-) kapcsolat létesítési ügynökének telepítési folyamatán, és azt, hogy miként konfigurálható először a Azure Portal.

>[!IMPORTANT]
>A következő telepítési utasítások feltételezik, hogy az összes [előfeltétel](how-to-prerequisites.md) teljesült.

Azure AD Connect kiépítés telepítése és konfigurálása a következő lépések végrehajtásával történik:

- [Csoportosan felügyelt szolgáltatásfiókok](#group-managed-service-accounts) 
- [Az ügynök telepítése](#install-the-agent)
- [Ügynök telepítésének ellenőrzése](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Csoportosan felügyelt szolgáltatásfiókok
A csoportosan felügyelt szolgáltatásfiók egy felügyelt tartományi fiók, amely automatikus jelszavas kezelést, egyszerűsített egyszerű szolgáltatásnév (SPN) felügyeletet, a felügyelet más rendszergazdák számára való delegálását, valamint a funkció több kiszolgálón való kibővítését is lehetővé teszi.  Azure AD Connect a Cloud Sync támogatja és javasolja egy csoportosan felügyelt szolgáltatásfiók használatát az ügynök futtatásához.  További információ a gMSA: [csoportosan felügyelt szolgáltatásfiókok](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Meglévő ügynök frissítése a gMSA-fiók használatára
Ha egy meglévő ügynököt szeretne frissíteni a telepítés során létrehozott gMSA-fiók használatára, egyszerűen frissítse az ügynököt a legújabb verzióra a AADConnectProvisioningAgent.msi futtatásával.  Ezzel a szolgáltatás a legújabb verzióra fog frissülni.  Most futtassa újra a telepítővarázslót, és adja meg a hitelesítő adatokat a fiók létrehozásához, amikor a rendszer kéri.



## <a name="install-the-agent"></a>Az ügynök telepítése
Az ügynök telepítéséhez kövesse az alábbi lépéseket.

 1. Jelentkezzen be arra a kiszolgálóra, amelyet vállalati rendszergazdai engedélyekkel fog használni.
 2. Jelentkezzen be a Azure Portalba, majd lépjen a **Azure Active Directoryra**.
 3. A bal oldali menüben válassza a **Azure ad Connect** lehetőséget.
 4. Válassza a **felügyelet létesítése (előzetes verzió)**  >  **minden ügynök áttekintése** elemet.
 5. Töltse le a Azure AD Connect kiépítési ügynököt a Azure Portalból.
   ![Helyszíni ügynök letöltése](media/how-to-install/install-9.png)</br>
 6. Futtassa a Azure AD Connect kiépítési telepítő AADConnectProvisioningAgent.msi.
 7. A **Microsoft Azure ad a kiépítési ügynök csomagjának összekapcsolása** képernyőn fogadja el a licencelési feltételeket, és válassza a **telepítés** lehetőséget.
   ![Microsoft Azure AD a kiépítési ügynök csomagjának összekapcsolására szolgáló képernyő](media/how-to-install/install-1.png)</br>
 8. A művelet befejeződése után elindul a konfigurációs varázsló. Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.
 9. A **szolgáltatásfiók konfigurálása képernyőn** válassza a **GMSA létrehozása** vagy **az egyéni gMSA használata** lehetőséget.  Ha engedélyezi az ügynök számára, hogy létrehozza a fiókot, a provAgentgMSA $ nevet kapja. Ha az **Egyéni GMSA használata** lehetőséget adja meg, a rendszer kérni fogja a fiók megadását.
 10. Adja meg a tartományi rendszergazdai hitelesítő adatokat a csoportosan felügyelt szolgáltatásfiók létrehozásához, amelyet az ügynök szolgáltatás futtatásához fog használni. Kattintson a **Tovább** gombra.  
   ![GMSA létrehozása](media/how-to-install/install-12.png)</br>
 11. A **Active Directory összekapcsolása** képernyőn válassza a **könyvtár hozzáadása** lehetőséget. Ezután jelentkezzen be Active Directory rendszergazdai fiókjával. Ez a művelet hozzáadja a helyszíni címtárat. 
 12. Ha szeretné, felügyelheti az ügynök által használt tartományvezérlők beállításait, ha kiválasztja a **tartományvezérlő prioritásának kiválasztása** és a tartományvezérlők listájának rendezése lehetőséget.   Kattintson az **OK** gombra.
  ![Tartományvezérlők sorrendje](media/how-to-install/install-2a.png)</br>
 13. Kattintson a **Tovább** gombra.
  ![Active Directory képernyő összekötése](media/how-to-install/install-3a.png)</br>
 14.  Az **ügynök telepítése** képernyőn erősítse meg a beállításokat és a létrehozandó fiókot, majd kattintson a **Confirm (megerősítés**) gombra.
  ![Beállítások megerősítése](media/how-to-install/install-11.png)</br>
 15. A művelet befejezése után látnia kell **az ügynök telepítését.** Válassza a **Kilépés** lehetőséget.
  ![Konfiguráció kész képernyő](media/how-to-install/install-4a.png)</br>
1. Ha továbbra is megjelenik a kezdeti Microsoft Azure AD a létesítési **ügynök csomagjának** kiosztása képernyő, válassza a **Bezárás** lehetőséget.

## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Az ügynök ellenőrzése a Azure Portal és az ügynököt futtató helyi kiszolgálón történik.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése
Az alábbi lépéseket követve ellenőrizheti, hogy az ügynök látható-e az Azure-ban.

1. Jelentkezzen be az Azure Portalra.
1. A bal oldalon válassza a **Azure Active Directory**  >  **Azure ad Connect** lehetőséget. A központban válassza a **felügyelet kiépítés (előzetes verzió)** lehetőséget.

   ![Azure Portal](media/how-to-install/install-6.png)</br>

1.  Az **Azure ad-kiépítés (előzetes verzió)** képernyőn válassza az **összes ügynök áttekintése** lehetőséget.

    ![Az összes ügynök lehetőség áttekintése](media/how-to-install/install-7.png)</br>
 
1. A helyszíni **kiépítési ügynökök** képernyőjén láthatja a telepített ügynököket. Ellenőrizze, hogy a szóban forgó ügynök *aktív*-e, és hogy van-e megjelölve.

   ![Helyszíni kiépítési ügynökök képernyő](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>A helyi kiszolgálón
Az ügynök futtatásának ellenőrzéséhez kövesse az alábbi lépéseket.

1.  Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal.
1.  Nyissa meg a **szolgáltatásokat** vagy navigáljon a szolgáltatásba, vagy **indítsa el** a  >  **Run**  >  **Services. msc** parancsot.
1.  Győződjön meg arról, hogy a **szolgáltatások** területen **Microsoft Azure ad a összekapcsolási ügynök frissítése** és a **Microsoft Azure ad kapcsolat létesítése ügynök** van, és az állapota *fut*.

    ![Szolgáltatások képernyő](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>Az ügynök telepítve van, de konfigurálni és engedélyezni kell a felhasználók szinkronizálásának megkezdése előtt. Új ügynök konfigurálásához tekintse meg az [Új konfiguráció létrehozása Azure ad Connect felhőalapú kiépítés számára](how-to-configure.md)című témakört.




## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
