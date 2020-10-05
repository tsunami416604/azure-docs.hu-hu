---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure Portal
titleSuffix: Azure Virtual Network
description: 'Gyors útmutató: virtuális hálózat létrehozása a Azure Portalban. Ezek a hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek, a biztonságos kommunikációt egymással és az internettel.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: dfeb9c6bc3dbe33881fc51ccb08a776b827dfe9c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "86082437"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rövid útmutató: Virtuális hálózat létrehozása az Azure Portallal

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot a Azure Portal használatával. Két virtuális gépet (VM) telepít. Ezután biztonságosan kommunikál a virtuális gépek között, és csatlakozhat a virtuális gépekhez az internetről. A virtuális hálózat az Azure-beli magánhálózat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek számára, hogy biztonságosan kommunikáljanak egymással és az internettel.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Az Azure Marketplace-en válassza a **hálózatkezelés**  >  **virtuális hálózat**lehetőséget.

1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Név | Adja meg a *myVirtualNetwork*. |
    | Hely | Válassza az **USA keleti régiója** lehetőséget.|

1. Válassza a **tovább lehetőséget: IP-címek**és **IPv4-címterület**esetében adja meg a *10.1.0.0/16*értéket.

1. Válassza az **alhálózat hozzáadása**lehetőséget, majd adja meg a *myVirtualSubnet* az alhálózat **neve** és a *10.1.0.0/24* **alhálózati címtartomány**mezőben.

1. Válassza a **Hozzáadás**, majd a **felülvizsgálat + létrehozás**elemet. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.

1. A **virtuális hálózat létrehozása**lapon válassza a **Létrehozás**lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.

1. Az Azure Marketplace-en válassza a **számítás**  >  **Windows Server 2019 Datacenter**lehetőséget. Kattintson a **Létrehozás** gombra.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az erőforráscsoportot az előző szakaszban hozta létre. |
    | **Példány adatai** |  |
    | Virtuális gép neve | Adja meg a *myVm1*. |
    | Region | Válassza az **USA keleti régiója** lehetőséget. |
    | Rendelkezésre állási beállítások | Alapértelmezés szerint **nem szükséges infrastruktúra-redundancia**. |
    | Kép | Alapértelmezett érték a **Windows Server 2019 Datacenter rendszerhez**. |
    | Méret | Az alapértelmezett érték a **standard DS1 v2**. |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **Bejövő portok szabályai** |  |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Válassza ki a bejövő portokat | Adja meg a *http (80)* és az *RDP (3389)* értéket. |
    | **Pénz megtakarítása** |  |
    | Már van Windows-licence? | Alapértelmezett érték: **nem**. |

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **virtuális gép létrehozása – lemezek**területen tartsa meg az alapértelmezett beállításokat, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Az alapértelmezett érték a **myVirtualNetwork**. |
    | Alhálózat | Az alapértelmezett érték a **myVirtualSubnet (10.1.0.0/24)**. |
    | Nyilvános IP-cím | Alapértelmezett érték **(új) myVm – IP**. |
    | NIC hálózati biztonsági csoport | Alapértelmezés szerint **alapszintű**. |
    | Nyilvános bejövő portok | Alapértelmezés a **kiválasztott portok engedélyezéséhez**. |
    | Válassza ki a bejövő portokat | Az alapértelmezett érték a **http** és az **RDP**.

1. Válassza a **Tovább: kezelés**lehetőséget.

1. A **virtuálisgép-kezelés létrehozása** **diagnosztikai Storage-fiókhoz**területen válassza az **új létrehozása**lehetőséget.

1. A **Storage-fiók létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a *myvmstorageaccount*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Fiók altípusa | Alapértelmezett a **Storage-hoz (általános célú v1)**. |
    | Teljesítmény | Alapértelmezett érték: **standard**. |
    | Replikáció | Az alapértelmezett érték a **helyileg redundáns tárolás (LRS)**. |

1. Válassza **az OK**, majd a **felülvizsgálat + létrehozás**elemet. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

1. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Ismételje meg az eljárást az előző szakaszban egy másik virtuális gép létrehozásához.

> [!IMPORTANT]
> A **virtuális gép neve**mezőbe írja be a következőt: *myVm2*.
>
> **Diagnosztikai Storage-fiók**esetén ügyeljen arra, hogy a létrehozása helyett a **myvmstorageaccount**válassza ki.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

A *myVm1*létrehozása után kapcsolódjon az internethez.

1. A Azure Portal keresse meg és válassza ki a *myVm1*.

1. Válassza a **kapcsolat**, majd az **RDP**elemet.

    ![Csatlakozás virtuális géphez](./media/quick-create-portal/connect-to-virtual-machine.png)

    Megnyílik a **kapcsolat** lap.

1. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg az RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

    > [!NOTE]
    > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezéskor a tanúsítványra vonatkozó figyelmeztetés jelenhet meg. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A *myVm1*távoli asztal nyissa meg a PowerShellt.

1. Írja be a következő szöveget: `ping myVm2`.

    Ehhez a kimenethez hasonló üzenet jelenik meg:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A `ping` sikertelen, mert `ping` a Internet Control Message Protocol (ICMP) protokollt használja. Alapértelmezés szerint az ICMP nem engedélyezett a Windows tűzfalon keresztül.

1. Ha engedélyezni szeretné a *myVm2* számára a *myVm1* pingelését egy későbbi lépésben, írja be a következő parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ez a parancs engedélyezi az ICMP-t a Windows tűzfalon keresztül:

1. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot.

1. Hajtsa ismét végre a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet) című részben leírt lépéseket, de most csatlakozzon a *myVm2* virtuális géphez.

1. Egy parancssorba írja be a következőt: `ping myvm1`.

    Az alábbihoz hasonló üzenet jelenik meg:

    ```output
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

1. Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Az internetről csatlakozik egy virtuális géphez, és biztonságosan kommunikál a két virtuális gép között.

Ha végzett a virtuális hálózat és a virtuális gépek használatával, törölje az erőforráscsoportot és a benne található összes erőforrást:

1. Keresse meg és válassza ki a *myResourceGroup*.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a *myResourceGroup* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

További információ a virtuális hálózati beállításokról: [virtuális hálózat létrehozása, módosítása vagy törlése](manage-virtual-network.md).

Az Azure alapértelmezés szerint lehetővé teszi a virtuális gépek közötti biztonságos kommunikációt. Az Azure csak az internetről bejövő távoli asztali kapcsolatokat engedélyez a Windows rendszerű virtuális gépeken. További információ a virtuálisgép-hálózati kommunikáció típusairól: [hálózati forgalom szűrése](tutorial-filter-network-traffic.md).

> [!NOTE] 
> Az Azure-szolgáltatások pénzbe kerülnek. A Azure Cost Management segítségével megadhatja a költségvetéseket, és konfigurálhatja a riasztásokat, hogy megőrizze a költségek szabályozását. Cost Managementával elemezheti, kezelheti és optimalizálhatja Azure-költségeit. További információkért tekintse meg a [költségek elemzését](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)ismertető rövid útmutatót.