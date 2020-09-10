---
title: Eszköz kiválasztása az Azure-erőforrások régiók közötti áthelyezéséhez
description: Az Azure-erőforrások régiók közötti áthelyezésére szolgáló beállítások és eszközök áttekintése
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 36f4bb0c10e8346caa0c4ef8fd1ed69e4b2878a0
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669374"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Eszköz kiválasztása az Azure-erőforrások áthelyezéséhez

Az Azure-ban a következőképpen helyezhet el erőforrásokat:

- **Erőforrások áthelyezése a régiók között**: az erőforrás áthelyezése az erőforrás-mozgató hubhoz vagy egy erőforráscsoporthoz. 
- **Erőforrások áthelyezése erőforráscsoport/előfizetések között**: áthelyezés egy erőforráscsoporthoz. 
- **Erőforrások áthelyezése az Azure-felhők között**: a Azure site Recovery szolgáltatás használatával áthelyezheti az erőforrásokat a nyilvános és a kormányzati felhők között.
- **Erőforrások áthelyezése a rendelkezésre állási zónák között ugyanabban a régióban**: a Azure site Recovery szolgáltatás használatával helyezze át az erőforrásokat a rendelkezésre állási zónák között ugyanabban az Azure-régióban.


## <a name="compare-move-tools"></a>Áthelyezési eszközök összehasonlítása

**Eszköz** | **A következő esetekben használja** | **További információ**
--- | --- 
**Áthelyezés az erőforráscsoport között** | Erőforrásokat helyezhet át egy másik erőforráscsoporthoz vagy előfizetésbe, vagy a régiók között.<br/><br/> Ha áthelyezi a régiókat, az erőforráscsoport kiválasztja az áthelyezni kívánt erőforrásokat, majd áthelyezi az erőforrás-mozgató központba, hogy ellenőrizze a függőségeket, és helyezze át az erőforrásokat a célként megadott régióba. | [Erőforrások áthelyezése másik erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Erőforrások áthelyezése egy másik régióba egy erőforráscsoport alapján](../azure-resource-manager/management/move-region.md).
**Áthelyezés az erőforrás-mozgató hubhoz** | Erőforrások áthelyezése régiók között. <br/><br/> Áthelyezheti a célhelyre, vagy egy adott rendelkezésre állási zónába vagy rendelkezésre állási csoportba a célként megadott régión belül. | [Erőforrások áthelyezése a régiók között az erőforrás-mozgató központban]().
**Virtuális gépek áthelyezése Site Recovery** | Az Azure-beli virtuális gépek kormányzati és nyilvános felhők közötti áthelyezésére használható.<br/><br/> Akkor használja, ha a virtuális gépeket ugyanabban a régióban lévő rendelkezésre állási zónák között szeretné áthelyezni. |[Erőforrások áthelyezése a kormányzati/nyilvános felhők között](../site-recovery/region-move-cross-geos.md), [az erőforrások áthelyezése a rendelkezésre állási zónákba ugyanabban a régióban](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>További lépések

[További](about-move-process.md) információ az erőforrás-mozgató összetevőkről és az áthelyezési folyamatról.
