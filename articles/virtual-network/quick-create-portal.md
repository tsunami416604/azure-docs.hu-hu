---
title: "Hozzon létre egy Azure virtuális hálózatra - portál |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy virtuális hálózatot az Azure portál használatával. A virtuális hálózati lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépeket, közvetlenül kommunikálnak egymással, és az internetes."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: c8f2cbe6b7377772e019a4ff90f91355ba0815ae
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Virtuális hálózat létrehozása az Azure Portallal

Virtuális hálózat közvetlenül kommunikálnak egymással, és az internetes Azure-erőforrások, például a virtuális gépek (VM), lehetővé. Ebből a cikkből megismerheti, hogyan hozhat létre virtuális hálózatot. Virtuális hálózat létrehozása után a két virtuális gépeket létrehozni a virtuális hálózatban telepít. Ezután csatlakoztassa az internetről egy virtuális Gépet, és közvetlenül a Microsoftnak a két virtuális gépek közötti kommunikációra.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. Adja meg, vagy válassza ki, a következő információkat, fogadja el az alapértelmezett értéket a többi beállítást, és válassza **létrehozása**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVirtualNetwork|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza ki **hozzon létre új** , és írja be *myResourceGroup*.|
    |Hely| Válassza ki **USA keleti régiója**.|

    ![A virtuális hálózat alapszintű adatainak megadása](./media/quick-create-portal/create-virtual-network.png)

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépek a virtuális hálózat:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** az Azure portál bal oldali, felső sarkában található.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.
3. Adja meg, vagy válassza ki, a következő információkat, fogadja el az alapértelmezett értéket a többi beállítást, és válassza **OK**:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVm1|
    |Felhasználónév| Adjon meg a nevet.|
    |Jelszó| Adja meg a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza ki **meglévő** válassza **myResourceGroup**.|
    |Hely| Válassza ki **USA keleti régiója**|

    ![Virtuális gép alapjai](./media/quick-create-portal/virtual-machine-basics.png)

4. A virtuális gép kiválasztásához, és válassza ki **válasszon**.
5. A **beállítások**, fogadja el az alapértelmezett beállításokat, és válassza ki **OK**.

    ![Virtuális gép beállításait](./media/quick-create-portal/virtual-machine-settings.png)

6. A **létrehozása** , a **összegzés**, jelölje be **létrehozása** elindítani a virtuális gép üzembe helyezéséhez. A virtuális gép telepítése néhány percet vesz igénybe. 

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

1-6 lépéseket újra, de a 3 nevet a virtuális gép *myVm2*.

## <a name="connect-to-a-vm-from-the-internet"></a>Csatlakoztassa a virtuális Gépet az internetről

1. Miután *myVm1* van létrehozva, csatlakozni. Adja meg az Azure portál felső részén *myVm1*. Ha **myVm1** megjelenik a keresési eredmények között, jelölje be. Válassza ki a **Connect** gombra.

    ![Csatlakozzon a virtuális géphez](./media/quick-create-portal/connect-to-virtual-machine.png)

2. Kijelölése után a **Connect** gombra, a távoli asztal protokoll (RDP) fájl jön létre, és a számítógép letölti.  
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**. Adja meg a felhasználónevet és a virtuális gép létrehozásakor megadott jelszót. Válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása. 
4. Kattintson az **OK** gombra.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a figyelmeztetést kap, válassza ki a **Igen** vagy **Folytatás**, hogy a kapcsolat.

## <a name="communicate-privately-between-vms"></a>Magánjellegű virtuális gépek közötti kommunikáció

1. Adja meg a Powershellből `ping myvm2`. Pingelés nem sikerül, mert az internet control message protocol (ICMP) és az ICMP ping keresztül nem engedélyezett a Windows tűzfal alapértelmezés szerint.
2. Hogy *myVm2* ping *myVm1* egy későbbi lépésben adja meg a következő parancsot a PowerShell, amely lehetővé teszi az ICMP bejövő a Windows tűzfalon keresztül:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

3. Zárja be a távoli asztali kapcsolatot *myVm1*. 

4. Hajtsa végre a [csatlakozás a virtuális gépek az internetről](#connect-to-a-vm-from-the-internet) újra, de csatlakozni *myVm2*. A parancssorból, írja be a `ping myvm1`.

    A választ kap *myVm1*, mert a a Windows tűzfalon keresztül a ICMP engedélyezett. a *myVm1* VM az előző lépésben.

5. Zárja be a távoli asztali kapcsolatot *myVm2*.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, az erőforráscsoport és az összes benne található erőforrás törlése:

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy alapértelmezett virtuális hálózat és két virtuális gépeket. Egy virtuális gép csatlakozik az interneten, és közvetlenül a Microsoftnak továbbítani a virtuális gép és egy másik virtuális gép között. Virtuális hálózati beállításaival kapcsolatos további tudnivalókért lásd: [egy virtuális hálózat kezeléséhez](manage-virtual-network.md).

Alapértelmezés szerint az Azure lehetővé teszi, hogy a virtuális gépek korlátlan titkos kommunikációját, de csak lehetővé teszi a bejövő távoli asztali kapcsolatokhoz a Windows virtuális gépek az internetről. Annak megismerése, hogyan engedélyezésére vagy korlátozására a különböző hálózati kommunikációs felé és felől a virtuális gépek, a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Hálózati forgalom szűrésére](virtual-networks-create-nsg-arm-pportal.md)