---
title: Alhálózati forgalom útválasztási módszerének konfigurálása az Azure Traffic Managerrel | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy hogyan konfigurálhatja úgy a Traffic Managert, hogy a felhasználói alhálózatok forgalmát adott végpontokba irányítsa.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 20c34b820eb326a18be1c4298b0850a58599be64
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956234"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Forgalom adott végpontokba történő irányítása felhasználói alhálózatok alapján a Traffic Managerben

Ez a cikk ismerteti, hogy hogyan konfigurálhatja a forgalom-útválasztási metódust. A **Subnet** forgalom-útválasztási metódussal IP-címtartományok egy készletét képezheti le adott végpontokra, így amikor a Traffic Manager egy kérést kap, megvizsgálja annak forrás IP-címét, és visszaadja a hozzá tartozó végpontot. 

Ebben az oktatóanyagban – a felhasználói lekérdezés IP-címétől függően – egy belső vagy egy éles üzemű webhelyre irányítjuk a forgalmat alhálózati útválasztással.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két, egy alapszintű webhelyet futtató virtuális gép létrehozása az IIS-en
> * Két virtuális tesztgép létrehozása a Traffic Manager működés közbeni megtekintéséhez
> * Az IIS-t futtató virtuális gépek DNS-nevének konfigurálása
> * Egy forgalom-útválasztási Traffic Manager-profil létrehozása a felhasználó alhálózata alapján
> * Virtuálisgép-végpontok hozzáadása a Traffic Manager-profilhoz
> * A Traffic Manager megtekintése működés közben


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Ha működés közben szeretné megtekinteni a Traffic Managert, az oktatóanyaghoz a következőket kell üzembe helyeznie:
- két alapszintű webhely, amelyek eltérő Azure-régiókban futnak – **az USA keleti régiója** (belső webhely) és **Nyugat-Európa** (éles üzemű webhely).
- két virtuális tesztgép a Traffic Manager teszteléséhez – egy **az USA keleti régiójában** és egy **Nyugat-Európában**. 

A virtuális tesztgépekkel mutatjuk be, hogy a Traffic Manager hogyan irányítja a felhasználói forgalmat a belső vagy az éles üzemű webhelyre a felhasználói lekérdezés alhálózata alapján.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozása a következő lépéseket foglalja magában:
1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához – egyet **az USA keleti régiójában**, egyet pedig **Nyugat-Európában**.
2. Telepítse az IIS-kiszolgálót mindkét virtuális gépen, és frissítse az alapértelmezett webhelylapot, amely ismerteti azon virtuális gép nevét, amelyhez a felhasználó csatlakozik a webhely megnyitásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához
Ebben a szakaszban két virtuális gépet hoz létre az **USA keleti régiója** és **Nyugat-Európa** Azure-régióiban, *InternalWebsite* és *ProdWebsite* néven.

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 VM** lehetőséget.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|InternalWebsite|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza az **Új** lehetőséget, majd írja be a *myResourceGroupTM1* nevet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|
    |||
4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| Válassza a **Virtuális hálózat** elemet a **Virtuális hálózat létrehozása** területen. A **Név** szakaszban adja meg a *myVNet1* kifejezést, az alhálózat nevének pedig a  *mySubnet* kifejezést.|
    |Hálózati biztonsági csoport|Válassza az **Alapszintű** lehetőséget, majd a **Nyilvános bejövő portok kiválasztása** legördülő menüben válassza a **HTTP** és az **RDP** lehetőségeket |
    |Rendszerindítási diagnosztika|Válassza a **Letiltva** lehetőséget.|
    |||
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. Végezze el ismét az 1–6. lépést az alábbi módosításokkal:

    |Beállítás|Érték|
    |---|---|
    |Erőforráscsoport | Válassza az **Új** lehetőséget, majd írja be a *myResourceGroupTM2* nevet.|
    |Hely|Nyugat-Európa|
    |Virtuális gép neve | ProdWebsite|
    |Virtuális hálózat | Válassza a **Virtuális hálózat** elemet a **Virtuális hálózat létrehozása** területen. A **Név** szakaszban adja meg a *myVNet2* kifejezést, az alhálózat nevének pedig a  *mySubnet* kifejezést.|
    |||
8. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban telepíti az IIS-kiszolgálót a két virtuális gépen (*myIISVMEastUS*  & *myIISVMWEurope*), majd frissíti az alapértelmezett webhelylapot. Az egyéni webhelylap azon virtuális gép nevét jeleníti meg, amelyhez Ön csatlakozik a webhely böngészőbeli megnyitásakor.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
7. Indítsa el a Windows PowerShellt az *InternalWebsite* virtuális gépen, és a következő parancsok használatával telepítse az IIS-kiszolgálót, valamint frissítse az alapértelmezett htm-fájlt.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```
8. Szüntesse meg az *InternalWebsite* RDP-kapcsolatát.
9. Ismételje meg az 1–6. lépést. Ehhez hozzon létre egy RDP-kapcsolatot a *ProdWebsite* virtuális géppel a *myResourceGroupTM2* erőforráscsoportban, és telepítse az IIS-t, valamint szabja testre az alapértelmezett weboldalt.
10. Indítsa el a Windows PowerShellt a *ProdWebsite* virtuális gépen, és a következő parancsok használatával telepítse az IIS-kiszolgálót, valamint frissítse az alapértelmezett htm-fájlt.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban konfigurálja az IIS-kiszolgálók (*InternalWebsite* és *ProdWebsite*) DNS-neveit.

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd az erőforrások listájában válassza a *myResourceGroupTM1* erőforráscsoportban található *InternalWebsite* elemet.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet, majd válassza a **Mentés** lehetőséget.
4. Ismételje meg az 1–3. lépést a *myResourceGroupTM2* erőforráscsoportban található *ProdWebsite* virtuális géppel is.

### <a name="create-test-vms"></a>Virtuális tesztgépek létrehozása

Ebben a szakaszban mindkét Azure-régióban (**az USA keleti régiója** és **Nyugat-Európa**) létrehoz egy virtuális gépet (*UserVMUS* és *UserVMEurope*). Ezekkel a virtuális gépekkel tesztelheti, hogy a webhely megnyitásakor a Traffic Manager hogyan irányítja át a forgalmat a legközelebbi IIS-kiszolgálónak.

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 Datacenter** lehetőséget.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|*UserVMUS*|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza a **Meglévő**, majd a *myResourceGroupTM1* lehetőséget.|
    |||

4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| Válassza a **Virtuális hálózat** elemet a **Virtuális hálózat létrehozása** területen. A **Név** szakaszban adja meg a *myVNet3* kifejezést, az alhálózat nevének pedig a  *mySubnet3* kifejezést.|
    |Hálózati biztonsági csoport|Válassza az **Alapszintű** lehetőséget, majd a **Nyilvános bejövő portok kiválasztása** legördülő menüben válassza a **HTTP** és az **RDP** lehetőségeket |
    |Rendszerindítási diagnosztika|Válassza a **Letiltva** lehetőséget.|
    |||

6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. Végezze el ismét az 1–5. lépést az alábbi módosításokkal:

    |Beállítás|Érték|
    |---|---|
    |Virtuális gép neve | *UserVMEurope*|
    |Erőforráscsoport | Válassza a **Meglévő** lehetőséget, majd írja be a *myResourceGroupTM2* kifejezést|
    |Virtuális hálózat | Válassza a **Virtuális hálózat** elemet a **Virtuális hálózat létrehozása** területen. A **Név** szakaszban adja meg a *myVNet4* kifejezést, az alhálózat nevének pedig a  *mySubnet4* kifejezést.|
    |||

8. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Létrehozhat egy Traffic Manager-profilt, amellyel a kérés forrás IP-címe alapján adott végpontokat adhat vissza.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** elemet.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:
    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza a **Subnet** útválasztási metódust.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő** lehetőséget, majd írja be a *myResourceGroupTM1* kifejezést. |
    | |                              |
    |
  
    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az IIS-kiszolgálót futtató virtuális gépeket (*InternalWebsite*  & *ProdWebsite*), amelyekkel a felhasználói lekérdezés alhálózata alapján irányíthatja a felhasználói forgalmat.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Name (Név)           | myInternalWebSiteEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Válasszon nyilvános IP-címet**, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza ki az *InternalWebsite-ip* nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Alhálózat útválasztási beállításai    |   Adja hozzá az *UserVMUS* virtuális tesztgép IP-címét. Az erről a virtuális gépről érkező felhasználói lekérdezések az *InternalWebSiteEndpoint* végpontra lesznek irányítva.    |

4. Ismételje meg a 2–3. lépést egy új, *ProdWebsiteEndpoint* nevű végpont a *ProdWebsite-ip* nyilvános IP-címhez való hozzáadásához (ez az IP-cím a *ProdWebsite* nevű IIS-kiszolgáló virtuális gépéhez van társítva). Az **alhálózat útválasztási beállításainál** adja hozzá a virtuális tesztgép (*UserVMEurope*) IP-címét. Az erről a virtuális tesztgépről érkező felhasználói lekérdezések a *myProdWebsiteEndpoint* végpontra lesznek irányítva.
5.  Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

 
## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése
Ebben a szakaszban tesztelheti, hogyan irányítja a Traffic Manager egy adott alhálózat felhasználói forgalmát egy adott végpontba. A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:
1. Határozza meg a Traffic Manager-profilja DNS-nevét.
2. A Traffic Manager megtekintése működés közben:
    - Az **USA keleti régiójában** található virtuális tesztgépről (*UserVMUS*) nyissa meg egy böngészőben a Traffic Manager-profil DNS-nevét.
    - A **Nyugat-Európában** található virtuális tesztgépről (*UserVMEurope*) nyissa meg egy böngészőben a Traffic Manager-profil DNS-nevét.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>A Traffic Manager-profil DNS-nevének meghatározása
Ebben az oktatóanyagban az egyszerűség kedvéért a Traffic Manager-profil DNS-nevét használja a webhelyek megnyitásához. 

A Traffic Manager-profil DNS-nevét a következőképp határozhatja meg:

1.  A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére. Kattintson a Traffic Manager-profilra a megjelenített eredmények között.
1. Kattintson az **Áttekintés** elemre.
2. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Éles környezetekben egy személyes tartománynév konfigurálásával mutathat a Traffic Manager tartománynevére, egy DNS CNAME rekord segítségével.

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
Ebben a szakaszban megtekintheti a Traffic Managert működés közben. 

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myUserVMUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához. 
1. A *UserVMUS* virtuális gépen egy böngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Mivel a *UserVMUS* virtuális gép IP-címe a *myInternalWebsiteEndpoint* végponthoz van társítva, a webböngésző elindítja a tesztwebhely (*InternalWebsite*) kiszolgálóját.

2. Ezután csatlakozzon a **Nyugat-Európában** található, *UserVMEurope* nevű virtuális géphez az 1–5. lépések megismétlésével, majd nyissa meg ezen a virtuális gépen a Traffic Manager-profil tartománynevét. Mivel a *UserVMEurope* virtuális gép IP-címe a *myProductionWebsiteEndpoint* végponthoz van társítva, a webböngésző elindítja a tesztwebhely (*ProdWebsite*) kiszolgálóját. 
  
## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése
Ha már nincs rájuk szüksége, törölje az erőforráscsoportokat (**ResourceGroupTM1** és **ResourceGroupTM2**). Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).


