---
title: fájl belefoglalása
description: fájl belefoglalása
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160516"
---
Létrehozhat egy előfizetésen belül több szolgáltatást is. Egyenként is üzembe helyezve egy konkrét csomagot kiválasztani. Minden egyes rétegben engedélyezett szolgáltatások számának csak korlátot jelent. Például létrehozhat legfeljebb 12 szolgáltatások, az alapszintű csomag és az S1 szintet ugyanazon az előfizetésen belül egy másik 12 szolgáltatásokhoz. Rétegek kapcsolatos további információkért lásd: [-Termékváltozat vagy termékváltozat-csomag kiválasztása az Azure Search](../articles/search/search-sku-tier.md).

Maximális Szolgáltatáskorlátok kérésre lehet megemelni. Ha ugyanazon az előfizetésen belül több szolgáltatás van szüksége, forduljon az Azure ügyfélszolgálatához.

| Resource            | Ingyenes<sup>1</sup> | Alapszintű | S1  | S2 | S3 | S3&nbsp;HD | L1 | 2\. |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximális szolgáltatások    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximális méret esetén keresési egységek (SU)<sup>2</sup> |– |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> ingyenes megosztott, nem dedikált erőforrások alapján. Vertikális felskálázás nem támogatott a megosztott erőforrások.

<sup>2</sup> keresési egységek vannak számlázási egység, mert lefoglalt egy *replika* vagy egy *partíció*. Mindkét erőforrás storage indexelése és lekérdezési műveleteket van szükség. SU számítások kapcsolatos további információkért lásd: [erőforrásszintek méretezése a lekérdezési és indexelési számítási feladatok](../articles/search/search-capacity-planning.md). 