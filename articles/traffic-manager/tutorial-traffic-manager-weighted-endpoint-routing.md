---
title: 'Oktatóanyag: forgalom átirányítása súlyozott végpontokra – Azure Traffic Manager'
description: Ez az oktatócikk azt ismerteti, hogyan irányíthatja át a forgalmat súlyozott végpontokra a Traffic Manager használatával.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: allensu
ms.openlocfilehash: 45ece08599722e04c4e6799fa5c3589cba1fca42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037918"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Oktatóanyag: A forgalom-útválasztás szabályozása súlyozott végpontokkal a Traffic Manager használatával

Ez az oktatóanyag azt ismerteti, hogyan szabályozhatja a végpontok közötti felhasználói adatforgalom útválasztását az Azure Traffic Managerrel a súlyozott útválasztási móddal. Ezzel az útválasztási móddal a Traffic Manager-profil konfigurációjában súlyt rendel minden végponthoz. A felhasználói adatforgalom ezután az egyes végpontokhoz rendelt súly alapján továbbítódik. A súlyok 1 és 1000 közötti egész számok. Minél nagyobb a végponthoz hozzárendelt súlyérték, annál nagyobb a prioritása.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Két, alapszintű webhelyet futtató virtuális gép létrehozása az IIS-en.
> * Két virtuális tesztgép létrehozása a Traffic Manager működés közbeni megtekintéséhez.
> * Az IIS-t futtató virtuális gépek DNS-nevének konfigurálása.
> * Traffic Manager-profil létrehozása.
> * Virtuálisgép-végpontok hozzáadása a Traffic Manager-profilhoz.
> * A Traffic Manager megtekintése működés közben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Traffic Manager működés közbeni megtekintéséhez helyezze üzembe az alábbiakat ehhez az oktatóanyaghoz:

