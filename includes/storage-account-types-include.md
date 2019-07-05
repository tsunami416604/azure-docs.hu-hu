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
ms.openlocfilehash: 14de5283197778464993b80b312a690662e066ab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443745"
---
Az Azure Storage számos különböző típusú tárfiókok kínál. Egyes támogatja a különböző funkciókat, és rendelkezik a saját díjszabási modell. Milyen típusú fiókot, amely a legjobb alkalmazásait a tárfiók létrehozása előtt, fontolja meg a különbségeket. A tárfiókok típusai a következők:

- **Általános célú v2 fiókok**: Alapszintű storage fióktípus esetében a blobok, fájlok, üzenetsorok és táblák. A legtöbb célra az Azure Storage ajánlott.
- **Általános célú v1 fiókok**: Blobok, fájlok, üzenetsorok és táblák örökölt fiók típusa. Helyette használja általános célú v2-fiókok, amikor csak lehetséges.
- **Blob storage-fiókok blokkolása**: A prémium szintű teljesítményt nyújt csak BLOB storage-fiókok. Javasolt a magas tranzakciók díjait számítjuk fel kisebb objektumok használatával, vagy konzisztens módon alacsony tárolási késés igénylő forgatókönyvek kialakítását.
- **Storage-fiókok FileStorage**: A prémium szintű teljesítményt nyújt csak fájlokat a storage-fiókok. Javasolt a vállalati vagy nagy teljesítményű alkalmazásokat.
- **BLOB storage-fiókok**: Csak a BLOB storage-fiókok. Helyette használja általános célú v2-fiókok, amikor csak lehetséges.

A következő táblázat a tárfiókok típusait és azok képességeinek:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszintek      | Támogatott elérési szint         | Replikációs beállítások               | Üzemi modell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encryption<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Általános célú V2   | BLOB, fájl, várólista, tábla és lemez       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gyakori, ritka elérésű, archív tárolási szint<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Titkosított              |
| Általános célú V1   | BLOB, fájl, várólista, tábla és lemez       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | –                            | LRS, GRS, RA-GRS                  | Resource Manager, klasszikus    | Titkosított              |
| Blokkblob-tárolás   | BLOB (blokkblobok és hozzáfűző blobok csak) | Prémium                       | –                            | LRS                               | Resource Manager             | Titkosított              |
| FileStorage   | Csak a fájlok | Prémium                       | –                            | LRS                               | Resource Manager             | Titkosított              |
| Blob Storage         | BLOB (blokkblobok és hozzáfűző blobok csak) | Standard                      | Gyakori, ritka elérésű, archív tárolási szint<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Titkosított              |

<div id="deployment-model"><sup>1</sup>ajánlott az Azure Resource Manager üzemi modell használatával. Storage-fiókok a klasszikus üzemi modell használatával továbbra is hozhatja létre az egyes helyeken, és a létező klasszikus fiók továbbra is támogatottak. További információkért lásd: <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager és klasszikus üzembe helyezési: Üzembe helyezési modellek és az erőforrások állapotának ismertetése</a>.</div>

<div id="encryption"><sup>2</sup>összes storage-fiók használatával történő Storage Service Encryption (SSE) inaktív adatok titkosítását. További információkért lásd: <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption az inaktív adatok</a>.</div>

<div id="archive"><sup>3</sup>az archív szinten érhető el, csak egy egyedi BLOB szintjén nem a storage-fiók szintjén. Csak blokkblobok és hozzáfűző blobok archiválhatók. További információkért lásd: <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob storage: A gyakran és ritkán használt adatok, és az archív tárolási szintek</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup>zónaredundáns tárolás (ZRS) csak a standard szintű, általános célú v2-tárfiókok esetén érhető el. A ZRS kapcsolatos további információkért lásd: <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások</a>. Más replikációs beállításokkal kapcsolatos további információkért lásd: <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage replikáció</a>.</div>

<div id="premium-performance"><sup>5</sup>prémium szintű teljesítményt, általános célú v2 és általános célú v1-fiókok csak a lemez- és BLOB érhető el.</div>
