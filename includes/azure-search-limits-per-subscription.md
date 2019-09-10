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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67179498"
---
Egy előfizetésen belül több szolgáltatást is létrehozhat. Mindegyiket egy adott rétegben lehet kiépíteni. Csak az egyes szintek által engedélyezett szolgáltatások száma korlátozza. Létrehozhat például akár 12 szolgáltatást az alapszintű szinten, és egy másik 12 szolgáltatást az S1 szinten ugyanazon előfizetés keretében. További információ a rétegekről: az [SKU vagy a Azure Search kiválasztása](../articles/search/search-sku-tier.md).

Kérés esetén a szolgáltatási korlátok maximális száma is megoldható. Ha ugyanahhoz az előfizetéshez több szolgáltatásra van szüksége, forduljon az Azure ügyfélszolgálatához.

| Resource            | Ingyenes<sup>1</sup> | Alapszintű | S1  | S2 | S3 | S3&nbsp;HD | L1 | 2\. |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Szolgáltatások maximális száma    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximális méretezés a keresési egységekben (SU)<sup>2</sup> |– |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> az ingyenes szolgáltatás megosztott, nem dedikált erőforrásokon alapul. A vertikális felskálázás nem támogatott a megosztott erőforrásokon.

<sup>2</sup> a keresési egységek olyan számlázási egységek, amelyek *replikaként* vagy *partícióként*vannak kiosztva. A tárolási, indexelési és lekérdezési műveletekhez mindkét erőforrásra szüksége van. A SU-számításokkal kapcsolatos további információkért lásd: [erőforrások szintjének skálázása lekérdezési és indexelési](../articles/search/search-capacity-planning.md)feladatokhoz. 