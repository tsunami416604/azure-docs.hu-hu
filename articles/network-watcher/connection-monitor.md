---
title: "Hálózati kapcsolatok Azure hálózati figyelőt - Azure-portálon való figyeléséhez |} Microsoft Docs"
description: "Útmutató a hálózati kapcsolat figyelő Azure hálózati figyelőt az Azure portál használatával."
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Hálózati kapcsolatok figyelő Azure hálózati figyelőt az Azure portál használatával

Útmutató a kapcsolat a figyelő a figyelheti az Azure virtuális gép és egy IP-cím közötti hálózati kapcsolat. Az IP-cím egy másik Azure-erőforrás, vagy az interneten vagy a helyi erőforrás rendelhetők.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő lépések végrehajtása előtt meg kell felelnie a következő előfeltételek teljesülését:

* A kapcsolat a figyelni kívánt hálózati figyelőt régióban példánya. Ha még nem rendelkezik egy, létrehozhat egy, a lépések végrehajtásával [hozzon létre egy Azure hálózati figyelőt példányt](network-watcher-create.md).
* A figyelheti a virtuális gép.
* Rendelkezik a `AzureNetworkWatcherExtension` telepítve a kapcsolatot a figyelni kívánt virtuális gépen. A bővítmény telepítéséhez Windows virtuális gépre, tekintse át [a Windows Azure hálózati figyelő ügynök virtuálisgép-bővítmény](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és a bővítmény telepítéséhez egy Linux virtuális gép itt talál: [Azure hálózati figyelő ügynök Linux virtuálisgép-bővítmény](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Egy kapcsolat figyelő létrehozása

1. A portál bal oldalán válassza ki a **további szolgáltatások**.
2. Kezdje el begépelni *hálózati figyelőt*. Ha **hálózati figyelőt** megjelenik a keresési eredmények között, jelölje be.
3. A **figyelés**, jelölje be **kapcsolat monitor (előzetes verzió)**. Az előzetes funkciók nem rendelkeznek azonos szintű megbízhatóság vagy régiónkénti elérhetőség, mint a szolgáltatások általában kiadási.
4. Válassza ki **+ Hozzáadás**.
5. Adja meg vagy válassza ki a megfelelő adatokat a szeretne figyelni, és válassza ki a kapcsolat **Hozzáadás**. Ebben a példában közötti kapcsolatot a *myVmSource* és *myVmDestination* virtuális gépeket a rendszer figyeli a 80-as porton keresztül.
    
    |  Beállítás                                 |  Érték               |
    |  -------------------------------------   |  ------------------- |
    |  Name (Név)                                    |  myConnectionMonitor |
    |  Forrás virtuális gép                  |  myVmSource          |
    |  Forrásport                             |                      |
    |  Cél, válassza ki a virtuális gépek   |  myVmDestination     |
    |  Célport                        |  80                  |

6. A figyelés megkezdése. Figyelő kapcsolat 60 másodpercenként mintavétel.
7. Kapcsolat a figyelő az átlagos oda-vissza idő és százalékos mintavételt eleget nem tevő jeleníti meg. Figyelő a rácsban, vagy egy grafikonon tekintheti meg.

## <a name="next-steps"></a>További lépések

- Csomag rögzíti a virtuális gép riasztások szerint automatizálása [figyelmeztetés csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md).

- Határozza meg, hogy bizonyos forgalom engedélyezett kívül a virtuális gép vagy a [IP folyamata ellenőrizze](network-watcher-check-ip-flow-verify-portal.md).