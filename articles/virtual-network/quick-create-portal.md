---
title: "Virtuális hálózat létrehozása az Azure - portál |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy virtuális hálózatot az Azure portál használatával. A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálhatnak egymással."
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
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 8b6a4abdb7677417462392feade0c7cfdf99246f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Virtuális hálózat létrehozása az Azure Portallal

Ebből a cikkből megismerheti, hogyan hozhat létre virtuális hálózatot. Virtuális hálózat létrehozása után két virtuális gépek telepítése a virtuális hálózathoz, és közöttük, és az internetes közvetlenül kommunikálnak.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza ki **+ hozzon létre egy erőforrást** a felső, bal oldali sarkában az Azure-portálon.
2. Válassza ki **hálózati**, majd válassza ki **virtuális hálózati**.
3. Adja meg, vagy válassza ki, a következő adatokat, és válassza ki **létrehozása**:
    - **Név**: *myVirtualNetwork*
    - **Címtér**: fogadja el az alapértelmezett. A címterület CIDR-formátumban van megadva.
    - **Előfizetés**: Jelölje ki az előfizetését.
    - **Erőforráscsoport**: válasszon **hozzon létre új** , és írja be *myResourceGroup*.
    - **Hely**: Válasszon * keleti USA **.
    - **Alhálózati név**: fogadja el az alapértelmezett.
    - **Alhálózat, címtartomány**: fogadja el az alapértelmezett.
    - **Szolgáltatás végpontjait**: fogadja el az alapértelmezett.

    ![A virtuális hálózat alapszintű adatainak megadása](./media/quick-create-portal/virtual-network.png)

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálnak egymással, és az internetes. A virtuális hálózatba telepítése erőforrás egy típus egy virtuális gépet.

1. Válassza ki **+ hozzon létre egy erőforrást** az Azure portál bal oldali, felső sarkában található.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.
3. Adja meg, vagy válassza ki, a következő adatokat, majd **OK**:
    - **Név**: *myVm1*
    - **Felhasználónév**: Adjon meg a felhasználónevet.
    - **Jelszó**: Adja meg a jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
    - **Előfizetés**: Jelölje ki az előfizetését.
    - **Erőforráscsoport**: válasszon **meglévő** válassza **myResourceGroup**.
    - **Hely**: válasszon *USA keleti régiója*.

    ![Adja meg a virtuális géppel kapcsolatos alapvető információk](./media/quick-create-portal/virtual-machine-basics.png)
4. Válassza ki a virtuális gép méretét majd **válasszon**.
5. A **beállítások**, *myVirtualNetwork* már választható ki **virtuális hálózati**, de ha nem, adja meg **virtuális hálózati** , majd válassza ki *myVirtualNetwork*. Hagyja *alapértelmezett* a kijelölt **alhálózati**, majd válassza ki **OK**.

    ![Válasszon virtuális hálózatot](./media/quick-create-portal/virtual-machine-network-settings.png)
6. Az a **összegzés** lapon jelölje be **létrehozása** elindítani a virtuális gépek telepítése során. A virtuális gép telepítése néhány percet vesz igénybe. 
7. 1-6 lépéseket újra, de a 3, a virtuális gép neve *myVm2*.

## <a name="connect-to-a-virtual-machine"></a>Csatlakozzon a virtuális géphez

1. Miután *myVm1* jön létre, távolról kapcsolódni hozzá. Adja meg az Azure portál felső részén *myVm1*. Ha **myVm1** megjelenik a keresési eredmények között, jelölje be. Válassza ki a **Connect** gombra.

    ![Virtuális gépek – áttekintés](./media/quick-create-portal/virtual-machine-overview.png)

2. Kijelölése után a **Connect** gombra, a távoli asztal protokoll (RDP) fájl jön létre, és a számítógép letölti.  
3. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, ha a megadott hitelesítő adatok megadását, a virtuális gép létrehozása), majd válassza az OK gombra. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** vagy **Folytatás** gombra a kapcsolat.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A parancssorból, írja be a `ping myvm2`. Pingelés nem sikerül, mert a ping használ ICMP és az ICMP keresztül nem engedélyezett a Windows tűzfal alapértelmezés szerint. Hogy *myVm2* ping *myVm1* egy későbbi lépésben adja meg a következő parancsot a parancssorba:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

2. Zárja be a távoli asztali kapcsolatot *myVm1*. 

3. Hajtsa végre a [kapcsolódás a virtuális gép](#connect-to-a-virtual-machine), de csatlakozni *myVm2*. A parancssorból, írja be a `ping myvm1`.

    Ping paranccsal lehet a *myVm1* virtuális gépek létrehozását a *myVm2* virtuális gép mert:

    - ICMP a Windows tűzfalon keresztül az engedélyezett a *myVm1* az előző lépésben a virtuális gép.
    - Alapértelmezés szerint Azure lehetővé teszi, hogy az összes hálózati forgalom az azonos virtuális hálózatban lévő erőforrások között.

## <a name="communicate-to-the-internet"></a>Az interneten kommunikálnak

1. Miközben továbbra is kapcsolódik a *myVm2* adja meg a virtuális gép, egy parancs parancssori futtatásával `ping bing.com`.

    Négy választ kap bing.com. 

    Internetes erőforráshoz a ping paranccsal lehet a *myVm2* virtuális gép, mert e virtuális gépek képes kommunikálni a kimenő csatlakozik az internethez, alapértelmezés szerint.

2. A távoli asztali munkamenet bezárásához.

## <a name="communicate-from-the-internet"></a>Az internetes kommunikáció

1. A nyilvános IP-cím az beszerzése a *myVm1* virtuális gépet. A képen látható az 1. lépés [kapcsolódás a virtuális gép](#connect-to-a-virtual-machine), megjelenik egy nyilvános IP-címet. A képen látható, a címe *13.90.241.247*. A címet a virtuális gép nem azonos. 

2. A számítógépről Pingelje meg a nyilvános IP-címét a *myVm1* virtuális gépet. Pingelés nem sikerül, annak ellenére, hogy az ICMP meg nyitva a Windows tűzfalon keresztül.

    Pingelés nem sikerül, mert a Windows virtuális gépek, kivéve a távoli asztali kapcsolatok 3389-es porton keresztül bejövő forgalmát alapértelmezés szerint az Azure-ban, megtagadva. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Már nincs szükség, ha az erőforráscsoport és a benne található összes erőforrást törli:

1. Adja meg *myResourceGroup* a a **keresési** a portál felső részén. Amikor látja **myResourceGroup** válassza ki azt a keresési eredmények között.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Adja meg *myResourceGroup* a **típus az ERŐFORRÁSCSOPORT-név:** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe egy alapértelmezett virtuális hálózatot egyetlen alhálózattal. Annak megismerése, hogyan hozhat létre egyéni virtuális hálózatot, több alhálózattal, továbbra is az oktatóanyag egy egyéni virtuális hálózat létrehozásához.

> [!div class="nextstepaction"]
> [Egyéni virtuális hálózat létrehozása](virtual-networks-create-vnet-arm-pportal.md)
