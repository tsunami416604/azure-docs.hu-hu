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
ms.openlocfilehash: a466ea29fa31c4c628724f3d5138a1612ef7a0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553556"
---
Létrehozhat egy előfizetésen belül több szolgáltatást is. Egyenként is üzembe helyezve egy konkrét csomagot kiválasztani. Minden egyes rétegben engedélyezett szolgáltatások számának csak korlátot jelent. Például létrehozhat legfeljebb 12 szolgáltatások, az alapszintű csomag és az S1 szintet ugyanazon az előfizetésen belül egy másik 12 szolgáltatásokhoz. Rétegek kapcsolatos további információkért lásd: [-Termékváltozat vagy termékváltozat-csomag kiválasztása az Azure Search](../articles/search/search-sku-tier.md).

Maximális Szolgáltatáskorlátok kérésre lehet megemelni. Ha ugyanazon az előfizetésen belül több szolgáltatás van szüksége, forduljon az Azure ügyfélszolgálatához.

| Erőforrás            | Ingyenes<sup>1</sup> | Alapszintű | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximális szolgáltatások    |1     | 12    | 12  | 6  | 6  | 6     |
| Maximális méret esetén keresési egységek (SU)<sup>2</sup> |– |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup>ingyenes megosztott, nem dedikált erőforrások alapján. Vertikális felskálázás nem támogatott a megosztott erőforrások.

<sup>2</sup>keresési egységek vannak számlázási egység, mert lefoglalt egy *replika* vagy egy *partíció*. Mindkét erőforrás storage indexelése és lekérdezési műveleteket van szükség. SU számítások kapcsolatos további információkért lásd: [erőforrásszintek méretezése a lekérdezési és indexelési számítási feladatok](../articles/search/search-capacity-planning.md). 