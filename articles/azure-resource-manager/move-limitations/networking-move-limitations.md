---
title: Azure hálózati erőforrások áthelyezése új előfizetésre vagy erőforráscsoporthoz
description: A Azure Resource Manager használatával áthelyezheti a virtuális hálózatokat és egyéb hálózati erőforrásokat egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: c4e6acb52f6342c57fb1db9fc3e83d90d6d01285
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150823"
---
# <a name="move-guidance-for-networking-resources"></a>Útmutató a hálózati erőforrások áthelyezéséhez

Ez a cikk bemutatja, hogyan helyezhetők át virtuális hálózatok és egyéb hálózati erőforrások adott forgatókönyvekhez.

## <a name="dependent-resources"></a>Függő erőforrások

Virtuális hálózat áthelyezésekor is át kell helyeznie a tőle függő erőforrások. A VPN-átjárók IP-címek, virtuális hálózati átjárók és minden kapcsolódó kapcsolati erőforrás kell áthelyezni. Helyi hálózati átjárók egy másik erőforráscsoportban található is lehet.

Ha egy virtuális gépet egy hálózati kártyával szeretne áthelyezni egy új előfizetésbe, az összes függő erőforrást át kell helyeznie. Helyezze át a virtuális hálózatot a hálózati adapterhez, a virtuális hálózathoz tartozó összes többi hálózati adaptert és a VPN-átjárókat.

További információ: [forgatókönyv az előfizetések közötti áthelyezéshez](../resource-group-move-resources.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Egyenrangú virtuális hálózat

Egy virtuális Társhálózat áthelyezni, előbb le kell tiltania a virtuális hálózatok közötti társviszony. Ha le van tiltva, áthelyezheti a virtuális hálózat. Az áthelyezés után a virtuális hálózatok közötti társviszony újraengedélyezni.

## <a name="subnet-links"></a>Alhálózati kapcsolatok

Virtuális hálózat nem helyezhetők át másik előfizetésbe való, ha a virtuális hálózati erőforrás-navigációs hivatkozásaival egy alhálózatot tartalmaz. Például ha az Azure Cache Redis erőforrás egy alhálózatában van üzembe helyezve, az alhálózatra, erőforrás-navigációs hivatkozást.

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../resource-group-move-resources.md).
