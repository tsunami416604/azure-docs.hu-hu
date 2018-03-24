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
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Hálózati kapcsolatok figyelő Azure hálózati figyelőt az Azure portál használatával

Útmutató a kapcsolat a figyelő a figyelheti az Azure virtuális gép és egy IP-cím közötti hálózati kapcsolat. Figyelő kapcsolat kapcsolati szintű figyelést biztosít. A kapcsolat a forrás és cél IP-cím és port kombinációja típusúként van definiálva. Kapcsolat a figyelő lehetővé teszi, hogy a helyzetekben, például a figyelés és a virtuális gép fut az SQL server ugyanazon vagy másik virtuális hálózatban, 1433-as porton keresztül egy virtuális hálózatot a virtuális gép közötti kapcsolatot. Figyelő kapcsolat kapcsolat késési, egy Azure-figyelő metrika, 60 másodpercenként rögzített biztosít. Emellett a Ugrás által Ugrás topológia lehetővé teszi, és a kapcsolat érintő konfigurációs problémák azonosítja.


## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő lépések végrehajtása előtt meg kell felelnie a következő előfeltételek teljesülését:

* A kapcsolat a figyelni kívánt hálózati figyelőt régióban példánya. Ha még nem rendelkezik egy, létrehozhat egy, a lépések végrehajtásával [hozzon létre egy Azure hálózati figyelőt példányt](network-watcher-create.md).
* A figyelheti a virtuális gép.
* Rendelkezik a `AzureNetworkWatcherExtension` telepítve a kapcsolatot a figyelni kívánt virtuális gépen. A bővítmény telepítéséhez Windows virtuális gépre, tekintse át [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és a bővítmény telepítéséhez egy Linux virtuális gép itt talál: [Azure hálózati figyelő ügynök Linux virtuálisgép-bővítmény](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Egy kapcsolat figyelő létrehozása

Az alábbi lépéseket a 80-as és az 1433-as porton keresztül a cél virtuális gép figyelés kapcsolat engedélyezése:

1. A portál bal oldalán válassza ki a **további szolgáltatások**.
2. Kezdje el begépelni *hálózati figyelőt*. Ha **hálózati figyelőt** megjelenik a keresési eredmények között, jelölje be.
3. A **figyelés**, jelölje be **kapcsolat monitor (előzetes verzió)**. Az előzetes funkciók nem rendelkeznek azonos szintű megbízhatóság vagy régiónkénti elérhetőség, mint a szolgáltatások általában kiadási.
4. Válassza ki **+ Hozzáadás**.
5. Adja meg vagy válassza ki a kapcsolat figyelésére, és válassza ki a kívánt adatokat **Hozzáadás**. A példában a következő ábrán látható, a figyelt kapcsolat között van a *MultiTierApp0* és *Database0* virtuális gépek:

    ![Vegye fel a kapcsolatot a figyelő](./media/connection-monitor/add-connection-monitor.png)

    A figyelés megkezdése. Figyelő kapcsolat 60 másodpercenként mintavétel.

## <a name="view-connection-monitoring"></a>Nézet kapcsolat ellenőrzése

1. Végezze el a lépéseket 1 – 3 [hozzon létre egy kapcsolat figyelő](#create-a-connection-monitor) kapcsolat figyelési megtekintéséhez.
2. A következő képen a AppToDB(80) kapcsolat részletes adatainak megjelenítése. A **állapot** érhető el. A **nézet diagramot** jeleníti meg a **átlagosan oda-vissza** és **vizsgálat nem sikerült %**. A diagram bemutatja a Ugrás-hop, és jeleníti meg, hogy nincs probléma cél reachability vannak hatással.

    ![Nézet kapcsolat figyelője](./media/connection-monitor/view-connection-monitor.png)

3. Megtekintés a *AppToDB(1433)* figyelő, a következő ábrán látható azt látja, hogy a azonos forrás és cél virtuális gépre, az állapot nem érhető el 1433-as porton keresztül. A **rácsnézethez** ebben a forgatókönyvben a szolgáltatás által az Ugrás az Ugrás által és a reachability érintő problémát. Ebben az esetben egy NSG-szabály 1433-as portot, a második Ugrás minden forgalmat blokkolja.

    ![Nézet kapcsolat figyelője](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>További lépések

- Csomag rögzíti a virtuális gép riasztások szerint automatizálása [figyelmeztetés csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md).
- Határozza meg, hogy bizonyos forgalom engedélyezett kívül a virtuális gép vagy a [IP folyamata ellenőrizze](network-watcher-check-ip-flow-verify-portal.md).