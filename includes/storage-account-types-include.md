---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d4f57eca89cbb68d61546c6d5ce5bcd04f9256e7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59286371"
---
Az Azure Storage számos különböző típusú tárfiókok kínál. Egyes támogatja a különböző funkciókat, és rendelkezik a saját díjszabási modell. Milyen típusú fiókot, amely a legjobb alkalmazásait a tárfiók létrehozása előtt, fontolja meg a különbségeket. A tárfiókok típusai a következők:

- **Általános célú v2 fiókok**: Alapszintű storage fióktípus esetében a blobok, fájlok, üzenetsorok és táblák. A legtöbb célra az Azure Storage ajánlott.
- **Általános célú v1 fiókok**: Blobok, fájlok, üzenetsorok és táblák örökölt fiók típusa. Helyette használja általános célú v2-fiókok, amikor csak lehetséges.
- **Blob storage-fiókok blokkolása**: A prémium szintű teljesítményt nyújt csak BLOB storage-fiókok. Javasolt a magas tranzakciók díjait számítjuk fel kisebb objektumok használatával, vagy konzisztens módon alacsony tárolási késés igénylő forgatókönyvek kialakítását.
- **Storage-fiókok (előzetes verzió) FileStorage**: A prémium szintű teljesítményt nyújt csak fájlokat a storage-fiókok. Javasolt a vállalati vagy nagy teljesítményű alkalmazásokat.
- **BLOB storage-fiókok**: Csak a BLOB storage-fiókok. Helyette használja általános célú v2-fiókok, amikor csak lehetséges.

A következő táblázat a tárfiókok típusait és azok képességeinek:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszintek      | Támogatott elérési szint         | Replikációs beállítások               | Üzemi modell<sup>1</sup> | Encryption<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Általános célú V2   | BLOB, fájl, várólista, tábla és lemez       | Standard, Premium<sup>5</sup> | Gyakori és ritka elérésű, archív<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager             | Titkosított              |
| Általános célú V1   | BLOB, fájl, várólista, tábla és lemez       | Standard, Premium<sup>5</sup> | –                            | LRS, GRS, RA-GRS                  | Resource Manager, klasszikus    | Titkosított              |
| Blokkblob-tárolás   | BLOB (blokkblobok és hozzáfűző blobok csak) | Prémium                       | –                            | LRS                               | Resource Manager             | Titkosított              |
| FileStorage (előzetes verzió)   | Csak a fájlok | Prémium                       | –                            | LRS                               | Resource Manager             | Titkosított              |
| Blob Storage         | BLOB (blokkblobok és hozzáfűző blobok csak) | Standard                      | Gyakori és ritka elérésű, archív<sup>3</sup> | LRS, GRS, RA-GRS                  | Resource Manager             | Titkosított              |

<sup>1</sup>ajánlott az Azure Resource Manager üzemi modell használatával. Storage-fiókok a klasszikus üzemi modell használatával továbbra is hozhatja létre az egyes helyeken, és a létező klasszikus fiók továbbra is támogatottak. További információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési: Üzembe helyezési modellek és az erőforrások állapotának ismertetése](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>összes storage-fiók használatával történő Storage Service Encryption (SSE) inaktív adatok titkosítását. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>az archív szinten érhető el, csak egy egyedi BLOB szintjén nem a storage-fiók szintjén. Csak blokkblobok és hozzáfűző blobok archiválhatók. További információkért lásd: [Azure Blob storage: A gyakran és ritkán használt adatok, és az archív tárolási szintek](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zónaredundáns tárolás (ZRS) csak a standard szintű, általános célú v2-tárfiókok esetén érhető el. A ZRS kapcsolatos további információkért lásd: [zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások](../articles/storage/common/storage-redundancy-zrs.md). Más replikációs beállításokkal kapcsolatos további információkért lásd: [Azure Storage replikáció](../articles/storage/common/storage-redundancy.md).

<sup>5</sup> prémium szintű teljesítményt, általános célú v2 és általános célú v1-fiókok csak a lemez- és BLOB érhető el.