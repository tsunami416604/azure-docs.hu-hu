---
title: Windows Azure-beli virtuális gépek közös elhelyezése
description: Ismerje meg, hogyan javíthatja a késést az Azure-beli virtuális gépek erőforrásainak közös elhelyezése.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266792"
---
# <a name="co-locate-resource-for-improved-latency"></a>Erőforrás közös keresése a jobb késés érdekében

Amikor üzembe helyezi az alkalmazást az Azure-ban, a különböző régiókba vagy rendelkezésre állási zónákba tartozó példányok elterjedése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 


## <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Következő lépések

Helyezzen üzembe egy virtuális gépet egy [közeli elhelyezési csoportba](proximity-placement-groups.md) Azure PowerShell használatával.

Megtudhatja, hogyan [tesztelheti a hálózati késést](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Útmutató a [hálózati átviteli sebesség optimalizálásához](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Ismerje meg, hogyan [használhatók a Proximity csoportok az SAP-alkalmazásokkal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).