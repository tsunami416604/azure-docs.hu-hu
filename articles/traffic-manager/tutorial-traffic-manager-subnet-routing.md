---
title: Alhálózat forgalom-útválasztási módszer az Azure Traffic Manager konfigurálása
description: Ez a cikk azt ismerteti, hogy hogyan konfigurálhatja úgy a Traffic Managert, hogy a felhasználói alhálózatok forgalmát adott végpontokba irányítsa.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: da2d4816f3f7a99ac2d213d72d7e801cf630e165
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304935"
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

Ebben a szakaszban két virtuális gép létrehozása *myIISVMEastUS* és *myIISVMWestEurope* a a **USA keleti Régiójában** és **Nyugat-Európa** Azure-régióban.

1. A felső, jelölje be az Azure portál felső részén található left **erőforrás létrehozása** > **számítási** > **Windows Server 2019 Datacenter**.
2. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:

   - **Előfizetés** > **erőforráscsoport**: Válassza ki **új létrehozása** és írja be **myResourceGroupTM1**.
   - **Példány részletei** > **virtuális gép neve**: Típus *myIISVMEastUS*.
   - **Példány részletei** > **régió**:  Válassza az **USA keleti régiója** lehetőséget.
   - **Rendszergazdai fiók** > **felhasználónév**:  Adjon meg egy tetszőleges felhasználónevet.
   - **Rendszergazdai fiók** > **jelszó**:  Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövőport-szabályok** > **nyilvános bejövő portok**: Válassza ki **lehetővé teszi a kiválasztott portok**.
   - **Bejövőport-szabályok** > **bejövő portok kiválasztása**: Válassza ki **RDP** és **HTTP** a listából a lekéréses.

3. Válassza ki a **felügyeleti** lapon, vagy válasszon **tovább: Lemezek**, majd **tovább: Hálózatkezelés**, majd **tovább: Felügyeleti**. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, és kattintson **létrehozás**.  
6. A lépésekkel hozzon létre egy második virtuális Gépet nevű *myIISVMWestEurope*, az egy **erőforráscsoport** neve *myResourceGroupTM2*, amely egy **hely**, *Nyugat-Európa*, és a többi beállítás ugyanaz, mint *myIISVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban az IIS-kiszolgáló telepíthető a két virtuális gép – *myIISVMEastUS* & *myIISVMWestEurope*, majd ezt követően az alapértelmezett webhely oldalt. Az egyéni webhelylap azon virtuális gép nevét jeleníti meg, amelyhez Ön csatlakozik a webhely böngészőbeli megnyitásakor.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet.
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
7. Launch-Windows PowerShell, a virtuális gép *myIISVMEastUS*, és telepítse az IIS-kiszolgálón, és frissíti az alapértelmezett htm-fájl az alábbi parancsok használatával.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Zárja be a RDP-kapcsolatot *myIISVMEastUS* virtuális Gépet.
9. Ismételje meg az 1 – 6 a lépéseket és a virtuális gép RDP-kapcsolatok létrehozásával *myIISVMWestEurope* belül a *myResourceGroupTM2* erőforráscsoportot, amelybe az IIS telepítése és testre szabhatja a saját alapértelmezett weblapot.
10. Launch-Windows PowerShell a *myIISVMWestEurope* a virtuális gép és az alábbi parancsokat az IIS-kiszolgáló telepítése és frissítése az alapértelmezett htm-fájl.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban konfigurálni az IIS-kiszolgálók – DNS-neveit *myIISVMEastUS* és *myIISVMWestEurope*.

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd válassza a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet az erőforrások listájában.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet, majd válassza a **Mentés** lehetőséget.
4. Ismételje meg az 1-3 lépéseket nevű virtuális gép *myIISVMWestEurope* található a *myResourceGroupTM2* erőforráscsoportot.

### <a name="create-test-vms"></a>Virtuális tesztgépek létrehozása

Ebben a szakaszban létrehoz egy virtuális Gépet (*myVMEastUS* és *myVMWestEurope*) minden egyes Azure-régióban (**USA keleti Régiójában** és **Nyugat-Európa**). Ezek a virtuális gépek használatával fogja tesztelni, hogyan irányítja a Traffic Manager a felhasználói forgalom az alhálózat, a felhasználó lekérdezés alapján.

