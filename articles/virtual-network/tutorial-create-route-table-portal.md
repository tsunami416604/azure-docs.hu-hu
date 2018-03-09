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
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Hálózati forgalom egy útválasztási táblázathoz, az Azure portál használatával

Az Azure automatikusan útvonalak forgalom alapértelmezés szerint egy virtuális hálózatban található alhálózatok között. A saját Azure felülbírálására útvonalak létrehozása alapértelmezett útválasztási. Képes létrehozni az egyéni útvonalak akkor hasznos, ha például azt szeretné, a tűzfalon keresztül alhálózatok közötti forgalom irányítására. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy útválasztási táblázatot
> * Útvonal létrehozása
> * Társítsa a virtuális hálózati alhálózat egy útválasztási táblázatot
> * Útválasztás tesztelése
> * Útválasztás hibaelhárítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-route-table"></a>Hozzon létre egy útválasztási táblázatot

Az Azure útvonalak forgalom virtuális hálózatban, alapértelmezés szerint minden alhálózatok között. Azure alapértelmezett útvonalak kapcsolatos további információkért lásd: [rendszerútvonalak](virtual-networks-udr-overview.md). Azure alapértelmezett útválasztási felülbírálását, létrehoz egy útválasztási táblázatot, amely tartalmazza az útvonalak, a virtuális hálózati alhálózat útválasztási táblázatot.

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **útvonaltábla**.
3. Válassza ki a **előfizetés** , és válassza ki vagy adja meg a következő adatokat, majd válassza ki **létrehozása**:
 
    ![Az útvonaltábla létrehozása](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Útvonal létrehozása

Egy útválasztási táblázatot nulla vagy több útvonalat tartalmazza. 

1. Az a *keresést az erőforrások, a szolgáltatások és a dokumentumok* a portál felső mezőbe írja be a szöveget *myRouteTablePublic*. Ha **myRouteTablePublic** megjelenik a keresési eredmények között, jelölje be.
2. A **beállítások**, jelölje be **útvonalak** , és válassza **+ Hozzáadás**, az alábbi ábrán látható módon:

    ![Útvonal hozzáadása](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. A **útvonal hozzáadása**, vagy adja meg a következő adatokat, majd válassza ki és **OK**:
    - **Útvonal neve**: *ToPrivateSubnet*
    - **Címelőtag**: 10.0.1.0/24
    - **A következő ugrás típusa**: válasszon **virtuális készülék**.
    - **Következő ugrási cím**: 10.0.2.4

    Az útvonal átirányítja egy hálózati 10.0.2.4 IP-címmel rendelkező virtuális készüléken keresztül 10.0.1.0/24 címelőtag irányuló teljes forgalomra. A hálózati virtuális készüléket és alhálózat a megadott címelőtag a későbbi lépésekben jönnek létre. Az útvonal Azure alapértelmezett útválasztási, amely irányítja a forgalmat közvetlen alhálózatba felülbírálja. Minden útvonal a következő ugrás típusa határozza meg. A következő ugrás típusa arra utasítja a Azure hogyan kell a forgalmat. Ebben a példában a következő ugrás típusa van *VirtualAppliance*. Azure-ban, és mikor érdemes használni azokat az összes elérhető következő ugrás típusok kapcsolatos további információkért lásd: [a következő ugrás típusa](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Társítson egy útválasztási táblázatot az alhálózathoz

Ahhoz, hogy társíthasson egy útválasztási táblázatot az alhálózathoz, hogy hozzon létre egy virtuális hálózati és alhálózati:

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. A **virtuális hálózat létrehozása**, válassza ki, vagy adja meg a következő adatokat, majd válassza ki **létrehozása**:
    
    - **Név**: *myVirtualNetwork*
    - **Címtér**: *10.0.0.0/16*
    - **Előfizetés**: Jelölje ki az előfizetését.
    - **Erőforráscsoport**: válasszon **meglévő** válassza **myResourceGroup**.
    - **Hely**: válasszon *USA keleti régiója*
    - **Alhálózati név**: *nyilvános*
    - **Címtartomány:** *10.0.0.0/24*

4. Az a **keresést az erőforrások, a szolgáltatások és a dokumentumok** a portál felső mezőbe írja be a szöveget *myVirtualNetwork*. Ha **myVirtualNetwork** megjelenik a keresési eredmények között, jelölje be.
5. Adja hozzá a két további alhálózatokat a virtuális hálózathoz. A **beállítások**, jelölje be **alhálózatok** , és válassza **+ alhálózati**, az alábbi ábrán látható módon:

    ![Alhálózat hozzáadása](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Válasszon ki vagy adja meg a következő adatokat, majd válassza ki **OK**:
    - **Név**: titkos
    - **Címtér**: *10.0.1.0/24*

7. Hajtsa végre a lépéseket 5 és 6 újra, a következő adatokat:
    - **Név**: DMZ
    - **Címtér**: *10.0.2.0/24*

A nulla vagy több alhálózatból egy útválasztási táblázatot lehet társítani. Egy alhálózat állhat nulla vagy egy útválasztási táblázatot társítva. Kimenő forgalom alhálózatból származó Azure alapértelmezett útvonalakat, és minden alhálózathoz társít egy útvonaltábla hozzáadott egyéni útvonalak alapján történik. Társítsa a *myRouteTablePublic* útválasztási táblázatot, hogy a *nyilvános* alhálózati:

1. A **myVirtualNetwork - alhálózatok** párbeszédpanel jelenik meg az előző lépés befejezése után. A **beállítások**, jelölje be **alhálózatok** majd **nyilvános**.
2. Az alábbi képen is látható, akkor jelölje ki **útvonaltábla**, majd jelölje be **MyRouteTablePublic**.

    ![Az útvonaltábla társítása](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. Kattintson a **Mentés** gombra.

## <a name="test-routing"></a>Útválasztás tesztelése

Útválasztás teszteléséhez beállításához hozzon létre virtuális gép számára, hogy az előző lépésben létrehozott útvonal keresztül továbbítja a hálózati virtuális készülék szolgál. Miután létrehozta a virtuális hálózati készüléknek, a virtuális gép telepítésekor a *nyilvános* és *titkos* alhálózatokat. Akkor lesz majd útvonal-forgalom a *nyilvános* alhálózatában ahhoz, hogy a *titkos* keresztül a virtuális hálózati berendezések.

### <a name="create-a-network-virtual-appliance"></a>A hálózati virtuális készülék létrehozása

Az előző lépésben létrehozott egy olyan útvonalat, amely a hálózati virtuális készülék szerepel a következő ugrás típusa. Egy olyan virtuális géphez egy hálózati alkalmazás gyakran nevezik egy virtuális hálózati berendezések. Éles környezetben telepít a hálózati virtuális készülék legtöbbször egy előre konfigurált virtuális gépet. Számos hálózati virtuális készülékek érhetők el a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Ez a cikk egy alapszintű a virtuális gép létrehozása.

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Kiválaszthatja, hogy egy másik operációs rendszer, de a többi lépések azt feltételezik, hogy kiválasztott **Windows Server 2016 Datacenter**. 
3. Válassza ki vagy adja meg a következő információkat **alapjai**, majd jelölje be **OK**:
    - **Név**: *myVmNva*
    - **Erőforráscsoport**: válasszon **meglévő** majd *myResourceGroup*.
    - **Hely**: válasszon *USA keleti régiója*.

    A **felhasználónév** és **jelszó** meg egy későbbi lépésben használt. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). A **hely** és **előfizetés** kijelölt meg kell egyeznie a hely és a virtuális hálózat előfizetése. Ez nem szükséges, hogy ugyanahhoz az erőforráscsoporthoz tartozik, a virtuális hálózati jött létre, de ugyanabban az erőforráscsoportban van kiválasztva, ez az oktatóanyag a választotta.
4. Válassza ki a Virtuálisgép-méretet a **méret kiválasztása**.
5. Válassza ki vagy adja meg a következő információkat **beállítások**, majd jelölje be **OK**:
    - **Virtuális hálózati**: Gondoskodjon arról, hogy **myVirtualNetwork** van kiválasztva. Ha nem, válassza ki a **virtuális hálózati**, majd jelölje be **myVirtualNetwork** alatt **válasszon virtuális hálózati**.
    - **Alhálózati**: válasszon **alhálózati** , és válassza **DMZ** alatt **alhálózat kiválasztása**.
    - **Nyilvános IP-cím**: válasszon **nyilvános IP-cím** válassza **nincs** alatt **nyilvános IP-cím kiválasztása**. Nincs nyilvános IP-cím van rendelve ehhez a virtuális géphez, mert az nem csatlakozik az interneten.
6. A **létrehozása** a a **összegzés**, jelölje be **létrehozása** elindítani a virtuális gépek telepítése során.

A virtuális gép létrehozásához néhány percet vesz igénybe. Ne folytassa a következő lépéssel mindaddig, amíg az Azure létrehozza a virtuális gépet, és egy mezőt nyílik meg a virtuális gép adatait.

A virtuális gép létrehozásakor a Azure azt is létre egy [hálózati illesztő](virtual-network-network-interface.md) a a *DMZ* alhálózat, a hálózati adapter kapcsolódik a virtuális gép. IP-továbbítás engedélyezni kell mindegyik Azure hálózati interfész, amely bármely IP-cím, amely nincs hozzárendelve a hálózati adapter felé forgalmat továbbítja.

1. A mezőbe, a virtuális gép létrehozás után, a megnyitott **beállítások**, jelölje be **hálózati**, majd válassza ki **myvmnva158** (a hálózati kapcsolat Azure a virtuális gépnek egy másik számot után létrehozott **myvmnva**), az alábbi ábrán látható módon:

    ![Virtuálisgép-hálózat](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    A hálózati virtuális készülék létrehozásakor a *DMZ* alhálózati, Azure automatikusan létrehozott hálózati kapcsolat, a hálózati illesztő a virtuális géphez csatolt, és a magánhálózati IP-címmel  *10.0.2.4* a hálózati illesztőhöz. 

2. A **beállítások**, jelölje be **IP-konfigurációk**, jelölje be **engedélyezve** a **IP-továbbítás**, majd válassza ki **mentése** , az alábbi ábrán látható módon:

    ![IP-továbbítás engedélyezése](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Két virtuális gép létrehozása virtuális hálózatban, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózati annak biztosítására, hogy a *titkos* alhálózatot a hálózati virtuális készülék egy későbbi lépésben keresztül.

Végezze el az 1-6. lépéseket a [hozzon létre egy virtuális hálózati berendezések](#create-a-network-virtual-appliance). Ugyanazokat a beállításokat használja a következő lépésekben 3. és 5, kivéve az alábbi módosításokat:

|Virtuális gép   |Name (Név)      |Alhálózat      | Nyilvános IP-cím     |
|---------         |--------- | -----------|---------              |
|1 virtuális gép | myVmWeb  | Nyilvános     | Fogadja el a portál alapértelmezett |
|virtuális gép 2 | myVmMgmt | Saját    | Fogadja el a portál alapértelmezett |

Létrehozhat a *myVmMgmt* virtuális gép, amíg az Azure létrehozza a *myVmWeb* virtuális gépet. Ne folytassa a következő lépések mindkét virtuális gép létrehozása Azure befejezéséig.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>A hálózati virtuális készülék-útvonal forgalmát

1. Az a *keresési* a portál felső mezőbe írja be a szöveget *myVmMgmt*. Ha **myVmMgmt** megjelenik a keresési eredmények között, jelölje be.
2. Távoli asztali kapcsolat létrehozása a *myVmMgmt* virtuális gép kiválasztásával **Connect**, az alábbi ábrán látható módon:

    ![Csatlakozás virtuális géphez](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Csatlakoztassa a virtuális Gépet, nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**.
4. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, ha a megadott hitelesítő adatok megadását, a virtuális gép létrehozása), majd válassza ki **OK**.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** gombra a kapcsolat.
6. Egy későbbi lépésben a tracert.exe parancs segítségével útválasztási tesztelése. Tracert ICMP, amely a Windows tűzfalon keresztül megtagadva használja. A Windows tűzfalon keresztül ICMP engedélyezéséhez az alábbi parancs beírásával a parancssorba:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Bár tracert ebben a cikkben útválasztási tesztelésére szolgál, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.
7. A virtuális gép hálózati adapter az Azure-ban az IP-továbbítást engedélyezte [engedélyezése IP fowarding](#enable-ip-forwarding). A virtuális gépekről az operációs rendszer és a virtuális gépen futó alkalmazások is kell tudni továbbítja a hálózati forgalmat. A hálózati virtuális készülék éles környezetben üzembe helyezésekor a készülék általában szűrők, naplózza, vagy forgalmat előtt néhány egyéb funkciót hajt végre. Ez a cikk azonban az operációs rendszer egyszerűen továbbítja kap az összes forgalom. Az operációs rendszerben az IP-továbbítás engedélyezése a *myVmNva* a következő lépések végrehajtásával a *myVmMgmt* virtuális gépet:

    A távoli asztal a *myVmNva* virtuális gép, amelyet a következő parancsot a parancssorba:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Ahhoz, hogy az IP-továbbítás operációs rendszerből, a PowerShellben adja meg a következő parancsot:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Indítsa újra a virtuális gépet, mely a távoli asztali munkamenet is kapcsolat bontása.
8. Közben továbbra is kapcsolódik a *myVmMgmt* virtuális gépet, utána a *myVmNva* virtuális gép újraindul, hozzon létre egy távoli asztali munkamenetet a *myVmWeb* virtuális gép a következő paranccsal:

    ``` 
    mstsc /v:myVmWeb
    ```
    
    A Windows tűzfalon keresztül ICMP engedélyezéséhez az alábbi parancs beírásával a parancssorba:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. A hálózati forgalom tesztelése a *myVmMgmt* a virtuális gép a *myVmWeb* virtuális gépet, adja meg a következő parancsot a parancssorba:

    ```
    tracert myvmmgmt
    ```

    A rendszer a választ az alábbi példához hasonló:
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Láthatja, hogy az első ugrásnál 10.0.2.4, amely a hálózati virtuális készülék magánhálózati IP-cím-e. A kétugrásos 10.0.1.4, a magánhálózati IP-címe a *myVmMgmt* virtuális gépet. Az útvonal hozzá a *myRouteTablePublic* útvonaltábla és társított a *nyilvános* alhálózati, hogy a forgalmat az NVA keresztül, és nem közvetlenül az Azure a *saját* alhálózat.
10.  Zárja be a távoli asztali munkamenetet a *myVmWeb* virtuális gépet, mely így továbbra is kapcsolódik a *myVmMgmt* virtuális gépet.
11. A hálózati forgalom tesztelése a *myVmWeb* a virtuális gép a *myVmMgmt* virtuális gépet, adja meg a következő parancsot a parancssorba:

    ```
    tracert myvmweb
    ```

    A rendszer a választ az alábbi példához hasonló:

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Láthatja, hogy továbbítódik közvetlenül a *myVmMgmt* a virtuális gépet a *myVmWeb* virtuális gépet. Alapértelmezés szerint az Azure útvonalak forgalom közvetlen alhálózatba.
12. Zárja be a távoli asztali munkamenetet a *myVmMgmt* virtuális gépet.

## <a name="troubleshoot-routing"></a>Útválasztás hibaelhárítása

A korábbi lépésekben megismert Azure érvényes alapértelmezett útvonalakat, szükség esetén felülírható saját útvonalak. Egyes esetekben forgalom irányítása nem feltétlenül, nincs rá szükség. Használhatja a [a következő Ugrás](../network-watcher/network-watcher-check-next-hop-portal.md) Azure hálózati figyelőt annak megállapításához, hogy Azure két virtuális gépek közötti adatforgalom-képességét. 

1. Az a *keresési* a portál felső mezőbe írja be a szöveget *hálózati figyelőt*. Ha **hálózati figyelőt** megjelenik a keresési eredmények között, jelölje be.
2. A **diagnosztikai eszközök**, jelölje be **a következő Ugrás**.
3. A forgalom útválasztásához tesztelése a *myVmWeb* (10.0.0.4) a virtuális gépet a *myVmMgmt* (10.0.1.4) virtuális gépet, jelölje ki az előfizetését, adja meg az alábbi képen látható információk (a **Hálózati illesztő** némileg eltér a nevét), majd válassza ki **a következő Ugrás**:

    ![Következő ugrás](./media/tutorial-create-route-table-portal/next-hop.png)  

    A **eredmény** kimeneti arról tájékoztatja, hogy a következőugrás-forgalmat az IP-cím *myVmWeb* való *myVmMgmt* 10.0.2.4 van (a *myVmNva* virtuális gép), hogy következőugrás-típus *VirtualAppliance*, és hogy van-e az útvonaltáblát az útválasztási okozó *myRouteTablePublic*.

A hatékony útvonalak mindegyik hálózati interfész olyan Azure alapértelmezett útvonalak és bármely útvonalakat adhat meg. Az összes útvonal egy virtuális gép egy adott hálózati csatoló hatékony megtekintéséhez kövesse az alábbi lépéseket:

1. Az a *keresési* a portál felső mezőbe írja be a szöveget *myVmWeb*. Ha **myVmWeb** megjelenik a keresési eredmények között, jelölje be.
2. A **beállítások**, jelölje be **hálózati**, majd válassza ki **myvmweb369** (a hálózati kapcsolat Azure létre a virtuális gép több különböző után**myvmweb**).
3. A **támogatási + hibaelhárítás**, jelölje be **hatékony útvonalak**, az alábbi ábrán látható módon:

    ![Hatékony útvonalak](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Megjelenik az Azure alapértelmezett útvonalak és a hozzáadott útvonal a *myRouteTablePublic* útválasztási táblázatot. Hogyan Azure választja ki egy útvonalat a listából az útvonalak kapcsolatos további információkért lásd: [hogyan Azure egy útvonalat választják ki](virtual-networks-udr-overview.md#how-azure-selects-a-route).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha az erőforráscsoport és a benne található összes erőforrást törli: 

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és az alhálózathoz társított. A hálózati virtuális készülék titkos alhálózathoz nyilvános alhálózatból származó forgalmat portleképezéseit létrehozott. A virtuális hálózaton belül számos Azure-erőforrások telepítése során egyes Azure PaaS szolgáltatások erőforrás nem telepíthető virtuális hálózatba. Továbbra is korlátozzuk néhány Azure PaaS-szolgáltatást csak a virtuális hálózati alhálózat forgalom erőforrásaihoz, ha. A következő oktatóanyag áttekintésével megismerheti, hogyan Azure PaaS erőforrásokhoz való hálózati hozzáférés korlátozása továbblépés.

> [!div class="nextstepaction"]
> [Hálózati hozzáférés korlátozása PaaS erőforrások](virtual-network-service-endpoints-configure.md#azure-portal)
