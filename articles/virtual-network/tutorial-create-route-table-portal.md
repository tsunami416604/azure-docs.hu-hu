---
title: "Hálózati forgalmat - az Azure portálon |} Microsoft Docs"
description: "Útmutató a hálózati forgalom irányításához egy útválasztási táblázathoz, az Azure portál használatával."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 45b07c6ca86802d0cc3e773234e1122ba7bd9ea7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Hálózati forgalom egy útválasztási táblázathoz, az Azure portál használatával

Az Azure automatikusan útvonalak forgalom alapértelmezés szerint egy virtuális hálózatban található alhálózatok között. A saját Azure felülbírálására útvonalak létrehozása alapértelmezett útválasztási. Képes létrehozni az egyéni útvonalak akkor hasznos, ha például azt szeretné, a hálózati virtuális készülék (NVA) keresztül alhálózatok közötti forgalom irányítására. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy útválasztási táblázatot
> * Útvonal létrehozása
> * Hozzon létre egy virtuális hálózatot, több alhálózattal
> * Társítson egy útválasztási táblázatot az alhálózathoz
> * Hozzon létre, amely irányítja a forgalmat NVA
> * Virtuális gépek (VM) üzembe helyezés különböző alhálózatokon
> * Irányíthatja a forgalmat a egyik alhálózatról a másikra NVA keresztül

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure portálon, a http://portal.azure.com.

