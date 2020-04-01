---
title: Oktatóanyag:Forgalom irányította a súlyozott végpontokra – Azure Traffic Manager
description: Ez az oktatócikk azt ismerteti, hogyan irányíthatja át a forgalmat súlyozott végpontokra a Traffic Manager használatával.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: rohink
ms.openlocfilehash: a4738b2e36786cd627f53af3e36bd8f1e3fbc375
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939477"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Oktatóanyag: A forgalom-útválasztás szabályozása súlyozott végpontokkal a Traffic Manager használatával

Ez az oktatóanyag azt ismerteti, hogyan szabályozhatja a végpontok közötti felhasználói adatforgalom útválasztását az Azure Traffic Managerrel a súlyozott útválasztási móddal. Ezzel az útválasztási móddal a Traffic Manager-profil konfigurációjában súlyt rendel minden végponthoz. A felhasználói adatforgalom ezután az egyes végpontokhoz rendelt súly alapján továbbítódik. A súlyok 1 és 1000 közötti egész számok. Minél nagyobb a végponthoz hozzárendelt súlyérték, annál nagyobb a prioritása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két, alapszintű webhelyet futtató virtuális gép létrehozása az IIS-en.
> * Két virtuális tesztgép létrehozása a Traffic Manager működés közbeni megtekintéséhez.
> * Az IIS-t futtató virtuális gépek DNS-nevének konfigurálása.
> * Traffic Manager-profil létrehozása.
> * Virtuálisgép-végpontok hozzáadása a Traffic Manager-profilhoz.
> * A Traffic Manager megtekintése működés közben.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A Traffic Manager működés közbeni megtekintéséhez helyezze üzembe az alábbiakat ehhez az oktatóanyaghoz:

- Két alapszintű webhelypéldány, amelyek különböző Azure-régiókban futnak: az USA keleti régiójában és Nyugat-Európában.
- Két virtuális tesztgép a Traffic Manager teszteléséhez: egy az USA keleti régiójában, egy pedig Nyugat-Európában. A virtuális tesztgépek annak bemutatására szolgálnak, hogyan irányítja át a Traffic Manager a felhasználói adatforgalmat egy olyan webhelyre, amelynek végpontjához nagyobb súly van hozzárendelve.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

### <a name="create-websites"></a>Webhelyek létrehozása

Ebben a szakaszban két webhelypéldányt hoz létre, amelyek a két szolgáltatásvégpontot szolgáltatják két Azure-régióban a Traffic Manager-profilnak. A két webhely létrehozásához hajtsa végre az alábbi lépéseket:

1. Hozzon létre kért virtuális gépet egy alapszintű webhely futtatásához: egyet az USA keleti régiójában, egyet pedig Nyugat-Európában.
2. Telepítsen IIS-kiszolgálót mindkét virtuális gépre. Frissítse az azon virtuális gép nevét leíró alapértelmezett weblapot, amelyhez a felhasználók csatlakoznak a webhely meglátogatásakor.

#### <a name="create-vms-for-running-websites"></a>Virtuális gépek létrehozása webhelyek futtatásához

Ebben a szakaszban két virtuális gépet *(myIISVMEastUS* és *myIISVMWestEurope)* hoz létre az USA keleti és nyugat-európai Azure-régióiban.

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

