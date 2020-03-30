---
title: Virtuális hálózat létrehozása - rövid útmutató - Azure portal
titleSuffix: Azure Virtual Network
description: 'Rövid útmutató: Hozzon létre egy virtuális hálózatot az Azure Portalon. Ezek a hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek, biztonságosan kommunikálnak egymással és az interneten.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240071"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Rövid útmutató: Virtuális hálózat létrehozása az Azure Portallal

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával. Két virtuális gépet (VM-eket) telepít. Ezután biztonságosan kommunikálhat a virtuális gépek között, és csatlakozhat a virtuális gépekhez az internetről. A virtuális hálózat az Azure-beli magánhálózat alapvető építőköve. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek biztonságosan kommunikáljanak egymással és az internettel.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Az Azure Piactéren válassza a **Hálózati** > **virtuális hálózat**lehetőséget.

1. A **Virtuális hálózat létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza **az Új létrehozása**lehetőséget, írja be a *myResourceGroup parancsot,* majd kattintson **az OK gombra.** |
    | Név | Adja meg *a myVirtualNetwork*. |
    | Hely | Válassza az **USA keleti régiója** lehetőséget.|

1. Válassza a **Tovább: IP-címek**és **az IPv4-címterület** *10.1.0.0/16*értéket.

1. Válassza **az Alhálózat hozzáadása**lehetőséget, majd adja meg a *myVirtualSubnet* nevet az **Alhálózat nevéhez,** a *10.1.0.0/24* értéket pedig az **Alhálózati címtartományhoz.**

1. Válassza **a Hozzáadás**lehetőséget, majd a **Véleményezés + create lehetőséget.** Hagyja a többit alapértelmezettként, és válassza a **Létrehozás lehetőséget.**

1. A **Virtuális hálózat létrehozása**csoportban válassza a **Létrehozás**lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.

1. Az Azure Piactéren válassza **a Compute** > **Windows Server 2019 Datacenter lehetőséget.** Kattintson a **Létrehozás** gombra.

1. A **Virtuális gép létrehozása - Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az erőforráscsoportot az előző szakaszban hozta létre. |
    | **Példány részletei** |  |
    | Virtuális gép neve | Adja meg *a myVm1 értéket.* |
    | Régió | Válassza az **USA keleti régiója** lehetőséget. |
    | Rendelkezésre állási beállítások | Alapértelmezés szerint **nincs szükség infrastruktúra-redundanciára.** |
    | Kép | Alapértelmezés szerint a **Windows Server 2019 Datacenter .** |
    | Méret | Alapértelmezett a **Standard DS1 v2**. |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adja meg az Ön által választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **Bejövőport-szabályok** |  |
    | Nyilvános bejövő portok | Válassza **a Kijelölt portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Írja be *a HTTP (80)* és *az RDP (3389) értéket.* |
    | **Takarítson meg pénzt** |  |
    | Már van Windows-licence? | Alapértelmezett érték: **Nem**. |

1. Válassza a **Tovább: Lemezek**lehetőséget.

1. A **Virtuális gép létrehozása - Lemezek**csoportban tartsa meg az alapértelmezett beállításokat, és válassza a **Tovább: Hálózat lehetőséget.**

1. A **Virtuális gép létrehozása - Hálózat csoportban**válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Alapértelmezés szerint **myVirtualNetwork**. |
    | Alhálózat | Alapértelmezés szerint **a myVirtualSubnet (10.1.0.0/24)**. |
    | Nyilvános IP-cím | Alapértelmezés szerint **(új) myVm-ip**. |
    | Hálózati adapter hálózati biztonsági csoportja | Alapértelmezés szerint **az Alap**. |
    | Nyilvános bejövő portok | Alapértelmezés szerint **a kijelölt portok engedélyezése**. |
    | Bejövő portok kiválasztása | Alapértelmezett: **HTTP** és **RDP**.

1. Válassza a **Tovább: Management lehetőséget.**

1. A **Virtuális gép létrehozása - Kezelés**csoportban a Diagnosztikai **tárfiókhoz**válassza az Új létrehozása **lehetőséget.**

1. A **Tárfiók létrehozása**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg *a myvmstorageaccount -ot.* Ha ez a név foglalt, hozzon létre egy egyedi nevet.|
    | Fióktípus | Alapértelmezett **tárolás (általános célú v1)**. |
    | Teljesítmény | Alapértelmezett érték **a Normál értékre.** |
    | Replikáció | Alapértelmezés szerint **helyileg redundáns tárolás (LRS)**. |

1. Válassza **az OK**lehetőséget, majd válassza a Véleményezés + **létrehozás**lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt.

1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Ismételje meg az eljárást az előző szakaszban egy másik virtuális gép létrehozásához.

> [!IMPORTANT]
> A **virtuális gép nevéhez**írja be a *myVm2 értéket.*
>
> A **diagnosztikai tárfiók,** győződjön meg róla, hogy válassza **ki a myvmstorageaccount,** létrehozása helyett egyet.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Miután létrehozta *a myVm1-et,* csatlakozzon az internethez.

1. Az Azure Portalon keresse meg és válassza a *myVm1*parancsot.

1. Válassza **a Csatlakozás**lehetőséget, majd az **RDP**lehetőséget.

    ![Csatlakozás virtuális géphez](./media/quick-create-portal/connect-to-virtual-machine.png)

    Megnyílik **a Csatlakozás** lap.

1. Válassza **az RDP-fájl letöltése lehetőséget.** Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg az RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

    > [!NOTE]
    > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. Bejelentkezéskor tanúsítványfigyelmeztetést kaphat. Ha tanúsítványfigyelmeztetést kap, válassza az **Igen** vagy **a Folytatás**lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A *myVm1*távoli asztalán nyissa meg a PowerShellt.

1. Írja be a `ping myVm2` (igen) kifejezést.

    A kimenethez hasonló üzenetet fog kapni:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A `ping` sikertelen, `ping` mert az Internet Control Message Protocol (ICMP) protokollt használja. Alapértelmezés szerint az ICMP nem engedélyezett a Windows tűzfalon keresztül.

1. Ha azt szeretné, hogy *a myVm2* egy későbbi lépésben pingelhesse a *myVm1-et,* írja be ezt a parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ez a parancs lehetővé teszi az ICMP bejövő a Windows tűzfalon keresztül:

1. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot.

1. Hajtsa ismét végre a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet) című részben leírt lépéseket, de most csatlakozzon a *myVm2* virtuális géphez.

1. Egy parancssorba írja be a következőt: `ping myvm1`.

    Kapsz vissza valami ilyesmi:

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

    A *myVm1*választ kap, mert a 3. *myVm1*

1. Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Egy virtuális géphez csatlakozott az internetről, és biztonságosan kommunikált a két virtuális gép között.

Ha befejezte a virtuális hálózat és a virtuális gépek használatát, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Keresse meg és válassza a *myResourceGroup elemet.*

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a *myResourceGroup értéket* **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

A virtuális hálózati beállításokról a Virtuális hálózat létrehozása, módosítása és törlése című témakörben olvashat [bővebben.](manage-virtual-network.md)

Alapértelmezés szerint az Azure lehetővé teszi a virtuális gépek közötti biztonságos kommunikációt. Az Azure csak az internetről engedélyezi a Windows virtuális gépekhez érkező távoli asztali kapcsolatokat. A virtuális gép hálózati kommunikációjának típusairól a [Hálózati forgalom szűrése (Hálózati forgalom szűrése) témakörben](tutorial-filter-network-traffic.md)olvashat bővebben.
