---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123075"
---
Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy meghatározott hardvertípusban vannak elkülönítve, és egyetlen ügyfél számára vannak fenntartva.  Ezek a virtuálisgép-méretek olyan számítási feladatokhoz megfelelőek, amelyeket magas szintű izolációval szükséges elkülöníteni más ügyfelek számítási feladataitól, beleértve olyan elemeket is mint a megfelelőség vagy a jogszabályi előírások betartása.  Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure-támogatásának](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait.

Az elkülönített méret kihasználása garantálja, hogy a virtuális gép az adott kiszolgálópéldány esetében csak egy fut.  A jelenlegi elkülönített virtuálisgép-ajánlatok a következők:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard m128ms
* Standard_GS5
* Standard G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Az összes rendelkezésre álló elszigetelt méretről [itt](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)olvashat bővebben.