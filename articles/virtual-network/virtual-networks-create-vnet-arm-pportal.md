---
title: Hozzon létre egy Azure virtuális hálózatra, több alhálózattal - portál |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre virtuális hálózatot, az Azure portál használatával több alhálózattal.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880555"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Hozzon létre egy virtuális hálózatot, az Azure portál használatával több alhálózattal

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások való kommunikációhoz az internetes és közvetlenül egymás mellett. Több alhálózattal a virtuális hálózat létrehozása lehetővé teszi, hogy a hálózati szegmenseket, így szűrheti vagy -vezérlőket a alhálózatok közötti forgalmat. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Alhálózat létrehozása
> * Virtuális gépek közötti hálózati kommunikációhoz tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. Ahogy az alábbi képen is látható, adja meg a *myVirtualNetwork* a **neve**, *10.0.0.0/16* a **Címtéren**,  **myResourceGroup** a **erőforráscsoport**, *nyilvános* alhálózat **neve**, a 10.0.0.0/24 alhálózat **-címtartományt**, jelölje be a **hely** és a **előfizetés**, fogadja el a többi alapértelmezett beállításokat, majd válassza ki **létrehozása**:

    ![Virtuális hálózat létrehozása](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    A **Címtéren** és **-címtartományt** CIDR-formátumban vannak megadva. A megadott **Címtéren** az IP-címek 10.0.0.0-10.0.255.254 tartalmazza. A **címtartomány** meg egy alhálózathoz, belül kell lennie a **Címtéren** a virtuális hálózathoz definiált. Azure DHCP egy alhálózati címtartományt az IP-címet rendel egy alhálózaton üzembe helyezett erőforrás. Azure csak rendel a címek 10.0.0.4-10.0.0.254 belül üzembe helyezett erőforrás a **nyilvános** alhálózatot, mert Azure fenntartja az első négy címeket (10.0.0.0-10.0.0.3 az alhálózat ebben a példában) és a legutóbbi címek () az alhálózat ebben a példában 10.0.0.255-ig) minden alhálózatban.

## <a name="create-a-subnet"></a>Alhálózat létrehozása

