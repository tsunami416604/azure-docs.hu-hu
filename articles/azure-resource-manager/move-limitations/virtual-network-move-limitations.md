---
title: Azure-beli virtuális hálózatok erőforrások áthelyezése új előfizetést vagy az erőforrás-csoport |} A Microsoft Docs
description: Azure Resource Manager használatával a virtuális hálózatok áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723492"
---
# <a name="move-guidance-for-virtual-networks"></a>Útmutató a virtuális hálózatok áthelyezése

Ez a cikk ismerteti, hogyan kívánja áthelyezni a virtuális hálózatok bizonyos forgatókönyvek esetén.

## <a name="dependent-resources"></a>Függő erőforrások

Virtuális hálózat áthelyezésekor is át kell helyeznie a tőle függő erőforrások. A VPN-átjárók IP-címek, virtuális hálózati átjárók és minden kapcsolódó kapcsolati erőforrás kell áthelyezni. Helyi hálózati átjárók egy másik erőforráscsoportban található is lehet.

Egy hálózati kártyát a virtuális gép áthelyezése, át kell helyeznie az összes függő erőforrást. Helyezze át a hálózati kártyát a virtuális hálózat, minden más hálózati kártyák a virtuális hálózat és a VPN Gateway átjárókkal.

## <a name="peered-virtual-network"></a>Virtuális Társhálózat

Egy virtuális Társhálózat áthelyezni, előbb le kell tiltania a virtuális hálózatok közötti társviszony. Ha le van tiltva, áthelyezheti a virtuális hálózat. Az áthelyezés után a virtuális hálózatok közötti társviszony újraengedélyezni.

## <a name="subnet-links"></a>Alhálózati hivatkozások

Virtuális hálózat nem helyezhetők át másik előfizetésbe való, ha a virtuális hálózati erőforrás-navigációs hivatkozásaival egy alhálózatot tartalmaz. Például ha az Azure Cache Redis erőforrás egy alhálózatában van üzembe helyezve, az alhálózatra, erőforrás-navigációs hivatkozást.

## <a name="next-steps"></a>További lépések

Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../resource-group-move-resources.md).
