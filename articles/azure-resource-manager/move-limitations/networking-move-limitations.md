---
title: Azure hálózati erőforrások áthelyezése új előfizetésre vagy erőforráscsoporthoz | Microsoft Docs
description: A Azure Resource Manager használatával áthelyezheti a virtuális hálózatokat és egyéb hálózati erőforrásokat egy új erőforráscsoporthoz vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 6ad7f32704d8cb73999a6e3cf60cb2a238268242
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034549"
---
# <a name="move-guidance-for-networking-resources"></a>Útmutató a hálózati erőforrások áthelyezéséhez

Ez a cikk bemutatja, hogyan helyezhetők át virtuális hálózatok és egyéb hálózati erőforrások adott forgatókönyvekhez.

## <a name="dependent-resources"></a>Függő erőforrások

Virtuális hálózat áthelyezésekor is át kell helyeznie a tőle függő erőforrások. A VPN-átjárók IP-címek, virtuális hálózati átjárók és minden kapcsolódó kapcsolati erőforrás kell áthelyezni. Helyi hálózati átjárók egy másik erőforráscsoportban található is lehet.

Egy virtuális gép hálózati kártyával való áthelyezéséhez át kell helyeznie az összes függő erőforrást. Helyezze át a virtuális hálózatot a hálózati adapterhez, a virtuális hálózathoz tartozó összes többi hálózati adaptert és a VPN-átjárókat.

## <a name="peered-virtual-network"></a>Egyenrangú virtuális hálózat

Egy virtuális Társhálózat áthelyezni, előbb le kell tiltania a virtuális hálózatok közötti társviszony. Ha le van tiltva, áthelyezheti a virtuális hálózat. Az áthelyezés után a virtuális hálózatok közötti társviszony újraengedélyezni.

## <a name="subnet-links"></a>Alhálózati kapcsolatok

Virtuális hálózat nem helyezhetők át másik előfizetésbe való, ha a virtuális hálózati erőforrás-navigációs hivatkozásaival egy alhálózatot tartalmaz. Például ha az Azure Cache Redis erőforrás egy alhálózatában van üzembe helyezve, az alhálózatra, erőforrás-navigációs hivatkozást.

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy](../resource-group-move-resources.md)előfizetésbe.
