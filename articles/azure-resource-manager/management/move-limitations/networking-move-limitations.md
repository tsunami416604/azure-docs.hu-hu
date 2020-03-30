---
title: Az Azure Networking-erőforrások áthelyezése új előfizetésbe vagy erőforráscsoportba
description: Az Azure Resource Manager segítségével virtuális hálózatokat és más hálózati erőforrásokat helyezhet át egy új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485233"
---
# <a name="move-guidance-for-networking-resources"></a>Útmutató áthelyezése a hálózati erőforrásokhoz

Ez a cikk azt ismerteti, hogy miként helyezheti át a virtuális hálózatokat és más hálózati erőforrásokat adott esetekben.

## <a name="dependent-resources"></a>Függő erőforrások

Virtuális hálózat áthelyezésekénél a függő erőforrásokat is át kell helyeznie. Vpn-átjárók esetén át kell helyeznie az IP-címeket, a virtuális hálózati átjárókat és az összes kapcsolódó kapcsolati erőforrást. A helyi hálózati átjárók egy másik erőforráscsoportban lehetnek.

Ha egy hálózati kártyakártyával rendelkező virtuális gépet új előfizetésbe szeretne áthelyezni, át kell helyeznie az összes függő erőforrást. Helyezze át a hálózati kártya virtuális hálózatát, a virtuális hálózat összes többi hálózati kártya és a VPN-átjárók áthelyezését.

További információ: [Forgatókönyv az előfizetések közötti áthelyezéshez.](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions)

## <a name="peered-virtual-network"></a>Társviszony-létesített virtuális hálózat

Társviszony-létesített virtuális hálózat áthelyezéséhez először le kell tiltania a virtuális hálózati társviszony-létesítést. Ha le van tiltva, áthelyezheti a virtuális hálózatot. Az áthelyezés után engedélyezze újra a virtuális hálózati társviszony-létesítést.

## <a name="subnet-links"></a>Alhálózati hivatkozások

Nem helyezhet át virtuális hálózatot másik előfizetésbe, ha a virtuális hálózat erőforrás-navigációs hivatkozásokkal rendelkező alhálózatot tartalmaz. Ha például egy Azure-gyorsítótár redis erőforrás van telepítve egy alhálózat, az alhálózat rendelkezik egy erőforrás-navigációs hivatkozás.

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére vonatkozó parancsokról az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](../move-resource-group-and-subscription.md)témakörben található.