1. A felső, jelölje be az Azure portál felső részén található left **erőforrás létrehozása** > **számítási** > **Windows Server 2019 Datacenter**.
2. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:

   - **Előfizetés** > **erőforráscsoport**: Select **myResourceGroupTM1**.
   - **Példány részletei** > **virtuális gép neve**: Típus *myVMEastUS*.
   - **Példány részletei** > **régió**:  Válassza az **USA keleti régiója** lehetőséget.
   - **Rendszergazdai fiók** > **felhasználónév**:  Adjon meg egy tetszőleges felhasználónevet.
   - **Rendszergazdai fiók** > **jelszó**:  Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövőport-szabályok** > **nyilvános bejövő portok**: Válassza ki **lehetővé teszi a kiválasztott portok**.
   - **Bejövőport-szabályok** > **bejövő portok kiválasztása**: Válassza ki **RDP** a listából a lekéréses.

3. Válassza ki a **felügyeleti** lapon, vagy válasszon **tovább: Lemezek**, majd **tovább: Hálózatkezelés**, majd **tovább: Felügyeleti**. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, és kattintson **létrehozás**.  
6. A lépésekkel hozzon létre egy második virtuális Gépet nevű *myVMWestEurope*, az egy **erőforráscsoport** neve *myResourceGroupTM2*, amely egy **hely** a *Nyugat-Európa*, és a többi beállítás ugyanaz, mint *myVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Létrehozhat egy Traffic Manager-profilt, amellyel a kérés forrás IP-címe alapján adott végpontokat adhat vissza.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** elemet.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási mód          | Válassza a **Subnet** útválasztási metódust.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő** lehetőséget, majd írja be a *myResourceGroupTM1* kifejezést. |
    | |                              |
    |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá a két virtuális gép fut az IIS-kiszolgálók – *myIISVMEastUS* & *myIISVMWestEurope* felhasználói forgalom irányítására az alhálózat, a felhasználó lekérdezés alapján.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Name (Név)           | myInternalWebSiteEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Válasszon nyilvános IP-címet**, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a *myIISVMEastUS-ip* nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Alhálózat útválasztási beállításai    |   Az IP-cím hozzáadásához *myVMEastUS* tesztelése a virtuális gép. A virtuális gépről származó felhasználói lekérdezés a rendszer átirányítja a *myInternalWebSiteEndpoint*.    |

4. Ismételje meg a 2. és 3-nevű másik végpont hozzáadása *myProdWebsiteEndpoint* a nyilvános IP-cím *myIISVMWestEurope-ip* az IIS-kiszolgálón nevű virtuális Géphez társított  *myIISVMWestEurope*. A **alhálózat útválasztási beállítások**, a teszt virtuális gép – IP-cím hozzáadásához *myVMWestEurope*. Az erről a virtuális tesztgépről érkező felhasználói lekérdezések a *myProdWebsiteEndpoint* végpontra lesznek irányítva.
5. Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban tesztelheti, hogyan irányítja a Traffic Manager egy adott alhálózat felhasználói forgalmát egy adott végpontba. A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:

1. Határozza meg a Traffic Manager-profilja DNS-nevét.
2. A Traffic Manager megtekintése működés közben:
    - A teszt virtuális gép a (*myVMEastUS*) található a **USA keleti Régiójában** régió, a webböngésző, keresse meg a DNS-nevével a Traffic Manager-profil.
    - A teszt virtuális gép a (*myVMWestEurope*) található a **Nyugat-Európa** régió, a webböngésző, keresse meg a DNS-nevével a Traffic Manager-profil.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>A Traffic Manager-profil DNS-nevének meghatározása

Ebben az oktatóanyagban az egyszerűség kedvéért a Traffic Manager-profil DNS-nevét használja a webhelyek megnyitásához.

A Traffic Manager-profil DNS-nevét a következőképp határozhatja meg:

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére. Kattintson a Traffic Manager-profilra a megjelenített eredmények között.
2. Kattintson az **Áttekintés** elemre.
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Éles környezetekben egy személyes tartománynév konfigurálásával mutathat a Traffic Manager tartománynevére, egy DNS CNAME rekord segítségével.

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

Ebben a szakaszban megtekintheti a Traffic Managert működés közben.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet.
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A *myVMEastUS* virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Mivel a virtuális gép *myVMEastUS* IP-cím társítva a végpont *myInternalWebsiteEndpoint*, a böngésző elindítja a Test-webhely kiszolgálójának - *myIISVMEastUS*.

7. Ezután csatlakozhat a virtuális gép *myVMWestEurope* található **Nyugat-Európa** lépések használatával 1 – 5, és keresse meg a Traffic Manager-profil tartománynévvel a virtuális gépről. Mivel a virtuális gép *myVMWestEurope* IP-cím társítva a végpont *myProductionWebsiteEndpoint*, a böngésző elindítja a Test-webhely kiszolgálójának - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése

Ha már nincs rájuk szüksége, törölje az erőforráscsoportokat (**ResourceGroupTM1** és **ResourceGroupTM2**). Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
