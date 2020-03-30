---
title: Az Azure NetApp-fájlok szolgáltatási szintjei | Microsoft dokumentumok
description: Az Azure NetApp-fájlok szolgáltatási szintjeinek átviteli teljesítményének ismertetése.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832583"
---
# <a name="service-levels-for-azure-netapp-files"></a>Az Azure NetApp Files szolgáltatásszintjei
A szolgáltatásszintek egy kapacitáskészlet attribútumai. A szolgáltatásszinteket a kapacitáskészletben lévő kötet ek számára engedélyezett maximális átviteli teljesítmény határozza meg és különbözteti meg a kötethez rendelt kvóta alapján.

## <a name="supported-service-levels"></a>Támogatott szolgáltatási szintek

Az Azure NetApp Files három szolgáltatási szintet támogat: *Ultra,* *Premium*és *Standard*. 

* <a name="Ultra"></a>Ultra tároló

    Az Ultra tárolási szint legfeljebb 128 MiB/s átviteli kapacitást biztosít 1 TiB hozzárendelt kötetkvótánként. 

* <a name="Premium"></a>Prémium szintű tárolás

    A prémium szintű tárolási szint legfeljebb 64 MiB/s átviteli kapacitást biztosít 1 TiB hozzárendelt kötetkvótánként. 

* <a name="Standard"></a>Standard szintű Storage

    A standard tárolási szint legfeljebb 16 MiB/s átviteli kapacitást biztosít 1 TiB hozzárendelt kötetkvótánként.

## <a name="throughput-limits"></a>Átviteli korlátok

A kötet átviteli korlátját a következő tényezők kombinációja határozza meg:
* Annak a kapacitáskészletnek a szolgáltatási szintje, amelyhez a kötet tartozik
* A kötethez rendelt kvóta  

Ezt a fogalmat az alábbi ábra szemlélteti:

![Szolgáltatási szint illusztrációja](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

A fenti 1. Ez a forgatókönyv a kapacitáskészlet méretétől vagy a tényleges kötetfelhasználástól függetlenül érvényes.

A fenti 2. Ez a forgatókönyv a kapacitáskészlet méretétől vagy a tényleges kötetfelhasználástól függetlenül érvényes.

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure NetApp Files díjszabási lapját](https://azure.microsoft.com/pricing/details/storage/netapp/) a különböző szolgáltatási szintek áráról
- Lásd: [Az Azure NetApp Files költségmodellje](azure-netapp-files-cost-model.md) a kapacitáskészlet kapacitásfelhasználásának kiszámításához 
- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
- Lásd [az Azure NetApp-fájlok szolgáltatásiszint-szerződését (SLA)](https://azure.microsoft.com/support/legal/sla/netapp/)