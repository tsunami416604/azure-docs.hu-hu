---
title: Oktatóanyag – Forgalom átirányítása súlyozott végpontokra az Azure Traffic Managerrel | Microsoft Docs
description: Ez az oktatócikk azt ismerteti, hogyan irányíthatja át a forgalmat súlyozott végpontokra a Traffic Managerrel.
services: traffic-manager
author: KumudD
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: kumud
ms.openlocfilehash: 171a9cfe5b5b63efde31c37cd49ef548f1e4ebcc
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649434"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-using-traffic-manager"></a>Oktatóanyag: A forgalom-útválasztás szabályozása súlyozott végpontokkal a Traffic Manager használatával 

Ez az oktatóanyag azt ismerteti, hogyan szabályozhatja a végpontok közötti felhasználói adatforgalom útválasztását a Traffic Managerrel a súlyozott útválasztási móddal. Ezen útválasztási mód esetében minden egyes végponthoz súlyokat rendelhet hozzá a Traffic Manager profilkonfigurációjában, és a rendszer az egyes végpontokhoz hozzárendelt súlyok alapján irányítja át a felhasználói adatforgalmat. A súlyok 1 és 1000 közötti egész számok. Minél nagyobb a végponthoz hozzárendelt súlyérték, annál nagyobb a prioritása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két, egy alapszintű webhelyet futtató virtuális gép létrehozása az IIS-en
> * Két virtuális tesztgép létrehozása a Traffic Manager működés közbeni megtekintéséhez
> * Az IIS-t futtató virtuális gépek DNS-nevének konfigurálása
> * Traffic Manager-profil létrehozása
> * Virtuálisgép-végpontok hozzáadása a Traffic Manager-profilhoz
> * A Traffic Manager megtekintése működés közben

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Ha működés közben szeretné megtekinteni a Traffic Managert, az oktatóanyaghoz a következőket kell üzembe helyeznie:
- két alapszintű webhelypéldány, amelyek különböző Azure-régiókban futnak – az **USA keleti régiójában** és **Nyugat-Európában**.
- két virtuális tesztgép a Traffic Manager teszteléséhez – egy **az USA keleti régiójában** és egy **Nyugat-Európában**. A virtuális tesztgépek annak bemutatására szolgálnak, hogyan irányítja át a Traffic Manager a felhasználói adatforgalmat egy olyan webhelyre, amelynek végpontjához nagyobb súly van hozzárendelve.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozásához hajtsa végre az alábbi lépéseket:
1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához – egyet **az USA keleti régiójában**, egyet pedig **Nyugat-Európában**.
2. Telepítse az IIS-kiszolgálót mindkét virtuális gépen, és frissítse az alapértelmezett webhelylapot, amely ismerteti azon virtuális gép nevét, amelyhez a felhasználó csatlakozik a webhely megnyitásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához
Ebben a szakaszban két virtuális gépet hoz létre az **USA keleti régiója** és **Nyugat-Európa** Azure-régióiban, *myIISVMEastUS*, illetve *myIISVMWEurope* néven.

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 VM** lehetőséget.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myIISVMEastUS|
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
    |Virtuális gép neve | myIISVMWEurope|
    |Virtuális hálózat | Válassza a **Virtuális hálózat** elemet a **Virtuális hálózat létrehozása** területen. A **Név** szakaszban adja meg a *myVNet2* kifejezést, az alhálózat nevének pedig a  *mySubnet* kifejezést.|
    |||
8. A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

