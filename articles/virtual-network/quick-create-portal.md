---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure Portal | Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával. A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek számára, hogy privát módon kommunikáljanak egymással és az internettel.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7107dc72686004141d8bea0083089cba065a9f4c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rövid útmutató: Virtuális hálózat létrehozása az Azure Portallal

A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek (VM-ek) számára, hogy privát módon kommunikáljanak egymással és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután az internetről csatlakozni fog az egyik virtuális géphez, és privát kommunikációt kezdeményez a két virtuális gép között.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza az **Új létrehozása** elemet, és adja meg a *myResourceGroup* nevet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|

    ![Írja be a virtuális hálózat alapvető adatait](./media/quick-create-portal/create-virtual-network.png)

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVm1|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza a **Meglévő használata** lehetőséget, majd a **myResourceGroup** elemet.|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|

    ![Virtuális gép alapadatai](./media/quick-create-portal/virtual-machine-basics.png)

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **Beállítások** területen fogadja el az összes alapértelmezett beállítást, majd kattintson az **OK** gombra.

    ![A virtuális gép beállításai](./media/quick-create-portal/virtual-machine-settings.png)

6. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez. A virtuális gép üzembe helyezése néhány percet vesz igénybe. 

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Végezze el ismét az 1-6. lépést, de a 3. lépésben a *myVm2* nevet adja a virtuális gépnek.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

1. Miután létrehozta a *myVm1* virtuális gépet, csatlakozzon hozzá. Az Azure Portal tetején található mezőbe gépelje be a *myVm1* kifejezést. Amikor a **myVm1** elem megjelenik a keresési eredmények között, válassza ki. Kattintson a **Csatlakozás** gombra.

    ![Csatlakozás virtuális géphez](./media/quick-create-portal/connect-to-virtual-machine.png)

2. A **Csatlakozás** gomb kiválasztása után a rendszer létrehoz és letölt a számítógépre egy Remote Desktop Protocol (.rdp) fájlt.  
3. Nyissa meg a letöltött rdp-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A PowerShellben írja be a következőt: `ping myvm2`. A pingelés sikertelen lesz, mert a pingelés az ICMP-t használja, és azt a Windows tűzfal alapértelmezés szerint nem engedi át.
2. Ahhoz, hogy a *myVm2* virtuális gép pingelhesse a *myVm1* virtuális gépet egy későbbi lépésben, írja be az alábbi parancsot PowerShellbe. Ez átengedi a beérkező ICMP-t a Windows tűzfalon:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

3. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot. 

4. Hajtsa ismét végre a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet) című részben leírt lépéseket, de most csatlakozzon a *myVm2* virtuális géphez. Egy parancssorba írja be a következőt: `ping myvm1`.

    Mivel az előző lépésben átengedte az ICMP-t a Windows tűzfalon a *myVm1* virtuális gép esetében, válaszokat fog kapni a *myVm1* virtuális géptől.

5. Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Ezután csatlakozott az internetről az egyik virtuális géphez, és privát kommunikációt folytatott egy másik virtuális géppel. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md).

Az Azure alapértelmezés szerint engedélyezi a virtuális gépek korlátlan privát kommunikációját, de az internetről bejövő távoli asztali kapcsolatokat csak a Windows rendszerű virtuális gépeken engedélyezi. Ha többet szeretne megtudni arról, hogyan lehet engedélyezni vagy korlátozni a virtuális gépek kimenő vagy bejövő hálózati kommunikációjának különböző típusait, lépjen a [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md) című oktatóanyagra.