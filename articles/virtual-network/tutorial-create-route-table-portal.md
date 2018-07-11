---
title: Hálózati forgalom irányítása – útmutató – Azure Portal | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan irányítható a hálózati forgalom egy útválasztási táblázattal az Azure Portalon.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 81478ace72a538f4970e114cd704fd64ceb94aa6
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344893"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Oktatóanyag: Hálózati forgalom útválasztási táblázattal való irányítása az Azure Portalon

Egy adott virtuális hálózaton belül az Azure alapértelmezés szerint automatikusan elosztja a forgalmat az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Útválasztási táblázat létrehozása
> * Útvonal létrehozása
> * Több alhálózattal rendelkező virtuális hálózat létrehozása
> * Útválasztási táblázat társítása alhálózattal
> * Forgalmat irányító hálózati virtuális berendezés létrehozása
> * Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
> * Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Igény szerint az oktatóanyagot az [Azure CLI](tutorial-create-route-table-cli.md) vagy az [Azure PowerShell](tutorial-create-route-table-powershell.md) használatával is elvégezheti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com címen.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd az **Útválasztási táblázat** elemet.
3. Adja meg vagy válassza ki a következő adatokat, a többi beállításnál fogadja el az alapértelmezett értékeket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myRouteTablePublic|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport | Válassza az **Új létrehozása** elemet, és adja meg a *myResourceGroup* nevet.|
    |Hely|USA keleti régiója|
 
    ![Útválasztási táblázat létrehozása](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Útvonal létrehozása

1. A portál felső részében található *Erőforrások, szolgáltatások és dokumentumok keresése* mezőben kezdje el beírni a *myRouteTablePublic* nevet. Amikor megjelenik a keresési eredmények között, válassza ki a **myRouteTablePublic** elemet.
2. A **BEÁLLÍTÁSOK** menüben válassza ki az **Útvonalak**, majd a **+ Hozzáadás** lehetőséget az alábbi ábrán látható módon:

    ![Útvonal hozzáadása](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. Az **Útvonal hozzáadása** területen adja meg vagy válassza ki a következő adatokat, a többi beállításnál fogadja el az alapértelmezett értékeket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Útvonal neve|ToPrivateSubnet|
    |Címelőtag| 10.0.1.0/24|
    |A következő ugrás típusa | Válassza a **Virtuális berendezés** lehetőséget.|
    |A következő ugrás címe| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Mielőtt hozzárendelhetne egy útválasztási táblázatot egy alhálózathoz, létre kell hoznia egy virtuális hálózatot és egy alhálózatot:

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. A **Virtuális hálózat létrehozása** területen adja meg vagy válassza ki a következő adatokat, fogadja a többi beállításnál fogadja el az alapértelmezett értékeket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork|
    |Címtér| 10.0.0.0/16|
    |Előfizetés | Válassza ki előfizetését.|
    |Erőforráscsoport|Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget.|
    |Hely|Válassza az *USA keleti régiója* lehetőséget.|
    |Alhálózat neve|Nyilvános|
    |Címtartomány|10.0.0.0/24|
    
4. A portál felső részén található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőben kezdje el beírni a *myVirtualNetwork* nevet. Amikor a **myVirtualNetwork** megjelenik a keresési eredmények között, válassza ki.
5. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok**, majd az **+ Alhálózat** lehetőséget, ahogyan az a következő képen látható:

    ![Alhálózat hozzáadása](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Válassza ki vagy adja meg a következő adatokat, és kattintson az **OK** gombra:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Privát|
    |Címtér| 10.0.1.0/24|

7. Végezze el ismét az 5. és 6. lépést a következő adatok megadásával:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|DMZ|
    |Címtér| 10.0.2.0/24|

8. Az előző lépés befejezése után megjelenik a **myVirtualNetwork – Alhálózatok** párbeszédpanel. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet, majd válassza a **Nyilvános** lehetőséget.
9. Ahogy a következő ábra mutatja, válassza az **Útválasztási táblázat** lehetőséget, majd a **MyRouteTablePublic** elemet, és kattintson a **Mentés** gombra:

    ![Útválasztási táblázat hozzárendelése](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Választhat másik operációs rendszert is, de a következő lépések itt azt feltételezik, hogy a **Windows Server 2016 Datacentert** választotta. 
3. Válassza ki vagy adja meg a következő adatokat az **Alapvető beállítások** területen, és kattintson az **OK** gombra:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVmNva|
    |Felhasználónév|Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó|Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza a **Meglévő használata**, majd a *myResourceGroup* lehetőséget.|
    |Hely|Válassza az **USA keleti régiója** lehetőséget.|
4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki vagy adja meg a következő adatokat a **Beállítások** területen, és kattintson az **OK** gombra:

    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat|myVirtualNetwork – Ha még nincs kiválasztva, válassza a **Virtuális hálózat**, elemet, majd a **Válasszon egy virtuális hálózatot** területen a **myVirtualNetwork** elemet.|
    |Alhálózat|Válassza az **Alhálózatok** elemet, majd a **Válasszon egy virtuális hálózatot** területen a **DMZ** lehetőséget. |
    |Nyilvános IP-cím| Válassza a **Nyilvános IP-cím** lehetőséget, majd a **Nyilvános IP-cím választása** területen a **Nincs** elemet. Ehhez a virtuális géphez nem lesz nyilvános IP-cím rendelve, mert nem lehet csatlakozni hozzá az internetről.
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

    A virtuális gép üzembe helyezése néhány percet vesz igénybe. Csak akkor folytassa a következő lépéssel, miután az Azure befejezte a virtuális gép létrehozását, és megjelenített egy ablakot a virtuális gép adataival.

7. A virtuális gép létrehozása után megjelenő ablak **BEÁLLÍTÁSOK** területén válassza a **Hálózatkezelés** lehetőséget, válassza ki a **myvmnva158** elemet (az Azure által a virtuális géphez létrehozott hálózati adapterhez másik szám járul az **myvmnva** után), ahogy a következő ábra mutatja:

    ![Virtuális gépek hálózatkezelése](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Ahhoz, hogy egy hálózati adapter továbbíthassa a neki küldött, de nem a saját IP-címére címzett forgalmat, engedélyeznie kell az IP-továbbítást a hálózati adapteren. A **BEÁLLÍTÁSOK** területen az **IP-konfigurációk** elemet, az **IP-továbbítás** beállításnál válassza az **Engedélyezve** értéket, majd kattintson a **Mentés** gombra, ahogy a következő ábra is mutatja:

    ![IP-továbbítás engedélyezése](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton belül, hogy egy későbbi lépésben ellenőrizhesse, hogy a rendszer a *Nyilvános* alhálózat forgalmát a *Privát* alhálózatra irányítja-e az NVA-n keresztül. Végezze el az [NVA létrehozása](#create-a-network-virtual-appliance) szakasz 1–6. lépését. A 3. és 5. lépésben ugyanazokat a beállításokat használja, a következő kivételekkel:

|Virtuális gép neve      |Alhálózat      | Nyilvános IP-cím     |
|--------- | -----------|---------              |
| myVmPublic  | Nyilvános     | Fogadja el a portál alapértelmezett értékét |
| myVmPrivate | Privát    | Fogadja el a portál alapértelmezett értékét |

Létrehozhatja a *myVmPrivate* virtuális gépet, miközben az Azure létrehozza a *myVmPublic* virtuális gépet. Csak akkor folytassa a következő lépésekkel, ha az Azure mindkét virtuális gép létrehozását befejezte.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

1. Kezdje el begépelni a *myVmPrivate* kifejezést a portál tetején található *keresőmezőbe*. Amikor a **myVmPrivate** virtuális gép megjelenik a keresési eredmények között, válassza ki.
2. Hozzon létre távoli asztali kapcsolatot a *myVmPrivate* virtuális géppel a **Csatlakozás** lehetőség kiválasztásával, ahogy a következő ábra mutatja:

    ![Kapcsolódás egy virtuális géphez ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Nyissa meg a letöltött RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.
4. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.
6. Egy későbbi lépésben az útvonalkövető eszközzel lesz tesztelve az útválasztás. Az útvonalkövetés az ICMP protokollt használja, amelyet a Windows tűzfal letilt. Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPrivate* virtuális gépen a következő PowerShell-parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Habár ebben az oktatóanyagban útvonalkövetést használunk az útválasztás teszteléséhez, az éles környezetekben üzemelő példányok esetében nem ajánlott az ICMP engedélyezése a Windows tűzfalon.
7. Az Azure-ban a virtuális gép hálózati adapterében, az [IP-továbbítás engedélyezése](#enable-ip-forwarding) szakaszban engedélyezte az IP-továbbítást. A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. IP-továbbítás engedélyezése a *myVmNva* virtuális gép operációs rendszerén belül:

    A *myVmPrivate* virtuális gépen egy parancssorból nyisson távoli asztali kapcsolatot a *myVmNva* virtuális géppel:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Az operációs rendszeren belüli IP-továbbítás engedélyezéséhez adja meg a következő parancsot a PowerShellben a *myVmNva* virtuális gépről:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Indítsa újra a *myVmNva* virtuális gépet – ez megszakítja a távoli asztali munkamenetet is.
8. Miközben továbbra is csatlakozik a *myVmPrivate* virtuális géphez, hozzon létre távoli asztali kapcsolatot a *myVmPublic* virtuális géppel, miután a *myVmNva* virtuális gép újraindult:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPublic* virtuális gépen a következő PowerShell-parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. A *myVmPublic* virtuális gépről a *myVmPrivate* virtuális gépre irányuló hálózati forgalom útválasztásának teszteléséhez adja meg a következő parancsot a PowerShellben a *myVmPublic* virtuális gépen:

    ```
    tracert myVmPrivate
    ```

    A válasz a következő példához hasonló:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Láthatja, hogy az első ugrás a 10.0.2.4 cím, amely az NVA magánhálózati IP-címe. A második ugrás a 10.0.1.4 cím – ez a *myVmPrivate* virtuális gép magánhálózati IP-címe. A*myRouteTablePublic* útválasztási táblázathoz hozzáadott és a *Magánjellegű* alhálózathoz rendelt útvonal miatt az Azure az NVA-n keresztül továbbította a forgalmat ahelyett, hogy közvetlenül a *Privát* alhálózatra továbbította volna.
10.  Zárja be a *myVmPublic* virtuális gépre irányuló távoli asztali munkamenetet. Ez nem bontja a *myVmPrivate* virtuális géppel való kapcsolatot.
11. A *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztásának teszteléséhez adja meg a következő parancsot egy parancssorban a *myVmPrivate* virtuális gépen:

    ```
    tracert myVmPublic
    ```

    A válasz a következő példához hasonló:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Láthatja, hogy a rendszer a *myVmPrivate* virtuális gépről közvetlenül a *myVmPublic* virtuális gépre irányítja a forgalmat. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.
12. Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást: 

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) számos előre konfigurált NVA-t helyezhet üzembe, amelyek olyan hálózati funkciókat végeznek, mint például a tűzfal és a WAN-optimalizálás. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).


Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. Ahhoz, hogy megtudja, hogyan korlátozható az Azure PaaS-erőforrásokhoz való hozzáférés, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources.md)
