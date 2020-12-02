---
title: Linux rendszerű virtuális gépek közös elhelyezése
description: Ismerje meg, hogy a Linux rendszerhez készült Azure-beli virtuális gépek erőforrásai hogyan használhatók fel a késés javítására.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 304623ca50fd030ab6e016b940f8be52819c161a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500631"
---
# <a name="co-locate-resources-for-improved-latency"></a>Közös elhelyezésű erőforrások a késleltetés csökkentéséhez

Amikor üzembe helyezi az alkalmazást az Azure-ban, a különböző régiókba vagy rendelkezésre állási zónákba tartozó példányok elterjedése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 

## <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>További lépések

Helyezzen üzembe egy virtuális gépet az Azure CLI használatával a [közelségi elhelyezési csoportba](proximity-placement-groups.md) .

Megtudhatja, hogyan [tesztelheti a hálózati késést](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Útmutató a [hálózati átviteli sebesség optimalizálásához](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Ismerje meg, hogyan [használhatók a Proximity csoportok az SAP-alkalmazásokkal](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).