---
title: fájl belefoglalása
description: fájl belefoglalása
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272942"
---
Egy előfizetésen belül több szolgáltatást is létrehozhat. Mindegyik kiépíthető egy adott szinten. Csak az egyes szinteken engedélyezett szolgáltatások száma korlátozza. Létrehozhat például 12 szolgáltatást az alapszinten, és további 12 szolgáltatást az S1 szinten ugyanazon az előfizetésen belül. A szintekről további információt a [Termékváltozat vagy az Azure Cognitive Search csomag kiválasztása](../articles/search/search-sku-tier.md)című témakörben talál.

Kérésre maximális szolgáltatási limitek emelhetők. Ha több szolgáltatásra van szüksége ugyanazon az előfizetésen belül, forduljon az Azure-támogatáshoz.

| Erőforrás            | Ingyenes<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | 1. | 2. |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximális szolgáltatások    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximális skála a keresési egységekben (SU)<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Ingyenes alapul megosztott, nem dedikált, források. A megosztott erőforrások nem támogatják a felskálázást.

<sup>2</sup> A keresési egységek olyan számlázási egységek, *amelyekrepénként* vagy *partícióként*vannak lefoglalva. A tárolási, indexelési és lekérdezési műveletekhez erőforrásokra is szükség van. Az SU-számításokról a [Lekérdezési és indexelési számítási feladatok erőforrásszintek méretezése](../articles/search/search-capacity-planning.md).. 