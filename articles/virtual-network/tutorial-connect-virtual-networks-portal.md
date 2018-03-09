---
title: "Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony - Azure-portál |} Microsoft Docs"
description: "Útmutató: virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítés."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítést az Azure portál használatával

Kapcsolódás virtuális hálózatok egymástól a virtuális hálózati társviszony-létesítés. Virtuális hálózatok vannak társviszonyban, ha mindkét virtuális hálózat erőforrásainak képesek kommunikálnak egymással, ugyanahhoz késés és a sávszélesség, mintha az erőforrásokat ugyanabban a virtuális hálózatban. Ez a cikk ismerteti, létrehozása és a társviszony-létesítés két virtuális hálózatok. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Két virtuális hálózatok létrehozása
> * Virtuális hálózatok közötti társviszony létrehozása
> * Tesztelje a társviszony-létesítés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. Ahogy az alábbi képen is látható, adja meg a *myVirtualNetwork1* a **neve**, *10.0.0.0/16* a **Címtéren**,  **myResourceGroup** a **erőforráscsoport**, *Alhalozat_1* alhálózat **neve**, a 10.0.0.0/24 alhálózat **címtartomány** , jelölje be a **hely** és a **előfizetés**, fogadja el a többi alapértelmezett beállításokat, majd válassza ki **létrehozása**:

    ![Virtuális hálózat létrehozása](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Hajtsa végre a lépéseket 1-3 újra, a következő változások:
    - **Név**: *myVirtualNetwork2*
    - **Erőforráscsoport**: válasszon **meglévő** majd **myResourceGroup**.
    - **Címtér**: *10.1.0.0/16*
    - **Alhálózati címtartományt**: *10.1.0.0/24*

    A címelőtag a *myVirtualNetwork2* virtuális hálózat nem fedi át a címterében a *myVirtualNetwork1* virtuális hálózat. Virtuális hálózatok átfedő címterek nem partnert.

## <a name="peer-virtual-networks"></a>Társ virtuális hálózatok

1. Az Azure portál felső keresési mezőbe, írja be a szöveget *MyVirtualNetwork1*. Ha **myVirtualNetwork1** megjelenik a keresési eredmények között, jelölje be.
2. Válassza ki **Társviszony**a **beállítások**, majd válassza ki **+ Hozzáadás**, az alábbi ábrán látható módon:

    ![Társviszony-létesítés létrehozása](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Adja meg vagy válassza ki az alábbi ábrán látható adatokat, majd válassza **OK**. Jelölje be a *myVirtualNetwork2* virtuális hálózathoz, jelölje be **virtuális hálózati**, majd jelölje be *myVirtualNetwork2*.

    ![Társviszony-létesítési beállításai](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    A **társviszony-LÉTESÍTÉS állapot** van *kezdeményezett*, az alábbi ábrán látható módon:

    ![Társviszony-létesítési állapot](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Ha nem látja az állapota, frissítse a böngészőt.

4. Keresse meg a *myVirtualNetwork2* virtuális hálózat. Ha az eredmény abban az esetben a keresési eredmények között, válassza ki azt.
5. Hajtsa végre 1-3 újra, a következő változtatásokat, majd válassza ki **OK**:
    - **Név**: *myVirtualNetwork2-myVirtualNetwork1*
    - **Virtuális hálózati**: *myVirtualNetwork1*

    A **társviszony-LÉTESÍTÉS állapot** van *csatlakoztatva*. Azure társviszony-létesítési állapota megváltozik a *myVirtualNetwork2-myVirtualNetwork1* társviszony-létesítési a *kezdeményezett* való *csatlakoztatva.* Virtuális hálózati társviszony-létesítés nem teljesen létrejön mindaddig, amíg a társviszony-létesítési mindkét virtuális hálózat állapota *csatlakoztatva.* 

Társviszony virtuális hálózatok között, de nem tranzitív. Igen, például ha is egyenrangú *myVirtualNetwork2* való *myVirtualNetwork3*, kell létrehoznia a virtuális hálózatok közötti társviszony további *myVirtualNetwork2* és *myVirtualNetwork3*. Annak ellenére, hogy *myVirtualNetwork1* nincsenek társviszonyban, a *myVirtualNetwork2*, erőforrások *myVirtualNetwork1* csak hozzáférjen az erőforrásokhoz  *myVirtualNetwork3* Ha *myVirtualNetwork1* lett is társítottak, a *myVirtualNetwork3*. 

Társviszony-létesítés előtt éles virtuális hálózatok, javasoljuk, hogy alaposan feltérképezése a [társviszony-létesítési áttekintése](virtual-network-peering-overview.md), [kezelése a társviszony-létesítés](virtual-network-manage-peering.md), és [virtuális hálózati korlátok ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Bár ez a cikk bemutatja, hogy társviszony-létesítés között két virtuális hálózat ugyanabban az előfizetésben és helyen, a virtuális hálózatok is partnert is [különböző régiókban](#register) és [különböző Azure-előfizetések](create-peering-different-subscriptions.md#portal). Is létrehozhat [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a társviszony-létesítés.

## <a name="test-peering"></a>Tesztelje a társviszony-létesítés

A társviszony-létesítés különböző virtuális hálózatokon lévő virtuális gépek közötti hálózati kommunikációhoz teszteléséhez virtuális gép telepítése minden egyes alhálózathoz, és majd a virtuális gépek közötti kommunikációra. 

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Virtuális gép létrehozása minden egyes virtuális hálózatban, hogy ellenőrizhesse a azokat egy későbbi lépésben közötti kommunikációt.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Kiválaszthatja, hogy egy másik operációs rendszer, de a többi lépések azt feltételezik, hogy kiválasztott **Windows Server 2016 Datacenter**. 
3. Válassza ki vagy adja meg a következő információkat **alapjai**, majd jelölje be **OK**:
    - **Név**: *myVm1*
    - **Erőforráscsoport**: válasszon **meglévő** majd *myResourceGroup*.
    - **Hely**: válasszon *USA keleti régiója*.

    A **felhasználónév** és **jelszó** meg egy későbbi lépésben használt. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). A **hely** és **előfizetés** kijelölt meg kell egyeznie a hely és a virtuális hálózat előfizetése. Ez nem szükséges, hogy ugyanahhoz az erőforráscsoporthoz tartozik, a virtuális hálózati jött létre, de ugyanabban az erőforráscsoportban van kiválasztva, ez a cikk a választotta.
4. Válassza ki a Virtuálisgép-méretet a **méret kiválasztása**.
5. Válassza ki vagy adja meg a következő információkat **beállítások**, majd jelölje be **OK**:
    - **Virtuális hálózati**: Gondoskodjon arról, hogy **myVirtualNetwork1** van kiválasztva. Ha nem, válassza ki **virtuális hálózati** majd **myVirtualNetwork1** alatt **válasszon virtuális hálózati**.
    - **Alhálózati**: Gondoskodjon arról, hogy **Alhalozat_1** van kiválasztva. Ha nem, válassza ki a **alhálózati** , és válassza **Alhalozat_1** alatt **alhálózat kiválasztása**, az alábbi ábrán látható módon:
    
        ![Virtuális gép beállításait](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. A **létrehozása** a a **összegzés**, jelölje be **létrehozása** elindítani a virtuális gépek telepítése során.
7. Ismét végezze el az 1-6 lépéseket, de meg *myVm2* a a **neve** a virtuális gép, és válassza ki a *myVirtualNetwork2* a **virtuális hálózati**.

Hozzárendelt Azure *10.0.0.4* a magánhálózati IP-címe, a *myVm1* virtuális gép, és a 10.1.0.4 a *myVm2* virtuális gép, mert az első elérhető IP-cím a címek *Alhalozat_1* , a *myVirtualNetwork1* és *myVirtualNetwork2* virtuális hálózatok, illetve.

A virtuális gépek létrehozása több percig is tarthat. Ne folytassa a hátralévő lépéseket addig mindkét virtuális gépek jönnek létre.

### <a name="test-virtual-machine-communication"></a>Teszt virtuális gép kommunikáció

1. Az a *keresési* a portál felső mezőbe írja be a szöveget *myVm1*. Ha **myVm1** megjelenik a keresési eredmények között, jelölje be.
2. Távoli asztali kapcsolat létrehozása a *myVm1* virtuális gép kiválasztásával **Connect**, az alábbi ábrán látható módon:

    ![Csatlakozás virtuális géphez](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Csatlakoztassa a virtuális Gépet, nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**.
4. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, ha a megadott hitelesítő adatok megadását, a virtuális gép létrehozása), majd válassza ki **OK**.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** gombra a kapcsolat.
6. Egy későbbi lépésben ping folytatott kommunikációhoz használandó a *myVm2* virtuális gépek létrehozását a *myVmWeb* virtuális gépet. Ping az ICMP, amely alapértelmezés szerint a Windows tűzfalon keresztül megtagadva. A Windows tűzfalon keresztül ICMP engedélyezéséhez az alábbi parancs beírásával a parancssorba:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Bár a ping tesztelési a cikk ezt használja, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

7. Csatlakozni a *myVm2* virtuális gépet, adja meg a következő parancsot a parancssorba a a *myVm1* virtuális gépet:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Mivel a ping engedélyezve van a *myVm1*, IP-cím szerint most pingelhető:

    ```
    ping 10.0.0.4
    ```
    
    Négy választ kap. Ha a virtuális gép neve pingelése (*myVm1*), az IP-cím helyett pingelés sikertelen, mert *myVm1* egy ismeretlen állomás neve. Azure alapértelmezett névfeloldás működik, az azonos virtuális hálózatban lévő virtuális gépek között, de nem a különböző virtuális hálózatokon lévő virtuális gépek között. Virtuális hálózatok közötti névfeloldás, kell [a saját DNS-kiszolgáló telepítése](virtual-networks-name-resolution-for-vms-and-role-instances.md) , vagy használjon [titkos tartományok Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Válassza le az RDP-munkamenetekhez egyaránt *myVm1* és *myVm2*.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha az erőforráscsoport és a benne található összes erőforrást törli: 

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

**<a name="register"></a>A globális virtuális hálózati társviszony-létesítési Preview regisztrálása**

Az azonos régiókban lévő virtuális hálózatok közötti társviszony kialakítása általánosan elérhető. Virtuális hálózatok különböző régiókban jelenleg előzetes verzióban érhetők társviszony. Lásd: [virtuális hálózati frissítések](https://azure.microsoft.com/updates/?product=virtual-network) az elérhető régiók. Virtuális hálózatok egyenrangú régiók között, először regisztrálnia kell az előzetes verziójára. A portál használatával nem regisztrálható, de használatával regisztrálhatja [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) vagy a [Azure CLI](tutorial-connect-virtual-networks-cli.md#register). Ha megkísérli különböző régiókban lévő virtuális hálózatok a képességhez regisztrálása előtt partnert, társviszony-létesítés meghiúsul.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, két hálózat kapcsolódás a virtuális hálózati társviszony-létesítés.

Saját számítógép csatlakoztatása egy virtuális hálózathoz egy VPN-en keresztül, és egy virtuális hálózatot, vagy nincsenek társviszonyban, virtuális hálózatok erőforrások módosításának továbbra is.

> [!div class="nextstepaction"]
> [A számítógép csatlakoztatása egy virtuális hálózatot](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
