---
title: Oktatóanyag – Az alhálózati forgalom útválasztásának konfigurálása az Azure Traffic Managerrel
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a Traffic Manager t a felhasználói alhálózatokból származó forgalom adott végpontokra történő irányítására.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: rohink
ms.openlocfilehash: 49e0bce6eea8fac32f49bb905c225e898e709af0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77136288"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Oktatóanyag: A Traffic Manager használatával a felhasználói alhálózat alapján adott végpontokra irányíthatja a forgalmat

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

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Ha működés közben szeretné megtekinteni a Traffic Managert, az oktatóanyaghoz a következőket kell üzembe helyeznie:

- két alapszintű webhely, amelyek eltérő Azure-régiókban futnak – **az USA keleti régiója** (belső webhely) és **Nyugat-Európa** (éles üzemű webhely).
- két virtuális tesztgép a Traffic Manager teszteléséhez – egy **az USA keleti régiójában** és egy **Nyugat-Európában**.

A virtuális tesztgépekkel mutatjuk be, hogy a Traffic Manager hogyan irányítja a felhasználói forgalmat a belső vagy az éles üzemű webhelyre a felhasználói lekérdezés alhálózata alapján.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozása a következő lépéseket foglalja magában:

1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához – egyet **az USA keleti régiójában**, egyet pedig **Nyugat-Európában**.
2. Telepítse az IIS-kiszolgálót mindkét virtuális gépen, és frissítse az alapértelmezett webhelylapot, amely ismerteti azon virtuális gép nevét, amelyhez a felhasználó csatlakozik a webhely megnyitásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához

Ebben a szakaszban két virtuális gépet hoz létre *myIISVMEastUS* és *myIISVMWestEurope* az **USA keleti** és **nyugat-európai** Azure-régióban.

1. Az Azure Portal felső, bal sarkában válassza az Erőforrás > létrehozása**Windows** > **Server 2019 Datacenter** **lehetőséget.**
2. A **Virtuális gép létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:

   - **Előfizetési** > **erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** majd írja be a **myResourceGroupTM1 parancsot.**
   - **Példány részletei** > **Virtuális gép neve**: Írja be a *myIISVMEastUS*nevet.
   - **Példány részleteirégió** > **Region**: Válassza az USA **keleti régiója**lehetőséget.
   - **Rendszergazdai fiók** > **felhasználóneve**: Adja meg az Ön által választott felhasználónevet.
   - **Rendszergazdai fiók** > **jelszava:** Adja meg az Ön által választott jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portszabályok** > **Nyilvános bejövő portok**: Válassza a Kijelölt **portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok:** > **Bejövő portok kiválasztása:** Válassza az **RDP** és **a HTTP elemet** a legördülő mezőben.

3. Válassza a **Kezelés** lapot, vagy válassza a **Tovább: Lemezek**, majd **a Tovább: Hálózat**, majd a **Tovább: Kezelés**lehetőséget. A **Figyelés**csoportban állítsa a **Rendszerindításdiadia -t** **Ki**beállításra.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás gombra.**  
6. A következő lépéseket követve hozzon létre egy második virtuális gép nevű *myIISVMWestEurope*, egy **erőforrás csoport** neve *myResourceGroupTM2*, a **helyét** *Nyugat-Európa*, és az összes többi beállítás ugyanaz, mint *myIISVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban telepíti az IIS-kiszolgálót a két virtuális gépre - *a myIISVMEastUS* & *myIISVMWestEurope-ra,* majd frissíti az alapértelmezett webhelyoldalt. Az egyéni webhelylap azon virtuális gép nevét jeleníti meg, amelyhez Ön csatlakozik a webhely böngészőbeli megnyitásakor.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet.
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Válassza **az OK gombot.**
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán keresse meg a **Windows felügyeleti eszközök**>**kiszolgálókezelőjét.**
7. Indítsa el a Windows PowerShell t a VM *myIISVMEastUS*rendszeren, és használja a következő parancsokat az IIS-kiszolgáló telepítéséhez és az alapértelmezett htm fájl frissítéséhez.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Zárja be az RDP-kapcsolatot a *myIISVMEastUS* virtuális géptel.
9. Ismételje meg *az* 1-6. *myIISVMWestEurope*
10. Indítsa el a Windows PowerShellt a *myIISVMWestEurope* virtuális gépen, és használja a következő parancsokat az IIS-kiszolgáló telepítéséhez és az alapértelmezett htm fájl frissítéséhez.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban konfigurálja az IIS-kiszolgálók DNS-neveit - *myIISVMEastUS* és *myIISVMWestEurope*.

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd válassza a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet az erőforrások listájában.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet, majd válassza a **Mentés** lehetőséget.
4. Ismételje meg *az* 1-3. *myIISVMWestEurope*

### <a name="create-test-vms"></a>Virtuális tesztgépek létrehozása

