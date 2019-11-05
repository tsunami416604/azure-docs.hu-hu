---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure Portal
titlesuffix: Azure Virtual Network
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával. A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek, az egymással és az internettel való biztonságos kommunikációt
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: d8e95f9c345a943eb458800b852640e3f1fde907
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488480"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rövid útmutató: Virtuális hálózat létrehozása az Azure Portallal

A virtuális hálózat az Azure-beli magánhálózat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy biztonságosan kommunikáljanak egymással és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot a Azure Portal használatával. Ezután két virtuális gépet telepíthet a virtuális hálózatba, és biztonságosan kommunikálhat a két virtuális gép között, és csatlakozhat az internetről a virtuális gépekhez.


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Bejelentkezés az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget.

2. Az Azure Marketplace-en válassza a **hálózatkezelés** > **virtuális hálózat**lehetőséget.

3. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *myVirtualNetwork*. |
    | Címtér | Adja meg a *10.1.0.0/16*értéket. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Földrajzi egység | Válassza az **USA keleti régiója** lehetőséget.|
    | Alhálózat – név | Adja meg a *myVirtualSubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24*értéket. |

4. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget.

2. Az Azure Marketplace-en válassza a **számítás** > **Windows Server 2019 Datacenter**elemet.

3. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre. |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm1*. |
    | Régió | Válassza az **USA keleti régiója** lehetőséget. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Image (Kép) | Hagyja meg az alapértelmezett **Windows Server 2019 Datacenter adatközpontot**. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |

4. Válassza a **Tovább: lemezek**lehetőséget.

5. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

6. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **myVirtualNetwork**. |
    | Alhálózat | Hagyja meg az alapértelmezett **myVirtualSubnet (10.1.0.0/24)** . |
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.

7. Válassza a **Tovább: kezelés**lehetőséget.

8. A **virtuálisgép-kezelés létrehozása** **diagnosztikai Storage-fiókhoz**területen válassza az **új létrehozása**lehetőséget.

9. A **Storage-fiók létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *myvmstorageaccount*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Fióktípus | Hagyja meg az alapértelmezett **tárolót (általános célú v1)** . |
    | Teljesítmény | Hagyja meg az alapértelmezett **standard**értéket. |
    | Replikáció | Hagyja meg az alapértelmezett **helyileg redundáns tárolást (LRS)** . |

10. Kattintson az **OK** gombra.

11. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

12. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

1. Hajtsa végre a fenti 1. és 9. lépést.

    > [!NOTE]
    > A 2. lépésben a **virtuális gép neve**mezőbe írja be a következőt: *myVm2*.
    >
    > A 7. lépésben a **diagnosztikai Storage-fiókhoz**válassza a **myvmstorageaccount**lehetőséget.

2. Válassza az **Áttekintés + létrehozás** lehetőséget. Ekkor megjelenik a **felülvizsgálat + létrehozás** oldal, és az Azure ellenőrzi a konfigurációt.

3. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

A *myVm1*létrehozása után kapcsolódjon az internethez.

1. A portál keresési sávján adja meg a *myVm1*.

2. Kattintson a **Csatlakozás** gombra.

    ![Csatlakozás virtuális géphez](./media/quick-create-portal/connect-to-virtual-machine.png)

    A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

3. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

4. Nyissa meg a letöltött *. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    2. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** kell kiválasztania > **eltérő fiókot használjon**.

5. Kattintson az **OK** gombra.

6. A bejelentkezési folyamat során a tanúsítványra vonatkozó figyelmeztetés jelenhet meg. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

7. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A *myVm1*távoli asztal nyissa meg a PowerShellt.

2. Írja be a `ping myVm2` (igen) kifejezést.

    Ehhez hasonló üzenet jelenik meg:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A `ping` sikertelen, mert a `ping` a Internet Control Message Protocol (ICMP) protokollt használja. Alapértelmezés szerint az ICMP nem engedélyezett a Windows tűzfalon keresztül.

3. Ha engedélyezni szeretné a *myVm2* számára a *myVm1* pingelését egy későbbi lépésben, írja be a következő parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ez a parancs engedélyezi az ICMP-t a Windows tűzfalon keresztül:

4. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot.

5. Hajtsa ismét végre a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet) című részben leírt lépéseket, de most csatlakozzon a *myVm2* virtuális géphez.

6. Egy parancssorba írja be a következőt: `ping myvm1`.

    Az alábbihoz hasonló üzenet jelenik meg:

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

    A *myVm1*válaszait kapja, mivel a 3. lépésben a *myVm1* virtuális gépen a Windows tűzfalon keresztül engedélyezte az ICMP-t.

7. Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális hálózat és a virtuális gépek használatával, törölje az erőforráscsoportot és a benne található összes erőforrást:

1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza a **myResourceGroup** lehetőséget a keresési eredmények közül.

2. Válassza az **Erőforráscsoport törlése** elemet.

3. Írja be a *myResourceGroup* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Az internetről csatlakozik egy virtuális géphez, és biztonságosan kommunikál a két virtuális gép között. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md).

Az Azure alapértelmezés szerint lehetővé teszi a virtuális gépek közötti korlátlan biztonságos kommunikációt. Ezzel ellentétben csak az internetről bejövő távoli asztali kapcsolatokat engedélyez a Windows rendszerű virtuális gépeken. Ha többet szeretne megtudni a különböző típusú virtuálisgép-hálózati kommunikációk konfigurálásáról, lépjen a [hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyagra.