1. Az a **keresést az erőforrások, a szolgáltatások és a dokumentumok** a portál felső mezőbe írja be a szöveget *myVirtualNetwork*. Ha **myVirtualNetwork** megjelenik a keresési eredmények között, jelölje be.
2. Válassza ki **alhálózatok** majd **+ alhálózati**, az alábbi ábrán látható módon:

     ![Adjon hozzá egy alhálózatot](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. Az a **alhálózat hozzáadása** megjelenő, a szövegmezőbe írja be *titkos* a **neve**, adja meg *10.0.1.0/24* a **-címtartományt**, majd válassza ki **OK**.  A alhálózati címtartományt a címtartományokat más alhálózatok virtuális hálózaton belülről nem lehet átfedésben. 

Az Azure virtuális hálózatok és alhálózatok üzemi használatra való telepítése előtt javasoljuk, hogy alaposan megismerje a címterület [szempontok](manage-virtual-network.md#create-a-virtual-network) és [virtuális hálózati korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Amennyiben az erőforrások telepítése alhálózatokra, virtuális hálózati és alhálózati módosításokat, például megváltoztatni a címtartományt, alhálózatok belül rendszerbe meglévő Azure-erőforrások újratelepítés lehet szükség.

## <a name="test-network-communication"></a>Tesztelje a hálózati kommunikációban

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások való kommunikációhoz az internetes és közvetlenül egymás mellett. A virtuális hálózatba telepítése erőforrás egy típus egy virtuális gépet. Hozzon létre két virtuális gép a virtuális hálózat, amellyel tesztelheti őket, és az Internet közötti hálózati kommunikációhoz egy későbbi lépésben.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Kiválaszthatja, hogy egy másik operációs rendszer, de a többi lépések azt feltételezik, hogy kiválasztott **Windows Server 2016 Datacenter**. 
3. Válassza ki vagy adja meg a következő információkat **alapjai**, majd jelölje be **OK**:
    - **Név**: *myVmWeb*
    - **Erőforráscsoport**: válasszon **meglévő** majd *myResourceGroup*.
    - **Hely**: válasszon *USA keleti régiója*.

    A **felhasználónév** és **jelszó** meg egy későbbi lépésben használt. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). A **hely** és **előfizetés** kijelölt meg kell egyeznie a hely és a virtuális hálózat előfizetése. Ez nem szükséges, hogy ugyanahhoz az erőforráscsoporthoz tartozik, a virtuális hálózati jött létre, de ugyanabban az erőforráscsoportban van kiválasztva, ez az oktatóanyag a választotta.
4. Válassza ki a Virtuálisgép-méretet a **méret kiválasztása**.
5. Válassza ki vagy adja meg a következő információkat **beállítások**, majd jelölje be **OK**:
    - **Virtuális hálózati**: Gondoskodjon arról, hogy **myVirtualNetwork** van kiválasztva. Ha nem, válassza ki **virtuális hálózati** majd **myVirtualNetwork** alatt **válasszon virtuális hálózati**.
    - **Alhálózati**: Gondoskodjon arról, hogy **nyilvános** van kiválasztva. Ha nem, válassza ki a **alhálózati** , és válassza **nyilvános** alatt **alhálózat kiválasztása**, az alábbi ábrán látható módon:
    
        ![Virtuális gép beállításait](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. A **létrehozása** a a **összegzés**, jelölje be **létrehozása** elindítani a virtuális gépek telepítése során.
7. Fejezze be újra 1-6. lépéseket, de meg *myVmMgmt* a a **neve** a virtuális gép, és válassza ki **titkos** a a **alhálózati**.

A virtuális gépek létrehozása több percig is tarthat. Ne folytassa a hátralévő lépéseket addig mindkét virtuális gépek jönnek létre.

Ez a cikk a létrehozott virtuális gépek rendelkezik ilyennel [hálózati illesztő](virtual-network-network-interface.md) dinamikusan hozzárendelt hálózati illesztő egy IP-címmel. A virtuális gép telepítése után is [több nyilvános és magánhálózati IP-címek hozzáadása vagy módosítása a IP-cím hozzárendelés metódus statikus](virtual-network-network-interface-addresses.md#add-ip-addresses). Is [hozzáadása a hálózati adapterek](virtual-network-network-interface-vm.md#vm-add-nic), akár által támogatott maximális a [Virtuálisgép-méretet](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) válassza, ha egy virtuális gépet hoz létre. Emellett [egygyökerű i/o-virtualizálás (SR-IOV) engedélyezése](create-vm-accelerated-networking-powershell.md) a virtuális gép, de csak akkor, ha egy virtuális gép létrehozásához, amely a funkció támogatja a Virtuálisgép-méretet.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>A virtuális gépek között, és az internetes kommunikáció

1. Az a *keresési* a portál felső mezőbe írja be a szöveget *myVmMgmt*. Ha **myVmMgmt** megjelenik a keresési eredmények között, jelölje be.
2. Távoli asztali kapcsolat létrehozása a *myVmMgmt* virtuális gép kiválasztásával **Connect**, az alábbi ábrán látható módon:

    ![Csatlakozás virtuális géphez](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Csatlakoztassa a virtuális Gépet, nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**.
4. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, ha a megadott hitelesítő adatok megadását, a virtuális gép létrehozása), majd válassza ki **OK**.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** gombra a kapcsolat.
6. Egy későbbi lépésben ping folytatott kommunikációhoz használandó a *myVmMgmt* virtuális gépek létrehozását a *myVmWeb* virtuális gépet. Ping az ICMP, amely alapértelmezés szerint a Windows tűzfalon keresztül megtagadva. A Windows tűzfalon keresztül ICMP engedélyezéséhez az alábbi parancs beírásával a parancssorba:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Bár a ping használatban van ebben a cikkben, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.
7. Biztonsági okokból általában távolról csatlakozhat a virtuális hálózat virtuális gépek számának korlátozása. Ebben az oktatóanyagban a *myVmMgmt* történő felügyeletére szolgál a virtuális gép a *myVmWeb* virtuális gép virtuális hálózatban. A távoli asztal a *myVmWeb* virtuális gépek létrehozását a *myVmMgmt* virtuális gépet, adja meg a következő parancsot a parancssorba:

    ``` 
    mstsc /v:myVmWeb
    ```
8. Való kommunikációra a *myVmMgmt* virtuális gépek létrehozását a *myVmWeb* virtuális gépet, adja meg a következő parancsot a parancssorba:

    ```
    ping myvmmgmt
    ```

    A következő egy példa a kimenetre hasonló kimenetet jelenhet meg:
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    Láthatja, hogy a cím a *myVmMgmt* virtuális gép 10.0.1.4. 10.0.1.4 volt az első elérhető IP-címek tartománya, a *titkos* alhálózati központilag telepített a *myVmMgmt* az előző lépésben a virtuális gép.  Azt látja, hogy a virtuális gép teljesen minősített tartománynevét *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Bár a *dar5p44cif3ulfq00wxznl3i3f* a tartománynév része nem egyezik a virtuális gép, a tartomány nevét az többi részének azonosak. 

    Alapértelmezés szerint az összes Azure virtuális gép használja az alapértelmezett Azure DNS szolgáltatást. Az összes virtuális gép a virtuális hálózaton belül az azonos virtuális hálózatban, Azure alapértelmezett DNS-szolgáltatás segítségével más virtuális gépek képes névfeloldásra. Azure alapértelmezett DNS-szolgáltatás helyett használhatja a saját DNS-kiszolgáló vagy a saját szolgáltatása az Azure DNS-szolgáltatás. További információkért lásd: [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) vagy [titkos tartományok Azure DNS használatával](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Internet Information Services (IIS) telepítése a Windows Server a a *myVmWeb* virtuális gépet, adja meg a következő parancsot egy PowerShell-munkamenetben:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. A telepítés végeztével az IIS, válassza le a *myVmWeb* a távoli asztali munkamenetgazda, így a a *myVmMgmt* távoli asztali munkamenetet. Nyisson meg egy webböngészőt, és keresse meg a http://myvmweb. Az IIS üdvözlőlap láthatja.
11. Válassza le a *myVmMgmt* távoli asztali munkamenetet.
12. A nyilvános IP-címének a a *myVmWeb* virtuális gépet. Azure létrehozásakor a *myVmWeb* virtuális gép, egy nyilvános IP-cím erőforrás nevű *myVmWeb* is létrejött, de a virtuális géphez rendelt. Láthatja, hogy 52.170.5.92 hozzá lett rendelve **nyilvános IP-cím** számára a *myVmMgmt* a képen látható, a 2. lépésben a virtuális gép. Rendelt nyilvános IP-cím kereséséhez a *myVmWeb* virtuális gépet, a Keresés *myVmWeb* a portál keresési mezőbe, majd válassza ki azt a keresési eredmények megjelenésekor.

    Bár a virtuális gép nem kell egy nyilvános IP-címet kap, Azure rendel hozzá egy nyilvános IP-cím minden virtuális gépet hoz létre, alapértelmezés szerint. Útján kommunikálnak az interneten a virtuális gép, egy nyilvános IP-címet kell rendelni a virtuális géphez. Minden virtuális gépek kommunikálhatnak az internettel kimenő, függetlenül attól, egy nyilvános IP-címet a virtuális géphez van rendelve. Az Azure-ban kimenő internetes kapcsolatok kapcsolatos további információkért lásd: [az Azure-ban kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. A saját számítógépen keresse meg a nyilvános IP-címét a *myVmWeb* virtuális gépet. A saját számítógépéről IIS-üdvözli a lapnak a megtekintésére irányuló kísérlet sikertelen lesz. A kísérlet meghiúsul, mert a virtuális gépek üzembe helyezése, amikor Azure alapértelmezés szerint minden egyes virtuális gép hálózati biztonsági csoport létrehozása. 

     Hálózati biztonsági csoport biztonsági szabályokat, amelyek engedélyezik vagy megtagadják a bejövő és kimenő hálózati forgalom port és az IP-címet tartalmazza. Az alapértelmezett hálózati biztonsági csoport létrehozása Azure lehetővé teszi a kommunikációt az azonos virtuális hálózatban lévő erőforrások közötti összes portokon keresztül. Windows virtuális gépek esetében az alapértelmezett hálózati biztonsági csoport megtagadja minden bejövő forgalom az internetről érkező összes portokon, kivéve a TCP-port 3389-es (RDP) keresztül. Ennek eredményeképpen alapértelmezés szerint is RDP közvetlenül a *myVmWeb* virtuális gép az internetről, annak ellenére, hogy nem érdemes port 3389 nyissa meg a webkiszolgálón. Webböngészés 80-as porton keresztül kommunikál, mivel kommunikációs az internetről sikertelen, mert nincs szabály az alapértelmezett hálózati biztonsági csoport átengedi a forgalmat a 80-as porton keresztül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha az erőforráscsoport és a benne található összes erőforrást törli: 

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudta, hogyan telepíthet egy virtuális hálózatot, több alhálózattal. Is megtanulta, hogy egy Windows rendszerű virtuális gép létrehozásakor az Azure létrehoz egy hálózati adapteren, hogy azt a virtuális gép csatlakozik, és létrehoz egy hálózati biztonsági csoportot, amely csak forgalom 3389-es porton keresztül az internetről. A következő oktatóanyag áttekintésével megismerheti, hogyan alhálózatokra, és nem az egyes virtuális gépek hálózati forgalom szűrésére továbblépés.

> [!div class="nextstepaction"]
> [Hálózati forgalom szűrésére alhálózathoz](./virtual-networks-create-nsg-arm-pportal.md)