Ebben a szakaszban virtuális gép *(myVMEastUS* és *myVMWestEurope)* létrehozása minden Azure-régióban **(USA keleti régiója** és **Nyugat-Európa ).** Ezekkel a virtuális gépeksegítségével tesztelheti, hogy a Traffic Manager hogyan irányítja a felhasználói forgalmat a felhasználó lekérdezésének alhálózata alapján.

1. Az Azure Portal felső, bal sarkában válassza az Erőforrás > létrehozása**Windows** > **Server 2019 Datacenter** **lehetőséget.**
2. A **Virtuális gép létrehozása**csoportban írja be vagy válassza ki a következő értékeket az **Alapok** lapon:

   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupTM1 lehetőséget.**
   - **Példány részletei** > **Virtuális gép neve**: Írja be a *myVMEastUS*nevet.
   - **Példány részleteirégió** > **Region**: Válassza az USA **keleti régiója**lehetőséget.
   - **Rendszergazdai fiók** > **felhasználóneve**: Adja meg az Ön által választott felhasználónevet.
   - **Rendszergazdai fiók** > **jelszava:** Adja meg az Ön által választott jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portszabályok** > **Nyilvános bejövő portok**: Válassza a Kijelölt **portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok** > **Bejövő portok kiválasztása:** Válassza az **RDP** elemet a legördülő mezőben.

3. Válassza a **Kezelés** lapot, vagy válassza a **Tovább: Lemezek**, majd **a Tovább: Hálózat**, majd a **Tovább: Kezelés**lehetőséget. A **Figyelés**csoportban állítsa a **Rendszerindításdiadia -t** **Ki**beállításra.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás gombra.**  
6. A lépéseket követve hozzon létre egy második virtuális gép nevű *myVMWestEurope*, egy **erőforrás csoport** neve *myResourceGroupTM2*, **a helyét** *Nyugat-Európa*, és az összes többi beállítás ugyanaz, mint *myVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Létrehozhat egy Traffic Manager-profilt, amellyel a kérés forrás IP-címe alapján adott végpontokat adhat vissza.

1. A képernyő bal felső részén válassza az **Erőforrás** > létrehozása Hálózati**forgalomkezelő létrehozása** > csoport létrehozása : Create (Erőforrás**létrehozása: Forgalomkezelő** > **létrehozása) lehetőséget.**
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Név                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza a **Subnet** útválasztási metódust.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő** lehetőséget, majd írja be a *myResourceGroupTM1* kifejezést. |
    | |                              |
    |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az IIS-kiszolgálókat futtató két virtuális gépet - *a myIISVMEastUS* & *myIISVMWestEurope-ot* a felhasználói forgalom nak a felhasználó lekérdezésalhálózata alapján való irányításához.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Írja be vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd kattintson az **OK gombra:**

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Név           | myInternalWebSiteEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Válasszon nyilvános IP-címet**, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a *myIISVMEastUS-ip* nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Alhálózat útválasztási beállításai    |   Adja hozzá a *myVMEastUS* teszt virtuális gép IP-címét. Az ebből a virtuális gépből származó felhasználói lekérdezések a *myInternalWebSiteEndpoint*-hoz lesznek irányítva.    |

4. Ismételje meg a *2.* *myProdWebsiteEndpoint* *myIISVMWestEurope-ip* **Az Alhálózati útválasztási beállításokhoz**adja meg a teszt virtuális gép - *myVMWestEurope*IP-címét. Az erről a virtuális tesztgépről érkező felhasználói lekérdezések a *myProdWebsiteEndpoint* végpontra lesznek irányítva.
5. Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban tesztelheti, hogyan irányítja a Traffic Manager egy adott alhálózat felhasználói forgalmát egy adott végpontba. A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:

1. Határozza meg a Traffic Manager-profilja DNS-nevét.
2. A Traffic Manager megtekintése működés közben:
    - Az **USA keleti régiójában,** webböngészőben található tesztvirtuális gépből *(myVMEastUS)* keresse meg a Traffic Manager-profil DNS-nevét.
    - A **nyugat-európai** régióban, webböngészőben található teszt virtuális gép *(myVMWestEurope)* böngészőből keresse meg a Traffic Manager-profil DNS-nevét.

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
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania.
4. Válassza **az OK gombot.**
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A *myVMEastUS* virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Mivel a VM *myVMEastUS* IP-cím a *myInternalWebsiteEndpoint*végponthoz van társítva, a webböngésző elindítja a Test weboldal szerverét - *myIISVMEastUS*.

7. Ezután csatlakozzon a **Nyugat-Európában** található VM *myVMWestEurope-hoz* az 1-5 lépésekkel, és keresse meg a Traffic Manager profil tartománynevét ebből a virtuális gépből. Mivel a VM *myVMWestEurope* IP-cím a *myProductionWebsiteEndpoint*végponthoz kapcsolódik, a webböngésző elindítja a Test weboldal szerverét - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése

Ha már nincs rájuk szüksége, törölje az erőforráscsoportokat (**ResourceGroupTM1** és **ResourceGroupTM2**). Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).
