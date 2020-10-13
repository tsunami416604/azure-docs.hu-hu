---
title: Linux rendszerű virtuális gépek közös elhelyezése
description: Ismerje meg, hogy a Linux rendszerhez készült Azure-beli virtuális gépek erőforrásai hogyan használhatók fel a késés javítására.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 822d67211982526bdd7a36cb5922f33b448eb7c7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973376"
---
# <a name="co-locate-resources-for-improved-latency"></a>Közös elhelyezésű erőforrások a késleltetés csökkentéséhez

Amikor üzembe helyezi az alkalmazást az Azure-ban, a különböző régiókba vagy rendelkezésre állási zónákba tartozó példányok elterjedése hálózati késést okoz, ami hatással lehet az alkalmazás általános teljesítményére. 

## <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Következő lépések

Helyezzen üzembe egy virtuális gépet az Azure CLI használatával a [közelségi elhelyezési csoportba](proximity-placement-groups.md) .

Megtudhatja, hogyan [tesztelheti a hálózati késést](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json).

Útmutató a [hálózati átviteli sebesség optimalizálásához](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Ismerje meg, hogyan [használhatók a Proximity csoportok az SAP-alkalmazásokkal](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).