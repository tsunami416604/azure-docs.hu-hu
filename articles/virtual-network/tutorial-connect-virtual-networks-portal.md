---
title: Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony - Azure-portál |} Microsoft Docs
description: Ebből a cikkből megtanulhatja a virtuális hálózatok kapcsolódni a virtuális hálózati társviszony-létesítést, az Azure portál használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b864c71a62289b3abef13a98b52683f7d928b8e1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítést az Azure portál használatával

Kapcsolódás virtuális hálózatok egymástól a virtuális hálózati társviszony-létesítés. Virtuális hálózatok vannak társviszonyban, ha mindkét virtuális hálózat erőforrásainak képesek kommunikálnak egymással, ugyanahhoz késés és a sávszélesség, mintha az erőforrásokat ugyanabban a virtuális hálózatban. Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * Két virtuális hálózatok létrehozása
> * A virtuális hálózati társviszony-létesítés két virtuális hálózatok csatlakoztatása
> * Virtuális gép (VM) telepítése minden virtuális hálózathoz
> * Virtuális gépek közötti kommunikáció

Tetszés szerint ez a cikk segítségével befejezheti a [Azure CLI](tutorial-connect-virtual-networks-cli.md) vagy [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. Adja meg, vagy válassza ki, a következő információkat, fogadja el az alapértelmezett értéket a többi beállítást, és válassza **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork1|
    |Címtér|10.0.0.0/16|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza ki **hozzon létre új** , és írja be *myResourceGroup*.|
    |Hely| Válassza ki **USA keleti régiója**.|
    |Alhálózat neve|Subnet1|
    |Alhálózati címtartományt|10.0.0.0/24|

      ![Virtuális hálózat létrehozása](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Hajtsa végre a lépéseket 1-3 újra, a következő változások:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork2|
    |Címtér|10.1.0.0/16|
    |Erőforráscsoport| Válassza ki **meglévő** majd **myResourceGroup**.|
    |Alhálózati címtartományt|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Társ virtuális hálózatok

1. Az Azure portál felső keresési mezőbe, írja be a szöveget *MyVirtualNetwork1*. Ha **myVirtualNetwork1** megjelenik a keresési eredmények között, jelölje be.
2. Válassza ki **Társviszony**a **beállítások**, majd válassza ki **+ Hozzáadás**, az alábbi ábrán látható módon:

    ![Társviszony-létesítés létrehozása](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Adja meg, vagy válassza ki, a következő információkat, fogadja el az alapértelmezett értéket a többi beállítást, és válassza **OK**.

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork1-myVirtualNetwork2|
    |Előfizetés| Válassza ki előfizetését.|
    |Virtuális hálózat|myVirtualNetwork2 - jelölje be a *myVirtualNetwork2* virtuális hálózathoz, jelölje be **virtuális hálózati**, majd jelölje be **myVirtualNetwork2**.|

    ![Társviszony-létesítési beállításai](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    A **társviszony-LÉTESÍTÉS állapot** van *kezdeményezett*, az alábbi ábrán látható módon:

    ![Társviszony-létesítési állapot](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Ha nem látja az állapota, frissítse a böngészőt.

4. Az a **keresési** az Azure portál felső mezőbe írja be a szöveget *MyVirtualNetwork2*. Ha **myVirtualNetwork2** megjelenik a keresési eredmények között, jelölje be.
5. Hajtsa végre 2-3 újra, a következő változtatásokat, majd válassza ki **OK**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork2-myVirtualNetwork1|
    |Virtuális hálózat|myVirtualNetwork1|

    A **társviszony-LÉTESÍTÉS állapot** van *csatlakoztatva*. Azure társviszony-létesítési állapota megváltozik a *myVirtualNetwork2-myVirtualNetwork1* társviszony-létesítési a *kezdeményezett* való *csatlakoztatva.* Virtuális hálózati társviszony-létesítés nem teljesen létrejön mindaddig, amíg a társviszony-létesítési mindkét virtuális hálózat állapota *csatlakoztatva.* 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Virtuális gép létrehozása minden egyes virtuális hálózatban, hogy egy későbbi lépésben közötti kommunikációt.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Kiválaszthatja, hogy egy másik operációs rendszer, de a többi lépések azt feltételezik, hogy kiválasztott **Windows Server 2016 Datacenter**. 
3. Adja meg, vagy válassza ki, a következő információkat **alapjai**, fogadja el az alapértelmezett értéket a többi beállítást, és válassza **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVm1|
    |Felhasználónév| Adjon meg a nevet.|
    |Jelszó| Adja meg a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza ki **meglévő** majd **myResourceGroup**.|
    |Hely| Válassza ki **USA keleti régiója**.|
4. Válassza ki a Virtuálisgép-méretet a **méret kiválasztása**.
5. Válassza ki a következő értékek **beállítások**, majd jelölje be **OK**:
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| myVirtualNetwork1 – Ha még nincs bejelölve, jelölje be **virtuális hálózati** , és válassza **myVirtualNetwork1** alatt **válasszon virtuális hálózati**.|
    |Alhálózat| Alhalozat_1 – Ha még nincs bejelölve, jelölje be **alhálózati** , és válassza **Alhalozat_1** alatt **alhálózat kiválasztása**.|
    
    ![Virtuális gép beállításait](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. A **létrehozása** a a **összegzés**, jelölje be **létrehozása** elindítani a virtuális gép üzembe helyezéséhez.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Hajtsa végre a lépéseket 1-6 újra, a következő változások:

|Beállítás|Érték|
|---|---|
|Name (Név) | myVm2|
|Virtuális hálózat | myVirtualNetwork2|

A virtuális gépek létrehozása több percig is tarthat. Ne folytassa a hátralévő lépéseket mindkét virtuális gépek létrehozásáig.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. Az a *keresési* a portál felső mezőbe írja be a szöveget *myVm1*. Ha **myVm1** megjelenik a keresési eredmények között, jelölje be.
2. Távoli asztali kapcsolat létrehozása a *myVm1* kiválasztásával VM **Connect**, az alábbi ábrán látható módon:

    ![Csatlakozás virtuális géphez](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Csatlakoztassa a virtuális Gépet, nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**.
4. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása), Válassza ki **OK**.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** gombra a kapcsolat.
6. Egy későbbi lépésben ping folytatott kommunikációhoz használandó a *myVm2* virtuális gép a *myVm1* virtuális gép. Ping használja az Internet Control üzenet Protocol (ICMP), amely alapértelmezés szerint a Windows tűzfalon keresztül megtagadva. Az a *myVm1* VM, engedélyezze az ICMP Protokollt a Windows tűzfalon keresztül, hogy a virtuális gép pingelhető *myVm2* egy későbbi lépésben, a PowerShell használatával:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Bár a ping használatával Ez a cikk a virtuális gépek közötti kommunikáció, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

7. Csatlakozni a *myVm2* VM, adja meg a következő parancsot a parancssorba a a *myVm1* VM:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Mivel a ping engedélyezve van a *myVm1*, IP-cím szerint most pingelhető:

    ```
    ping 10.0.0.4
    ```
    
9. Válassza le az RDP-munkamenetekhez egyaránt *myVm1* és *myVm2*.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha az erőforráscsoport és a benne található összes erőforrást törli: 

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, azonos Azure-régióban, két hálózat kapcsolódás a virtuális hálózati társviszony-létesítés. Virtuális hálózatok, a másik partnert is meg is [támogató régiók](virtual-network-manage-peering.md#cross-region) és a [különböző Azure-előfizetések](create-peering-different-subscriptions.md#portal), valamint létrehozása [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) rendelkező társviszony-létesítés. Virtuális hálózati társviszony-létesítés kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítési áttekintése](virtual-network-peering-overview.md) és [kezelheti a virtuális hálózati társviszony](virtual-network-manage-peering.md).

Saját számítógép csatlakoztatása egy virtuális hálózathoz egy VPN-en keresztül, és az erőforrások egy virtuális hálózatot, vagy nincsenek társviszonyban, virtuális hálózatok interakciót [a számítógép csatlakoztatása egy virtuális hálózati](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
