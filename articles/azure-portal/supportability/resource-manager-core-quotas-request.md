---
title: Az Azure Resource Manager vCPU-kvótanövelése késedelmi kérelmek
description: Az Azure Resource Manager vCPU-kvótanövelése késedelmi kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843667"
---
# <a name="quota-increase-requests"></a>Kvótanövelési kérések

A virtuális gépek és a virtuálisgép-méretezési csoportok Erőforrás-kezelő vCPU-kvótái minden egyes előfizetéshez két szinten vannak kényszerítve.

Az első szint a teljes regionális vCPU-k korlátját az összes virtuálisgép-sorozatban. A második szint a virtuális gépsorozatvCPU-k korlát, például a D-sorozat vCPU-k. Amikor egy új virtuális gép üzembe helyezése, az összeg az új és a meglévő vCPU-k használata az adott virtuálisgép-sorozat nem haladhatja meg az adott virtuálisgép-sorozat jóváhagyott vCPU-kvótát. Továbbá az összes virtuálisgép-sorozatban üzembe helyezett új és meglévő vCPU-szám nem haladhatja meg az előfizetéshez jóváhagyott összes regionális vCPU-kvótát. Ha ezek közül bármelyik a kvóták túllépése, a virtuális gép központi telepítése nem engedélyezett.
Kérheti a vCPU-k kvótakorlátjának növelését az Azure Portalról a virtuális gépsorozathoz. A virtuálisgép-sorozat kvóta növekedése automatikusan növeli a teljes regionális vCPU-k korlátját ugyanazzal az összeggel.

Új előfizetés létrehozásakor előfordulhat, hogy az alapértelmezett összes regionális vCPU nem egyezik meg az összes virtuális gép sorozat alapértelmezett vCPU-kvótáinak összegével. Ez a tény azt eredményezheti, hogy egy előfizetés elegendő kvótát minden egyes virtuálisgép-sorozat, amely üzembe kívánja helyezni. Lehet, hogy nem rendelkezik elegendő kvótával a teljes regionális vCPU-k hoz minden üzembe helyezéshez. Ebben az esetben a teljes regionális vCPU-k kifejezett növelésére vonatkozó kérelmet kell benyújtania. Az összes regionális vCPU-korlát nem haladhatja meg a jóváhagyott kvóta összegét a régió összes virtuálisgép-sorozatában.

A kvótákról további információt a [Virtuálisgép vCPU-kvóták](../../virtual-machines/windows/quotas.md) és [az Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.

