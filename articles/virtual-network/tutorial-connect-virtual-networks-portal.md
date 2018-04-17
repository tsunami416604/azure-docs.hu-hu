---
title: Virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony létesítésével – oktatóanyag – Azure Portal | Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogy kapcsolhat össze virtuális hálózatokat virtuális hálózatok közötti társviszony az Azure Portal használatával történő létesítésével.
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
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d702253c7b58b0a29c03e6563238b56ae75fa0d1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Oktatóanyag: Virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony az Azure Portal használatával történő létesítésével

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két virtuális gép létrehozása
> * Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
> * Virtuális gép üzembe helyezése mindkét virtuális hálózaton
> * Virtuális gépek közötti kommunikáció

Igény szerint az oktatóanyagot az [Azure CLI](tutorial-connect-virtual-networks-cli.md) vagy az [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md) használatával is elvégezheti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork1|
    |Címtér|10.0.0.0/16|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza az **Új létrehozása** elemet, és adja meg a *myResourceGroup* nevet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|
    |Alhálózat neve|Subnet1|
    |Alhálózat címtartománya|10.0.0.0/24|

      ![Virtuális hálózat létrehozása](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Végezze el ismét az 1–3. lépést az alábbi módosításokkal:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork2|
    |Címtér|10.1.0.0/16|
    |Erőforráscsoport| Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget.|
    |Alhálózat címtartománya|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

1. Kezdje el begépelni a *MyVirtualNetwork1* kifejezést az Azure Portal tetején található keresőmezőbe. Amikor a **myVirtualNetwork1** elem megjelenik a keresési eredmények között, válassza ki.
2. A **BEÁLLÍTÁSOK** területen válassza a **Társviszonyok**, majd a **+Hozzáadás** elemet az alábbi ábrán látható módon:

    ![Társviszony létrehozása](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork1-myVirtualNetwork2|
    |Előfizetés| Válassza ki előfizetését.|
    |Virtuális hálózat|myVirtualNetwork2 – A *myVirtualNetwork2* virtuális hálózat kiválasztásához válassza a **Virtuális hálózat**, majd a **myVirtualNetwork2** elemet.|

    ![Társviszony beállításai](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    A **TÁRSVISZONY-LÉTESÍTÉS ÁLLAPOTA** *Kezdeményezve* lesz, ahogy az az alábbi ábrán látható:

    ![Társviszony-létesítés állapota](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Ha nem látja az állapotot, frissítse a böngészőjét.

4. Kezdje el begépelni a *MyVirtualNetwork2* kifejezést az Azure Portal tetején található **Keresés** mezőbe. Amikor a **myVirtualNetwork2** elem megjelenik a keresési eredmények között, válassza ki.
5. Végezze el ismét a 2. és 3. lépést az alábbi módosításokkal, majd kattintson az **OK** elemre:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork2-myVirtualNetwork1|
    |Virtuális hálózat|myVirtualNetwork1|

    A **TÁRSVISZONY-LÉTESÍTÉS ÁLLAPOTA** *Csatlakoztatva* lesz. Az Azure a *myVirtualNetwork2-myVirtualNetwork1* társviszonyának állapotát is módosította a *Kezdeményezve* értékről a *Csatlakoztatva* értékre. A virtuális hálózatok közötti társviszony létesítése egészen addig nem történik meg sikeresen, amíg a társviszony-létesítés állapota mindkét virtuális hálózat esetében *Csatlakoztatva* értékre nem vált. 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Választhat másik operációs rendszert is, de a következő lépések itt azt feltételezik, hogy a **Windows Server 2016 Datacentert** választotta. 
3. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVm1|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Erőforráscsoport| Válassza a **Meglévő használata**, majd a **myResourceGroup** lehetőséget.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|
4. Válasszon egy virtuálisgép-méretet a **Méret kiválasztása** alatt.
5. Válassza ki az alábbi értékeket a **Beállítások** területen, majd kattintson az **OK** gombra:
    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| myVirtualNetwork1 – Ha még nincs kiválasztva, válassza a **Virtuális hálózat** elemet, majd a **Virtuális hálózat kiválasztása** területen válassza a **myVirtualNetwork1** elemet.|
    |Alhálózat| Subnet1 – Ha még nincs kiválasztva, válassza az **Alhálózat** elemet, majd az **Alhálózat kiválasztása** területen válassza a **Subnet1** elemet.|
    
    ![A virtuális gép beállításai](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Végezze el ismét az 1–6. lépést az alábbi módosításokkal:

|Beállítás|Érték|
|---|---|
|Name (Név) | myVm2|
|Virtuális hálózat | myVirtualNetwork2|

A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. Kezdje el begépelni a *myVm1* kifejezést a portál tetején található *Keresés* mezőbe. Amikor a **myVm1** elem megjelenik a keresési eredmények között, válassza ki.
2. Hozzon létre egy távoli asztali kapcsolatot a *myVm1* virtuális géppel a **Csatlakozás** lehetőség kiválasztásával az alábbi ábrán látható módon:

    ![Csatlakozás virtuális géphez](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Nyissa meg a letöltött RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.
4. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.
6. Egy későbbi lépésben pingeléssel fog kommunikálni a *myVm2* virtuális géppel a *myVm1* virtuális gépről. A pingelés az ICMP protokollt használja, amelyet a Windows tűzfal alapértelmezés szerint letilt. A *myVm1* virtuális gépen engedélyezze az ICMP-t a Windows tűzfalon, hogy egy később lépésben pingelhesse ezt a virtuális gépet a *myVm2* virtuális gépről a PowerShell használatával.

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Habár ebben az oktatóanyagban pingelést használunk a virtuális gépek közötti kommunikációhoz, az éles környezetekben üzemelő példányok esetében nem ajánlott az ICMP engedélyezése a Windows tűzfalon.

7. A *myVm2* virtuális géphez való csatlakozáshoz adja meg a következő parancsot egy parancssorban a *myVm1* virtuális gépen:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Mivel engedélyezte a pingelést a *myVm1* virtuális gépen, most már pingelheti azt az IP-cím alapján:

    ```
    ping 10.0.0.4
    ```
    
9. Válassza le az RDP-munkameneteket a *myVm1* és *myVm2* virtuális gépről egyaránt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást: 

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerhette, hogyan kapcsolhat össze két, ugyanabban az Azure-régióban található virtuális hálózatot virtuális hálózatok közötti társviszony létesítésével. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#portal) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

Ha csatlakoztatni szeretné saját számítógépét egy virtuális hálózathoz VPN-en keresztül, és interakcióba szeretne lépni egy virtuális hálózaton vagy társviszonnyal összekapcsolt virtuális hálózatokon található erőforrásokkal, tekintse meg a [Számítógép csatlakoztatása egy virtuális hálózathoz](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikket.
