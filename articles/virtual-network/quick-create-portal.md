---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure Portal | Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával. Virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például virtuális gépeket, privát módon kommunikáljanak egymással, és az interneten.
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
ms.date: 11/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: d627d54686cb40714e9e8574ceae56a8900cb45e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269464"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Gyors útmutató: Virtuális hálózat létrehozása az Azure Portallal

Virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például virtuális gépeket (VM), hogy privát módon kommunikáljanak egymással, és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozhat a virtuális gépekhez az internetről, és privát módon kommunikáljanak a két virtuális gép között.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A képernyő bal felső oldalán válassza **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózati**.

1. A **virtuális hálózat létrehozása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myVirtualNetwork*. |
    | Címtér | Adja meg *10.1.0.0/16*. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza ki **új létrehozása**, adja meg *myResourceGroup*, majd **OK**. |
    | Hely | Válassza az **USA keleti régiója** lehetőséget.|
    | Alhálózat - név | Adja meg *myVirtualSubnet*. |
    | Alhálózat – címtartomány | Adja meg *10.1.0.0/24*. |

1. Hagyja meg az alapértelmezett beállításokat, és válassza ki a többi **létrehozás**.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. A képernyő bal felső oldalán válassza **erőforrás létrehozása** > **számítási** > **Windows Server 2016 Datacenter**.

1. A **hozzon létre egy virtuális gép – alapvető**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza ki **MyResourceGroup**. Az előző szakaszban létrehozott. |
    | **PÉLDÁNY ADATAI** |  |
    | Virtuális gép neve | Adja meg *myVm1*. |
    | Régió | Válassza az **USA keleti régiója** lehetőséget. |
    | Rendelkezésre állási beállításai | Hagyja meg az alapértelmezett **szükséges infrastruktúra redundancia**. |
    | Kép | Hagyja meg az alapértelmezett **Windows Server 2016 Datacenter**. |
    | Méret | Hagyja meg az alapértelmezett **Standard DS1 v2**. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adjon meg egy tetszőleges felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Írja be újból a jelszót. |
    | **BEJÖVŐPORT-SZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **None**. |
    | **PÉNZT TAKARÍTHAT MEG** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**. |

1. Válassza ki **tovább: Lemezek**.

1. A **hozzon létre egy virtuálisgép - lemezek**, meghagyhatja az alapértelmezett beállításokat, és válassza ki **tovább: Hálózatkezelés**.

1. A **hozzon létre egy virtuálisgép - hálózat**, jelölje be ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **myVirtualNetwork**. |
    | Alhálózat | Hagyja meg az alapértelmezett **myVirtualSubnet (10.1.0.0/24)**. |
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) ip-myVm**. |
    | Hálózati biztonsági portok | Válassza ki **lehetővé teszi a kiválasztott portok**. |
    | Bejövő portok kiválasztása | Válassza ki **HTTP** és **RDP**.

1. Válassza ki **tovább: Felügyeleti**.

1. A **hozzon létre egy virtuálisgép - felügyeleti**, a **diagnosztikai tárfiók**válassza **hozzon létre új**.

1. A **storage-fiók létrehozása**, adja meg vagy válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg *myvmstorageaccount*. |
    | Fióktípus | Hagyja meg az alapértelmezett **Storage (általános célú v1)**. |
    | Teljesítmény | Hagyja meg az alapértelmezett **Standard**. |
    | Replikáció | Hagyja meg az alapértelmezett **helyileg redundáns tárolás (LRS)**. |

1. Kattintson az **OK** gombra.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Ekkor átkerül a **felülvizsgálat + létrehozása** oldal és az Azure ellenőrzi a konfigurációt.

1. Ha azt láthatja, hogy **ellenőrzésen**válassza **létrehozás**.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

1. 1. és 9 a fenti lépéseket.

    > [!NOTE]
    > 2. lépésében, az a **virtuális gép neve**, adja meg *myVm2*.
    >
    > A 7. lépés, a **diagnosztikai tárfiók**, győződjön meg arról, hogy ki **myvmstorageaccount**.

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Ekkor átkerül a **felülvizsgálat + létrehozása** oldal és az Azure ellenőrzi a konfigurációt.

1. Ha azt láthatja, hogy **ellenőrzésen**válassza **létrehozás**.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Miután létrehozott *myVm1*, az interneten keresztül csatlakozzanak hozzá.

1. Adja meg a portál keresősávjában *myVm1*.

1. Kattintson a **Csatlakozás** gombra.

    ![Csatlakozás virtuális géphez](./media/quick-create-portal/connect-to-virtual-machine.png)

    Kiválasztása után a **Connect** gomb **csatlakozhat a virtuális gép** nyílik meg.

1. Válassza ki **RDP-fájl letöltése**. Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépet.

1. Nyissa meg a letöltött *.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy ki kell választania **további lehetőségek** > **másik fiók használata**, a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy tanúsítványfigyelmeztetés folyamat jelenhet meg. Ha a tanúsítványra vonatkozó figyelmeztetést kap, válassza ki a **Igen** vagy **Folytatás**.

1. A virtuális gép asztalához megjelenik, miután minimalizálása érdekében, hogy térjen vissza a helyi számítógépére.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A távoli asztali, *myVm1*, nyissa meg a Powershellt.

1. Írja be a `ping myVm2` (igen) kifejezést.

    Kap vissza ezt az üzenetet hasonló:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A `ping` sikertelen lesz, mert `ping` használja az Internet Control Message Protocol (ICMP). Alapértelmezés szerint az ICMP nem engedélyezett, a Windows tűzfalon keresztül.

1. Engedélyezéséhez *myVm2* pingelni *myVm1* egy későbbi lépésben adja meg ezt a parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Hogy a parancs engedélyezi az ICMP bejövő, a Windows tűzfalon keresztül:

1. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot.

1. Hajtsa ismét végre a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet) című részben leírt lépéseket, de most csatlakozzon a *myVm2* virtuális géphez.

1. Egy parancssorba írja be a következőt: `ping myvm1`.

    Kap vissza ezt az üzenetet hasonló:

    ```powershell
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Mivel az előző lépésben átengedte az ICMP-t a Windows tűzfalon a *myVm1* virtuális gép esetében, válaszokat fog kapni a *myVm1* virtuális géptől.

1. Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, a virtuális hálózat és a virtuális gépek, törölje az erőforráscsoportot és az összes benne található erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**.

1. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Adja meg *myResourceGroup* a **írja be az ERŐFORRÁSCSOPORT nevét** válassza **törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Egy virtuális géphez az internetről csatlakozik, és privát kommunikációt a két virtuális gépet. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md).

Alapértelmezés szerint az Azure virtuális gépek korlátlan privát kommunikációját lehetővé teszi. Ezzel szemben csak lehetővé teszi a bejövő távoli asztali kapcsolatok Windows virtuális gépekhez az internetről. Virtuális gép hálózati kommunikációt a különböző típusú konfigurálásával kapcsolatos további tudnivalókért keresse fel a [hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyag.