---
title: Virtuális gépek közös elhelyezése a jobb késés érdekében
description: Ismerje meg, hogyan javíthatja a késést az Azure-beli virtuális gépek erőforrásainak közös elhelyezése.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: eb7b5d3b477a511432dbd3ee8a6b382002aaab44
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488466"
---
# <a name="co-locate-resource-for-improved-latency"></a>Erőforrás közös keresése a jobb késés érdekében

Amikor üzembe helyezi az alkalmazást az Azure-ban, a különböző régiókba vagy rendelkezésre állási zónákba tartozó példányok elterjedése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 


## <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>További lépések

Helyezzen üzembe egy virtuális gépet egy [közeli elhelyezési csoportba](proximity-placement-groups.md) Azure PowerShell használatával.

Megtudhatja, hogyan [tesztelheti a hálózati késést](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Útmutató a [hálózati átviteli sebesség optimalizálásához](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Ismerje meg, hogyan [használhatók a Proximity csoportok az SAP-alkalmazásokkal](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).