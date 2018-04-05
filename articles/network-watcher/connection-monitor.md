---
title: Hálózati kapcsolatok Azure hálózati figyelőt - Azure-portálon való figyeléséhez |} Microsoft Docs
description: Útmutató a hálózati kapcsolat figyelő Azure hálózati figyelőt az Azure portál használatával.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: 0d550d3bda119cfcb9ecc6f852006d5e325fdfa3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Hálózati kapcsolatok figyelő Azure hálózati figyelőt az Azure portál használatával

Útmutató a kapcsolat a figyelő a figyelheti egy Azure virtuális gép (VM) és IP-cím közötti hálózati kapcsolat. Kapcsolat a figyelő a forrás és cél IP-cím és port figyelést biztosít. Kapcsolat a figyelő lehetővé teszi, hogy a helyzetekben, például egy virtuális gép futó SQL server ugyanazon vagy másik virtuális hálózatban, és egy virtuális hálózatot a virtuális gép közötti kapcsolatot ellenőrzése az 1433-as porton keresztül. Figyelő kapcsolat kapcsolat késési, egy Azure-figyelő metrika, 60 másodpercenként rögzített biztosít. Emellett a Ugrás által Ugrás topológia lehetővé teszi, és a kapcsolat érintő konfigurációs problémák azonosítja.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő lépések végrehajtása előtt meg kell felelnie a következő előfeltételek teljesülését:

* A kapcsolat a figyelni kívánt hálózati figyelőt régióban példánya. Ha még nem rendelkezik egy, létrehozhat egy, a lépések végrehajtásával [hozzon létre egy Azure hálózati figyelőt példányt](network-watcher-create.md).
* A virtuális gépek a figyelheti. A virtuális gép létrehozásához, lásd: hozzon létre egy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) vagy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) virtuális gép.
* Rendelkezik a `AzureNetworkWatcherExtension` a kapcsolatot a figyelni kívánt virtuális gépen telepítve. A bővítmény telepítéséhez egy Windows virtuális gépre, tekintse át [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és a bővítmény telepítéséhez egy Linux virtuális gép itt talál: [az Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Egy kapcsolat figyelő létrehozása

Az alábbi lépéseket a kapcsolat a cél virtuális gép 80-as és az 1433-as porton keresztül történő figyelés engedélyezése:

1. A portál bal oldalán válassza ki a **minden szolgáltatás**.
2. Kezdje el begépelni *hálózati figyelőt* a a **szűrő** mezőbe. Ha **hálózati figyelőt** megjelenik a keresési eredmények között, jelölje be.
3. A **figyelés**, jelölje be **kapcsolat figyelő**.
4. Válassza ki **+ Hozzáadás**.
5. Adja meg vagy válassza ki a kapcsolat figyelésére, és válassza ki a kívánt adatokat **Hozzáadás**. A példában a következő ábrán látható, a figyelt kapcsolat származik a *MultiTierApp0* virtuális Gépet a *Database0* VM 80-as porton keresztül:

    ![Vegye fel a kapcsolatot a figyelő](./media/connection-monitor/add-connection-monitor.png)

    A figyelés megkezdése. Figyelő kapcsolat 60 másodpercenként mintavétel.
6. Végezze el ismét az 5. lépés azonos forrás és cél virtuális gépek és a következő értékek megadásával:
    
    |Beállítás  |Érték          |
    |---------|---------      |
    |Name (Név)     | AppToDB(1433) |
    |Port     | 1433          |

## <a name="view-connection-monitoring"></a>Nézet kapcsolat ellenőrzése

1. Végezze el a lépéseket 1 – 3 [hozzon létre egy kapcsolat figyelő](#create-a-connection-monitor) kapcsolat figyelési megtekintéséhez.
2. Az alábbi képen látható részleteit a *AppToDB(80)* kapcsolat. A **állapot** érhető el. A **nézet diagramot** jeleníti meg a **átlagosan oda-vissza** és **vizsgálat nem sikerült %**. A diagram bemutatja a Ugrás-hop, és jeleníti meg, hogy nincs probléma cél reachability vannak hatással.

    ![A diagramnézetnek](./media/connection-monitor/view-graph.png)

3. Megtekintés a *AppToDB(1433)* kapcsolat a következő ábrán látható azt látja, hogy ugyanazon forrás és cél virtuális gépek, az állapot nem érhető el 1433-as porton keresztül. A **rácsnézethez** ebben a forgatókönyvben a szolgáltatás által az Ugrás az Ugrás által és a reachability érintő problémát. Ebben az esetben egy NSG-szabály 1433-as portot, a második Ugrás minden forgalmat blokkolja.

    ![Rácsnézet](./media/connection-monitor/view-grid.png)

## <a name="next-steps"></a>További lépések

- Csomag rögzíti a virtuális gép riasztások szerint automatizálása [figyelmeztetés csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md).
- Határozza meg, hogy bizonyos forgalom engedélyezett kívül a virtuális gép vagy a [IP folyamata ellenőrizze](network-watcher-check-ip-flow-verify-portal.md).