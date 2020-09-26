---
title: Linux rendszerű virtuális gépek közös elhelyezése
description: Ismerje meg, hogy a Linux rendszerhez készült Azure-beli virtuális gépek erőforrásai hogyan használhatók fel a késés javítására.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 6bf01f4da6ce96b413c87b81c14a998603cebd6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279723"
---
# <a name="co-locate-resources-for-improved-latency"></a>Közös elhelyezésű erőforrások a késleltetés csökkentéséhez

Amikor üzembe helyezi az alkalmazást az Azure-ban, a különböző régiókba vagy rendelkezésre állási zónákba tartozó példányok elterjedése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 

## <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Következő lépések

Helyezzen üzembe egy virtuális gépet az Azure CLI használatával a [közelségi elhelyezési csoportba](proximity-placement-groups.md) .

Megtudhatja, hogyan [tesztelheti a hálózati késést](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Útmutató a [hálózati átviteli sebesség optimalizálásához](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Ismerje meg, hogyan [használhatók a Proximity csoportok az SAP-alkalmazásokkal](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
