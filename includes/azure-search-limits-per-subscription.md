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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755894"
---
Egy előfizetésben, minden egyes üzembe helyezett egy konkrét csomagot kiválasztani, minden egyes rétegben engedélyezett szolgáltatások száma korlátozza, több szolgáltatást is létrehozhat. Például létrehozhat legfeljebb 12 szolgáltatások, az alapszintű csomag és az S1 szintet ugyanazon az előfizetésen belül egy másik 12 szolgáltatásokhoz. Rétegek kapcsolatos további információkért lásd: [Termékváltozat vagy szint kiválasztása az Azure Search](../articles/search/search-sku-tier.md).

Maximális Szolgáltatáskorlátok kérésre lehet megemelni. Ha ugyanazon az előfizetésen belül több szolgáltatás van szüksége, forduljon az Azure ügyfélszolgálatához.

| Erőforrás            | Ingyenes&nbsp;<sup>1</sup> | Alapszintű | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Maximális szolgáltatások    |1     | 12    | 12  | 6  | 6  | 6     |
| Maximális méret esetén SU&nbsp;<sup>2</sup> |– |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> ingyenes megosztott, nem dedikált erőforrások alapján. Vertikális felskálázás nem támogatott a megosztott erőforrások.

<sup>2</sup> keresési egységek (SU) vannak számlázási egység, mert lefoglalt egy *replika* vagy egy *partíció*. Mindkét erőforrás storage indexelése és lekérdezési műveleteket van szükség. SU számítások kapcsolatos további információkért lásd: [erőforrásszintek méretezése a lekérdezési és indexelési számítási feladatok](../articles/search/search-capacity-planning.md). 