![Virtuális gép létrehozása](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>Az IIS telepítése és az alapértelmezett weblap testreszabása

Ebben a szakaszban telepíti az IIS-kiszolgálót a két virtuális gépre, a myIISVMEastUS és a myIISVMWestEurope rendszerre, majd frissíti az alapértelmezett weblapot. Az egyéni weblap azon virtuális gép nevét jeleníti meg, amelyhez a webhely webböngészőbeli megnyitásakor csatlakozva van.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myIISVMEastUS** erőforrást.
2. Az **Áttekintés** oldalon válassza a **Csatlakozás** elemet. A **Csatlakozás virtuális géphez** területen kattintson az **RDP-fájl letöltése** elemre.
3. Nyissa meg a letöltött .rdp fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.
4. Válassza **az OK gombot.**
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha megjelenik a figyelmeztetés, válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolat folytatásához.
6. A kiszolgáló asztalán keresse meg a **Windows felügyeleti eszközök** > **kiszolgálókezelője**.
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
9. Ismételje meg az 1–8. lépést. Hozzon létre egy RDP-kapcsolatot a VM **myIISVMWestEurope-mal** a **myResourceGroupTM2** erőforráscsoporton belül az IIS telepítéséhez és alapértelmezett weblapjának testreszabásához.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Az IIS-t futtató virtuális gépek DNS-neveinek konfigurálása

A Traffic Manager a szolgáltatásvégpontok DNS-neve alapján irányítja a felhasználói forgalmat. Ebben a szakaszban konfigurálja a myIISVMEastUS és a myIISVMWestEurope IIS kiszolgálók DNS-neveit.

1. Válassza ki a bal oldali menü **Összes erőforrás** elemét. Az erőforrások listájában válassza a **myResourceGroupTM1** erőforráscsoportban a **myIISVMEastUS** erőforrást.
2. Az **Áttekintés** lap **DNS-név** területén válassza a **Konfigurálás** lehetőséget.
3. A **Konfiguráció** lap DNS-név címkéje alatt adjon hozzá egy egyedi nevet. Ezután válassza a **Save** (Mentés) lehetőséget.
4. Ismételje meg az 1-3. **myIISVMWestEurope** **myResourceGroupTM2**

### <a name="create-a-test-vm"></a>Tesztelési virtuális gép létrehozása

Ebben a szakaszban virtuális gép *(myVMEastUS* és *myVMWestEurope)* létrehozása minden Azure-régióban **(USA keleti régiója** és **Nyugat-Európa ).** Ezekkel a virtuális gépek segítségével tesztelheti, hogy a Traffic Manager hogyan irányítja a forgalmat a webhely végpontjára, amely a magasabb súlyértéket rendelkezik.

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

Hozzon létre egy Traffic Manager-profilt a **Súlyozott** útválasztási mód alapján.

1. A képernyő bal felső részén válassza az **Erőforrás** > **létrehozása hálózati** > forgalomkezelő létrehozása csoport létrehozása : Create (Erőforrás létrehozása hálózati forgalomkezelő**létrehozása) című profilja.****Traffic Manager profile** > 
2. A **Traffic Manager-profil létrehozása** területen adja meg vagy válassza ki a következő információkat. Fogadja el a többi beállítás alapértelmezéseit, majd válassza a **Létrehozás gombot.**

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Név                   | Adjon meg egy egyedi nevet a trafficmanager.net zónában. Ez a trafficmanager.net DNS-nevet eredményezi, amellyel a Traffic Manager-profil elérhető.                                   |
    | Útválasztási metódus          | Válassza a **Súlyozott** útválasztási módot.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a **Meglévő használata**, majd a **myResourceGroupTM1** lehetőséget. |
    |        |   |

    ![Traffic Manager-profil létrehozása](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá a myIISVMEastUS és a myIISVMWestEurope IIS kiszolgálókat futtató két virtuális gépet a felhasználói forgalom hozzájuk irányítva.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére. Válassza ki a megjelenített eredmények között a profilt.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában válassza a **Végpontok** > **Hozzáadás** elemet.
3. Adja meg vagy válassza ki a következő információkat. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza az **OK** elemet.

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Adja meg az Azure-végpontot.                                   |
    | Név           | Adja meg a **myEastUSEndpoint** nevet.                                        |
    | Célerőforrás típusa           | Válassza a **Nyilvános IP-cím** elemet.                          |
    | Célerőforrás          | Válasszon egy nyilvános IP-címet, így megjelenítheti az azonos előfizetéshez tartozó, nyilvános IP-címmel rendelkező erőforrások listáját. Az **Erőforrás** területen válassza a **myIISVMEastUS-ip** nevű nyilvános IP-címet. Ez az USA keleti régiójában található IIS-kiszolgáló virtuális gépének nyilvános IP-címe.|
    |  Tömeg      | Adja meg a **100** értéket.        |
    |        |           |

4. Ismételje meg a **2.** **myWestEuropeEndpoint** Ez a cím a myIISVMWestEurope nevű IIS-kiszolgáló virtuális gépéhez van társítva. A **Súly** mezőben adjon meg **25** értéket.
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
3. Nyissa meg a letöltött .rdp fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.
4. Válassza **az OK gombot.**
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha megjelenik a figyelmeztetés, válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolat folytatásához.
6. A myVMEastUS virtuális gépen egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét a webhely megtekintéséhez. Ekkor a myIISVMEastUS nevű IIS-kiszolgálón üzemeltetett webhelyre kerül, mert nagyobb, **100** értékű súly van hozzárendelve. Az IISVMWestEurope IISVMWestEurope iISVMWestEurope kiszolgálóhoz **25**alacsonyabb végpontsúly-érték van rendelve.

   ![Traffic Manager-profil tesztelése](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Ismételje meg az 1-6.

## <a name="delete-the-traffic-manager-profile"></a>A Traffic Manager-profil törlése

Ha már nincs szüksége az ezen oktatóanyagban létrehozott erőforráscsoportokra, törölheti őket. Ehhez válassza ki az erőforráscsoportot (**ResourceGroupTM1** vagy **ResourceGroupTM2**), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az adatforgalom meghatározott végpontokra való átirányítása a felhasználó földrajzi helye alapján](traffic-manager-configure-geographic-routing-method.md)