- Két alapszintű webhelypéldány, amelyek különböző Azure-régiókban futnak: az USA keleti régiójában és Nyugat-Európában.
- Két virtuális tesztgép a Traffic Manager teszteléséhez: egy az USA keleti régiójában, egy pedig Nyugat-Európában. A virtuális tesztgépek annak bemutatására szolgálnak, hogyan irányítja át a Traffic Manager a felhasználói adatforgalmat egy olyan webhelyre, amelynek végpontjához nagyobb súly van hozzárendelve.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Bejelentkezés az [Azure Portalra](https://portal.azure.com).

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozásához hajtsa végre az alábbi lépéseket:

1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához: egyet az USA keleti régiójában, egyet pedig Nyugat-Európában.
2. Telepítsen IIS-kiszolgálót mindkét virtuális gépre. Frissítse az azon virtuális gép nevét leíró alapértelmezett weblapot, amelyhez a felhasználók csatlakoznak a webhely meglátogatásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához

Ebben a szakaszban két virtuális gépet hoz létre (*myIISVMEastUS* és *MYIISVMWESTEUROPE*) az USA keleti régiójában és a Nyugat-európai Azure-régióban.

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása** > **számítási** > a **Windows Server 2019 Datacenter**elemet.
2. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:

   - **Előfizetés** > **erőforráscsoport**: válassza az **új létrehozása** elemet, majd írja be a **myResourceGroupTM1**.
   - **Példány részletei** > **virtuális gép neve**: Type *myIISVMEastUS*.
   - **Példány részletei** > régió: válassza az **USA keleti** **régióját**.
   - **Rendszergazdai fiók** > **Felhasználónév**: adja meg a választott felhasználónevet.
   - **Rendszergazdai fiók** > **jelszó**: adja meg a választott jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portszabályok** > **nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok** > a **bejövő portok kiválasztása**: válassza az **RDP** és a **http** elemet a legördülő listából.

3. Válassza a **kezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**, majd a **Tovább: kezelés**lehetőséget. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.  
6. A következő lépésekkel hozhat létre egy *myIISVMWestEurope*nevű második virtuális gépet a *myResourceGroupTM2*nevű **erőforráscsoport** -névvel, a Nyugat- *Európai* **helyével** és az összes többi beállítással, amely ugyanaz, mint a *myIISVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

![Virtuális gép létrehozása](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>Az IIS telepítése és az alapértelmezett weblap testreszabása

Ebben a szakaszban telepítenie kell az IIS-kiszolgálót a két virtuális gépre myIISVMEastUS és myIISVMWestEurope, majd frissítenie kell az alapértelmezett weblapot. Az egyéni weblap azon virtuális gép nevét jeleníti meg, amelyhez a webhely webböngészőbeli megnyitásakor csatlakozva van.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myIISVMEastUS** erőforrást.
2. Az **Áttekintés** oldalon válassza a **Csatlakozás** elemet. A **Csatlakozás virtuális géphez** területen kattintson az **RDP-fájl letöltése** elemre.
3. Nyissa meg a letöltött .rdp fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek** > **Másik fiók használata** lehetőségre kell kattintania.
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök** > **Kiszolgálókezelő** elemre.
7. Nyissa meg a Windows PowerShellt a VM1 virtuális gépen. Használja a következő parancsokat az IIS-kiszolgáló telepítéséhez és az alapértelmezett .htm fájl frissítéséhez.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Az IIS telepítése és a weblap testreszabása](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Szüntesse meg az RDP-kapcsolatot a **myIISVMEastUS** virtuális géppel.
9. Ismételje meg az 1–8. lépést. Hozzon létre egy RDP-kapcsolatot a virtuálisgép- **myIISVMWestEurope** a **myResourceGroupTM2** erőforráscsoporthoz, és telepítse az IIS-t, és szabja testre az alapértelmezett weblapot.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban a DNS-neveket konfigurálja az IIS-kiszolgálók myIISVMEastUS és myIISVMWestEurope.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myIISVMEastUS** erőforrást.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet. Ezután válassza a **Save** (Mentés) lehetőséget.
4. Ismételje meg a 1-3. lépést a **myIISVMWestEurope** nevű virtuális géphez a **myResourceGroupTM2** erőforráscsoporthoz.

### <a name="create-a-test-vm"></a>Tesztelési virtuális gép létrehozása

Ebben a szakaszban létrehoz egy virtuális gépet (*myVMEastUS* és *myVMWestEurope*) minden egyes Azure-régióban (az**USA keleti** régiójában és **Nyugat-Európában**). Ezeket a virtuális gépeket fogja használni annak tesztelésére, hogy Traffic Manager hogyan irányítja át a forgalmat a webhely-végpontra, amely a magasabb súlyozási értéket használja.

1. A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása** > **számítási** > a **Windows Server 2019 Datacenter**elemet.
2. A **hozzon létre egy virtuális gépet**adja meg a következő értékeket a **alapjai** lapon:

   - **Előfizetés** > **erőforráscsoporthoz**: válassza a **myResourceGroupTM1**lehetőséget.
   - **Példány részletei** > **virtuális gép neve**: Type *myVMEastUS*.
   - **Példány részletei** > régió: válassza az **USA keleti** **régióját**.
   - **Rendszergazdai fiók** > **Felhasználónév**: adja meg a választott felhasználónevet.
   - **Rendszergazdai fiók** > **jelszó**: adja meg a választott jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Bejövő portszabályok** > **nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok** > a **bejövő portok kiválasztása**: válassza az **RDP** elemet a legördülő listából.

3. Válassza a **kezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**, majd a **Tovább: kezelés**lehetőséget. A **figyelés**állítsa be **rendszerindítási diagnosztika** való **ki**.
4. Válassza az **Áttekintés + létrehozás** lehetőséget.
5. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.  
6. A következő lépésekkel hozhat létre egy *myVMWestEurope*nevű második virtuális gépet a *myResourceGroupTM2*nevű **erőforráscsoport** -névvel, a Nyugat- *Európai* **helyével** és az összes többi beállítással, amely ugyanaz, mint a *myVMEastUS*.
7. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager-profilt a **Súlyozott** útválasztási mód alapján.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** elemet.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki a következő információkat. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **Létrehozás** elemet.

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Adjon meg egy egyedi nevet a trafficmanager.net zónában. Ez a trafficmanager.net DNS-nevet eredményezi, amellyel a Traffic Manager-profil elérhető.                                   |
    | Útválasztási metódus          | Válassza a **Súlyozott** útválasztási módot.                                       |
    | Előfizetést            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő használata**, majd a **myResourceGroupTM1** lehetőséget. |
    |        |   |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az IIS-kiszolgálókat futtató két virtuális gépet a myIISVMEastUS és a myIISVMWestEurope, hogy átirányítsa a felhasználói forgalmat.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére. Válassza ki a megjelenített eredmények között a profilt.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában válassza a **Végpontok** > **Hozzáadás** elemet.
3. Adja meg vagy válassza ki a következő információkat. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza az **OK** elemet.

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Adja meg az Azure-végpontot.                                   |
    | Name (Név)           | Adja meg a **myEastUSEndpoint** nevet.                                        |
    | Célerőforrás típusa           | Válassza a **Nyilvános IP-cím** elemet.                          |
    | Célerőforrás          | Válasszon egy nyilvános IP-címet, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a **myIISVMEastUS-ip** nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Tömeg      | Adja meg a **100** értéket.        |
    |        |           |

4. Ismételje meg a 2. és a 3. lépést egy **myWestEuropeEndpoint** nevű másik végpont hozzáadásához a nyilvános IP- **myIISVMWestEurope-IP-** címhez. Ez a címe a myIISVMWestEurope nevű IIS-kiszolgáló virtuális géphez van társítva. A **Súly** mezőben adjon meg **25** értéket.
5. Miután mindkét végpontot hozzáadta, azok megjelennek a Traffic Manager-profil panelen, **Online** figyelési állapottal.

## <a name="test-the-traffic-manager-profile"></a>A Traffic Manager-profil tesztelése

A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:

1. Határozza meg a Traffic Manager-profilja DNS-nevét.
2. A Traffic Manager megtekintése működés közben.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>A Traffic Manager-profil DNS-nevének meghatározása

Ebben az oktatóanyagban az egyszerűség kedvéért a Traffic Manager-profil DNS-nevét használja a webhelyek megnyitásához.

A Traffic Manager-profil DNS-nevét a következőképp határozhatja meg:

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére. Válassza ki a Traffic Manager-profilt a megjelenített eredmények között.
2. Válassza az **Áttekintés** lehetőséget.
3. A Traffic Manager-profil mezőben megjelenik annak DNS-neve. Éles környezetekben egy személyes tartománynév konfigurálásával mutathat a Traffic Manager tartománynevére egy DNS CNAME rekord segítségével.

   ![Traffic Manager DNS-neve](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

Ebben a szakaszban megtekintheti a Traffic Managert működés közben.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myVMEastUS** erőforrást.
2. Az **Áttekintés** oldalon válassza a **Csatlakozás** elemet. A **Csatlakozás virtuális géphez** területen kattintson az **RDP-fájl letöltése** elemre.
3. Nyissa meg a letöltött .rdp fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek** > **Másik fiók használata** lehetőségre kell kattintania.
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A myVMEastUS virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Ekkor a myIISVMEastUS nevű IIS-kiszolgálón üzemeltetett webhelyre kerül, mert nagyobb, **100** értékű súly van hozzárendelve. Az IIS-kiszolgáló myIISVMWestEurope hozzá van rendelve a **25**-ös alsó végponti súlyozási értékhez.

   ![Traffic Manager-profil tesztelése](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Ismételje meg a 1-6-es lépéseket a virtuális gép myVMWestEurope a súlyozott webhely válaszának megtekintéséhez.

## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése

Ha már nincs szüksége az ezen oktatóanyagban létrehozott erőforráscsoportokra, törölheti őket. Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az adatforgalom meghatározott végpontokra való átirányítása a felhasználó földrajzi helye alapján](traffic-manager-configure-geographic-routing-method.md)
