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
ms.openlocfilehash: 0da7ad35f6efc031a52ef43caa514559c08c94fe
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632767"
---
Létrehozhat egy előfizetésen belül több szolgáltatást is. Egyenként is üzembe helyezve egy konkrét csomagot kiválasztani. Minden egyes rétegben engedélyezett szolgáltatások számának csak korlátot jelent. Például létrehozhat legfeljebb 12 szolgáltatások, az alapszintű csomag és az S1 szintet ugyanazon az előfizetésen belül egy másik 12 szolgáltatásokhoz. Rétegek kapcsolatos további információkért lásd: [-Termékváltozat vagy termékváltozat-csomag kiválasztása az Azure Search](../articles/search/search-sku-tier.md).

Maximális Szolgáltatáskorlátok kérésre lehet megemelni. Ha ugyanazon az előfizetésen belül több szolgáltatás van szüksége, forduljon az Azure ügyfélszolgálatához.

| Erőforrás            | Ingyenes<sup>1</sup> | Alapszintű | S1  | S2 | S3 | S3&nbsp;HD | L1 | 2. |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximális szolgáltatások    |1     | 12    | 12  | 6  | 6  | 6     | 6  | 6  |
| Maximális méret esetén keresési egységek (SU)<sup>2</sup> |– |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> ingyenes megosztott, nem dedikált erőforrások alapján. Vertikális felskálázás nem támogatott a megosztott erőforrások.

<sup>2</sup> keresési egységek vannak számlázási egység, mert lefoglalt egy *replika* vagy egy *partíció*. Mindkét erőforrás storage indexelése és lekérdezési műveleteket van szükség. SU számítások kapcsolatos további információkért lásd: [erőforrásszintek méretezése a lekérdezési és indexelési számítási feladatok](../articles/search/search-capacity-planning.md). 