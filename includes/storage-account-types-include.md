---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/02/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b278cce8f885f31f9d59fd389261812e04e58405
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54066990"
---
Az Azure Storage három típusú tárfiókok kínál. Egyes támogatja a különböző funkciókat, és rendelkezik a saját díjszabási modell. Milyen típusú fiókot, amely a legjobb alkalmazásait a tárfiók létrehozása előtt, fontolja meg a különbségeket. A storage-fiókok három típusok a következők:

* **Általános célú v2 fiókok**: Alapszintű storage fióktípus esetében a blobok, fájlok, üzenetsorok és táblák. A legtöbb célra az Azure Storage ajánlott.
* **Általános célú v1 fiókok**: Blobok, fájlok, üzenetsorok és táblák örökölt fiók típusa. Helyette használja általános célú v2-fiókok, amikor csak lehetséges.
* **BLOB storage-fiókok**: Csak a BLOB storage-fiókok. Helyette használja általános célú v2-fiókok, amikor csak lehetséges. 

A következő táblázat a tárfiókok típusait és azok képességeinek:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszintek | Támogatott elérési szint               | Replikációs beállítások                                                | Üzemi modell<sup>1</sup>  | Titkosítási<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Általános célú V2   | BLOB, fájl, várólista, tábla és lemez       | Standard, prémium szintű           | Gyakori és ritka elérésű, archív<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager | Titkosított  |
| Általános célú V1   | BLOB, fájl, várólista, tábla és lemez       | Standard, prémium szintű           | –                                  | LRS, GRS, RA-GRS                                                   | Resource Manager, klasszikus  | Titkosított  |
| Blob Storage         | BLOB (blokkblobok és hozzáfűző blobok csak) | Standard                    | Gyakori és ritka elérésű, archív<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Resource Manager  | Titkosított  |

<sup>1</sup>ajánlott az Azure Resource Manager üzemi modell használatával. Storage-fiókok a klasszikus üzemi modell használatával továbbra is hozhatja létre az egyes helyeken, és a létező klasszikus fiók továbbra is támogatottak. További információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési: Üzembe helyezési modellek és az erőforrások állapotának ismertetése](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>összes storage-fiók használatával történő Storage Service Encryption (SSE) inaktív adatok titkosítását. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>az archív szinten érhető el, csak egy egyedi BLOB szintjén nem a storage-fiók szintjén. Csak blokkblobok és hozzáfűző blobok archiválhatók. További információkért lásd: [Azure Blob storage: A gyakran és ritkán használt adatok, és az archív tárolási szintek](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zónaredundáns tárolás (ZRS) csak a standard szintű, általános célú v2-tárfiókok esetén érhető el. A ZRS kapcsolatos további információkért lásd: [zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások](../articles/storage/common/storage-redundancy-zrs.md). Más replikációs beállításokkal kapcsolatos további információkért lásd: [Azure Storage replikáció](../articles/storage/common/storage-redundancy.md).