![Virtuális gép létrehozása](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Az IIS telepítése és az alapértelmezett webhely testreszabása

Ebben a szakaszban telepíti az IIS-kiszolgálót a két virtuális gépen (*myIISVMEastUS*  & *myIISVMWEurope*), majd frissíti az alapértelmezett webhelylapot. Az egyéni webhelylap azon virtuális gép nevét jeleníti meg, amelyhez Ön csatlakozik a webhely böngészőbeli megnyitásakor.

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.
6. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök**>**Kiszolgálókezelő** elemre.
7. Indítsa el a Windows PowerShellt a VM1-en, és a következő parancsok használatával telepítse az IIS-kiszolgálót, valamint frissítse az alapértelmezett htm-fájlt.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Az IIS telepítése és a webhely testreszabása](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Szüntesse meg az RDP-kapcsolatot a *myIISVMEastUS* virtuális géppel.
9. Ismételje meg az 1–8. lépést. Ehhez hozzon létre egy RDP-kapcsolatot a *myIISVMWEurope* virtuális géppel a *myResourceGroupTM2* erőforráscsoportban, és telepítse az IIS-t, valamint szabja testre az alapértelmezett weboldalt.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban konfigurálja az IIS-kiszolgálók (*myIISVMEastUS* és *myIISVMWEurope*) DNS-neveit.

1. Kattintson a **Minden erőforrás** elemre a bal oldali menüben, majd válassza a *myResourceGroupTM1* erőforráscsoportban található *myIISVMEastUS* elemet az erőforrások listájában.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet, majd válassza a **Mentés** lehetőséget.
4. Ismételje meg az 1–3. lépést a *myResourceGroupTM1* erőforráscsoportban található *myIISVMWEurope* virtuális géppel is.

### <a name="create-a-test-vm"></a>Tesztelési virtuális gép létrehozása

Ebben a szakaszban egy új virtuális gépet hoz létre *mVMEastUS* néven. Ezzel a virtuális géppel tesztelheti, hogy a Traffic Manager hogyan irányítja át a forgalmat annak a webhelynek a végpontjára, amelynek nagyobb a súlyértéke.

1. Az Azure Portal bal felső sarkában válassza az **Erőforrás létrehozása** > **Számítás** > **Windows Server 2016 VM** lehetőséget.
2. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVMEastUS|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza a **Meglévő**, majd a *myResourceGroupTM1* lehetőséget.|
    |||

4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| Válassza a **Virtuális hálózat** elemet a **Virtuális hálózat létrehozása** területen. A **Név** mezőben adja meg a *myVNet3* kifejezést, alhálózatként pedig a  *mySubnet* kifejezést.|
    |Hálózati biztonsági csoport|Válassza az **Alapszintű** lehetőséget, majd a **Nyilvános bejövő portok kiválasztása** legördülő menüben válassza a **HTTP** és az **RDP** lehetőségeket |
    |Rendszerindítási diagnosztika|Válassza a **Letiltva** lehetőséget.|
    |||

6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.
8. A virtuális gép üzembe helyezése néhány percet vesz igénybe. Csak akkor folytassa a fennmaradó lépésekkel, ha a virtuális gép már létrejött.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Hozzon létre egy Traffic Manager-profilt a **Súlyozott** útválasztási mód alapján.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** elemet.
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza a **Súlyozott** útválasztási módot.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő**, majd a *myResourceGroupTM1* lehetőséget. |
    |        |   |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá a két IIS-kiszolgálót futtató virtuális gépet (*myIISVMEastUS*  & *myIISVMWEurope*) a felhasználói adatforgalom azokon való átirányításához.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Azure-végpont                                   |
    | Name (Név)           | myEastUSEndpoint                                        |
    | Célerőforrás típusa           | Nyilvános IP-cím                          |
    | Célerőforrás          | **Válasszon nyilvános IP-címet**, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a *myIISVMEastUS-ip* nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Tömeg      | Adja meg a **100** értéket.        |
    |        |           |

4. Ismételje meg a 2–3. lépést úgy, hogy hozzáad egy másik, *myWestEuropeEndpoint* nevű végpontot a *myIISVMWEurope-ip* nyilvános IP-címhez (ez az IP-cím a *myIISVMWEurope* nevű, IIS-kiszolgálót futtató virtuális géphez van társítva), **Súlyként** pedig a **25** értéked adja meg. 
5.  Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése
A Traffic Manager működés közbeni megtekintéséhez végezze el a következő lépéseket:
1. Határozza meg a Traffic Manager-profilja DNS-nevét.
2. A Traffic Manager megtekintése működés közben.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>A Traffic Manager-profil DNS-nevének meghatározása
Ebben az oktatóanyagban az egyszerűség kedvéért a Traffic Manager-profil DNS-nevét használja a webhelyek megnyitásához. 

A Traffic Manager-profil DNS-nevét a következőképp határozhatja meg:

1.  A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére. Kattintson a Traffic Manager-profilra a megjelenített eredmények között.
1. Kattintson az **Áttekintés** elemre.
2. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Éles környezetekben egy személyes tartománynév konfigurálásával mutathat a Traffic Manager tartománynevére, egy DNS CNAME rekord segítségével.

   ![Traffic Manager DNS-neve](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
Ebben a szakaszban megtekintheti a Traffic Managert működés közben. 

1. Válassza a **Minden erőforrás** elemet a bal oldali menüben, majd az erőforrások listájában a *myResourceGroupTM1* erőforráscsoportban található *myVMEastUS* elemet.
2. Az **Áttekintés** lapon kattintson a **Csatlakozás** lehetőségre, majd a **Csatlakozás virtuális géphez** területen válassza az **RDP-fájl letöltése** elemet. 
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához. 
6. A *myVMEastUS* virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. A rendszer a *myIISVMEastUS* IIS-kiszolgálón üzemelő webhelyre irányítja át, mivel ahhoz nagyobb (**100** értékű) súly van hozzárendelve a *myIISVMWEurope* IIS-kiszolgálóhoz képest (melyhez alacsonyabb, **25** értékű végpontsúly van hozzárendelve).

   ![Traffic Manager-profil tesztelése](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése
Ha már nincs rá szüksége, törölje az oktatóanyagban létrehozott erőforráscsoportot. Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az adatforgalom meghatározott végpontokra való átirányítása a felhasználó földrajzi helye alapján](traffic-manager-configure-geographic-routing-method.md)


