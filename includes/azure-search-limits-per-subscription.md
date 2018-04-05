---
title: fájl belefoglalása
description: fájl belefoglalása
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
Előfizetés, üzembe helyezve egy konkrét csomagot kiválasztani, csak az egyes rétegek engedélyezett szolgáltatások száma korlátozza mindegyiknél belül több szolgáltatáshoz is létrehozhat. Például létrehozhat legfeljebb 12-szolgáltatásokat az alapszintű rétegben, és egy másik 12: a S1 rétegben egyazon előfizetésen belül. Rétegek kapcsolatos további információkért lásd: [válasszon egy SKU vagy a réteg az Azure Search](../articles/search/search-sku-tier.md).

Maximális szolgáltatásra vonatkozó korlátozások is generál, kérésre. Ha további szolgáltatásokat egyazon előfizetésen belül van szüksége, forduljon az Azure ügyfélszolgálatához.

| Erőforrás            | Szabad&nbsp;<sup>1</sup> | Alapszintű | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximális szolgáltatások    |1     | 12    | 12  | 6  | 6  | 6     |
| SU a maximális skálája&nbsp;<sup>2</sup> |– |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> szabad megosztott, nem dedikált erőforrások alapul. Megosztott erőforrások méretezett nem támogatott.

<sup>2</sup> keresési egységek (SU) vannak számlázást egységeket, mert lefoglalt egy *replika* vagy egy *partíció*. A tárolás, az indexelést és a lekérdezési műveletek mindkét erőforrás van szüksége. SU számítások kapcsolatos további információkért lásd: [lekérdezési és indexelési munkaterhelések szintjeinek erőforrás méretezése](../articles/search/search-capacity-planning.md). 