## <a name="create-a-route-table"></a>Hozzon létre egy útválasztási táblázatot

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **útvonaltábla**.
3. Adja meg, vagy válassza ki, a következő információkat, fogadja el a többi beállítás az alapértelmezett, és válassza **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myRouteTablePublic|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport | Válassza ki **hozzon létre új** , és írja be *myResourceGroup*.|
    |Hely|USA keleti régiója|
 
    ![Az útvonaltábla létrehozása](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Útvonal létrehozása

1. Az a *keresést az erőforrások, a szolgáltatások és a dokumentumok* a portál felső mezőbe írja be a szöveget *myRouteTablePublic*. Ha **myRouteTablePublic** megjelenik a keresési eredmények között, jelölje be.
2. A **beállítások**, jelölje be **útvonalak** , és válassza **+ Hozzáadás**, az alábbi ábrán látható módon:

    ![Útvonal hozzáadása](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. A **útvonal hozzáadása**, adja meg, vagy válassza ki, a következő információkat, fogadja el az alapértelmezett beállítása a többi beállítást, és válassza **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Útvonal neve|ToPrivateSubnet|
    |Címelőtag| 10.0.1.0/24|
    |A következő ugrás típusa | Válassza ki **virtuális készülék**.|
    |A következő ugrás címe| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Társítson egy útválasztási táblázatot az alhálózathoz

Ahhoz, hogy társíthasson egy útválasztási táblázatot az alhálózathoz, létre kell hoznia egy virtuális hálózatot és az alhálózatot, majd alhálózathoz útvonaltábla társíthat:

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. A **virtuális hálózat létrehozása**, adja meg, vagy válassza ki, a következő információkat, fogadja el az alapértelmezett beállítása a többi beállítást, és válassza **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork|
    |Címtér| 10.0.0.0/16|
    |Előfizetés | Válassza ki előfizetését.|
    |Erőforráscsoport|Válassza ki **meglévő** majd **myResourceGroup**.|
    |Hely|Válassza ki *USA keleti régiója*|
    |Alhálózat neve|Nyilvános|
    |Címtartomány|10.0.0.0/24|
    
4. Az a **keresést az erőforrások, a szolgáltatások és a dokumentumok** a portál felső mezőbe írja be a szöveget *myVirtualNetwork*. Ha **myVirtualNetwork** megjelenik a keresési eredmények között, jelölje be.
5. A **beállítások**, jelölje be **alhálózatok** , és válassza **+ alhálózati**, az alábbi ábrán látható módon:

    ![Alhálózat hozzáadása](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Válasszon ki vagy adja meg a következő adatokat, majd válassza ki **OK**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Saját|
    |Címtér| 10.0.1.0/24|

7. Hajtsa végre a lépéseket 5 és 6 újra, a következő adatokat:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|DMZ|
    |Címtér| 10.0.2.0/24|

8. A **myVirtualNetwork - alhálózatok** párbeszédpanel jelenik meg az előző lépés befejezése után. A **beállítások**, jelölje be **alhálózatok** majd **nyilvános**.
9. Az alábbi képen is látható, akkor jelölje ki **útvonaltábla**, jelölje be **MyRouteTablePublic**, majd válassza ki **mentése**:

    ![Az útvonaltábla társítása](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>NVA létrehozása

NVA egy virtuális Gépet egy hálózati funkció, például az útválasztást, firewalling vagy WAN-optimalizálást végző.

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Kiválaszthatja, hogy egy másik operációs rendszer, de a többi lépések azt feltételezik, hogy kiválasztott **Windows Server 2016 Datacenter**. 
3. Válassza ki vagy adja meg a következő információkat **alapjai**, majd jelölje be **OK**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVmNva|
    |Felhasználónév|Adjon meg a nevet.|
    |Jelszó|Adja meg a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza ki **meglévő** majd *myResourceGroup*.|
    |Hely|Válassza ki **USA keleti régiója**.|
4. Válassza ki a Virtuálisgép-méretet a **méret kiválasztása**.
5. Válassza ki vagy adja meg a következő információkat **beállítások**, majd jelölje be **OK**:

    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat|myVirtualNetwork – Ha nincs bejelölve, jelölje be **virtuális hálózati**, majd jelölje be **myVirtualNetwork** alatt **válasszon virtuális hálózati**.|
    |Alhálózat|Válassza ki **alhálózati** majd **DMZ** alatt **alhálózat kiválasztása**. |
    |Nyilvános IP-cím| Válassza ki **nyilvános IP-cím** válassza **nincs** alatt **nyilvános IP-cím kiválasztása**. Nyilvános IP-cím van hozzárendelve a virtuális gép, mert az nem csatlakozik az interneten.
6. A **létrehozása** a a **összegzés**, jelölje be **létrehozása** elindítani a virtuális gép üzembe helyezéséhez.

    A virtuális gép létrehozásához néhány percet vesz igénybe. Ne folytassa a következő lépéssel mindaddig, amíg az Azure létrehozza a virtuális Gépet, és a virtuális gép adatait tartalmazó megnyitása.

7. A mezőbe, a virtuális gép létrehozás után, a megnyitott **beállítások**, jelölje be **hálózati**, majd válassza ki **myvmnva158** (Azure létre hálózati kapcsolat a A virtuális gép rendelkezik után eltérő számú **myvmnva**), az alábbi ábrán látható módon:

    ![Virtuális gépek hálózatkezelése](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. A hálózati adaptert továbbküldhetik hálózati forgalmat küldeni, nem a saját IP-cím, IP-továbbítás engedélyezni kell a hálózati adapter. A **beállítások**, jelölje be **IP-konfigurációk**, jelölje be **engedélyezve** a **IP-továbbítás**, majd válassza ki **mentése** , az alábbi ábrán látható módon:

    ![IP-továbbítás engedélyezése](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépek a virtuális hálózaton, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózati annak biztosítására, hogy a *titkos* keresztül az NVA egy későbbi lépésben. Végezze el az 1-6. lépéseket a [hozzon létre egy NVA](#create-a-network-virtual-appliance). Ugyanazokat a beállításokat használja a következő lépésekben 3. és 5, kivéve az alábbi módosításokat:

|Virtuális gép neve      |Alhálózat      | Nyilvános IP-cím     |
|--------- | -----------|---------              |
| myVmPublic  | Nyilvános     | Fogadja el a portál alapértelmezett |
| myVmPrivate | Saját    | Fogadja el a portál alapértelmezett |

Létrehozhat a *myVmPrivate* VM, amíg az Azure létrehozza a *myVmPublic* virtuális gép. Ne folytassa a következő lépések mindkét virtuális gép létrehozása Azure befejezéséig.

## <a name="route-traffic-through-an-nva"></a>NVA-útvonal forgalmát

1. Az a *keresési* a portál felső mezőbe írja be a szöveget *myVmPrivate*. Ha a **myVmPrivate** VM jelenik meg a keresési eredmények között, válassza ki azt.
2. Távoli asztali kapcsolat létrehozása a *myVmPrivate* kiválasztásával VM **Connect**, az alábbi ábrán látható módon:

    ![Kapcsolódás egy virtuális géphez ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Csatlakoztassa a virtuális Gépet, nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**.
4. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása), Válassza ki **OK**.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** gombra a kapcsolat.
6. Egy későbbi lépésben a tracert.exe parancs segítségével útválasztási tesztelése. Tracert használja az Internet Control üzenet Protocol (ICMP), amely a Windows tűzfalon keresztül megtagadva. A következő parancs beírásával a Powershellből engedélyezze az ICMP Protokollt a Windows tűzfalon keresztül:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Bár tracert ebben a cikkben útválasztási tesztelésére szolgál, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.
7. A virtuális hálózati adapter az Azure-ban az IP-továbbítást engedélyezte [engedélyezése IP fowarding](#enable-ip-forwarding). A virtuális Gépen belül az operációs rendszer és az alkalmazások, a virtuális gépen belül is kell tudni továbbítja a hálózati forgalmat. Az operációs rendszerben az IP-továbbítás engedélyezése a *myVmNva* ; Ehhez hajtsa végre a következő virtuális gép a lépések a *myVmPrivate* VM:

    A távoli asztal a *myVmNva* a parancssorba a következő paranccsal:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Ahhoz, hogy az IP-továbbítás operációs rendszerből, a PowerShellben adja meg a következő parancsot:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Indítsa újra a virtuális Gépet, amely a távoli asztali munkamenet is leválasztja.
8. Közben továbbra is kapcsolódik a *myVmPrivate* VM, hozzon létre egy távoli asztali munkamenetet a *myVmPublic* virtuális gép a következő parancsot, és miután a *myVmNva* virtuális gép újraindul:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    A következő parancs beírásával a Powershellből engedélyezze az ICMP Protokollt a Windows tűzfalon keresztül:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. A hálózati forgalom tesztelése a *myVmPrivate* virtuális gép a *myVmPublic* VM, adja meg a következő parancsot a PowerShell:

    ```
    tracert myVmPrivate
    ```

    A rendszer a választ az alábbi példához hasonló:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Láthatja, hogy az első ugrásnál 10.0.2.4, amely az NVA magánhálózati IP-cím-e. A kétugrásos 10.0.1.4, a magánhálózati IP-címe a *myVmPrivate* virtuális gép. Az útvonal hozzá a *myRouteTablePublic* útvonaltábla és társított a *nyilvános* alhálózati, hogy a forgalmat az NVA keresztül, és nem közvetlenül az Azure a *saját* alhálózat.
10.  Zárja be a távoli asztali munkamenetet a *myVmPublic* VM, így Ön továbbra is kapcsolódik a *myVmPrivate* virtuális gép.
11. A hálózati forgalom tesztelése a *myVmPublic* virtuális gép a *myVmPrivate* VM, adja meg a következő parancsot a parancssorba:

    ```
    tracert myVmPublic
    ```

    A rendszer a választ az alábbi példához hasonló:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Láthatja, hogy továbbítódik közvetlenül a *myVmPrivate* virtuális Gépet a *myVmPublic* virtuális gép. Alapértelmezés szerint az Azure útvonalak forgalom közvetlen alhálózatba.
12. Zárja be a távoli asztali munkamenetet a *myVmPrivate* virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha az erőforráscsoport és a benne található összes erőforrást törli: 

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és az alhálózathoz társított. Létrehozott egy egyszerű NVA portleképezéseit titkos alhálózathoz nyilvános alhálózatból származó forgalmat. Számos előre konfigurált hálózati funkciókat, például a tűzfal és a WAN-optimalizálást végző NVAs telepítése a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Az útvonaltáblák üzemi használatra való telepítése előtt javasoljuk, hogy alaposan feltérképezése [az Azure útválasztási](virtual-networks-udr-overview.md), [kezelése az útvonaltáblák](manage-route-table.md), és [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).


A virtuális hálózaton belül számos Azure-erőforrások telepítése során egyes Azure PaaS szolgáltatások erőforrás nem telepíthető virtuális hálózatba. Továbbra is korlátozzuk néhány Azure PaaS-szolgáltatást csak a virtuális hálózati alhálózat forgalom erőforrásaihoz, ha. A következő oktatóanyag áttekintésével megismerheti, hogyan Azure PaaS erőforrásokhoz való hálózati hozzáférés korlátozása továbblépés.

> [!div class="nextstepaction"]
> [Hálózati hozzáférés korlátozása PaaS erőforrások](virtual-network-service-endpoints-configure.md#azure-portal)
