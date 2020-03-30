---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371552"
---
Az Azure Storage többféle tárfiókot kínál. Minden típus támogatja a különböző funkciókat, és saját díjszabási modellel rendelkezik. Vegye figyelembe ezeket a különbségeket, mielőtt létrehozna egy tárfiókot, hogy meghatározza, hogy milyen típusú fiók a legmegfelelőbb az alkalmazások számára. A tárfiókok típusai a következők:

- **Általános célú v2-fiókok:** Egyszerű tárfiók típusa blobok, fájlok, várólisták és táblák. Az Azure Storage legtöbb forgatókönyvéhez ajánlott.
- **Általános célú v1-fiókok:** Örökölt fióktípusa blobok, fájlok, várólisták és táblák. Ha lehetséges, használjon általános célú v2-fiókokat.
- **BlockBlobStorage-fiókok:** A blokkblobok és hozzáfűző blobok prémium szintű teljesítményjellemzőivel rendelkező tárfiókok. Magas tranzakciós rátával rendelkező esetekhez ajánlott, vagy olyan esetekben, amelyek kisebb objektumokat használnak, vagy konzisztensen alacsony tárolási késést igényelnek.
- **FileStorage-fiókok:** Csak fájlokat tároló fiókok prémium szintű teljesítményjellemzőkkel. Nagyvállalati vagy nagy teljesítményű alkalmazásokhoz ajánlott.
- **BlobStorage-fiókok:** Csak örökölt Blob-tárfiókok. Ha lehetséges, használjon általános célú v2-fiókokat.

Az alábbi táblázat a tárfiókok típusait és azok képességeit ismerteti:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszintek      | Támogatott hozzáférési szintek         | Replikációs lehetőségek               | Üzemi modell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Titkosítás<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Általános célú V2   | Blob, Fájl, Várólista, Tábla, Lemez és Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Forró, Hűvös, Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (előzetes), RA-GZRS (előzetes verzió)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Titkosított              |
| Általános célú V1   | Blob, Fájl, Várólista, Tábla és Lemez       | Standard, Prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS, GRS, RA-GRS                  | Erőforrás-kezelő, Klasszikus    | Titkosított              |
| BlockBlobStorage tároló   | Blob (csak blokkblobok és hozzáfűző blobok esetén) | Prémium                       | N/A                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Titkosított              |
| FileStorage (Fájltárolás)   | Csak fájl | Prémium                       | N/A                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Titkosított              |
| BlobStorage tároló         | Blob (csak blokkblobok és hozzáfűző blobok esetén) | Standard                      | Forró, Hűvös, Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Titkosított              |

<div id="deployment-model"><sup>1 1</sup> Az Azure Resource Manager központi telepítési modell használata ajánlott. A klasszikus üzembe helyezési modellt használó tárfiókok továbbra is létrehozhatók bizonyos helyeken, és a meglévő klasszikus fiókok továbbra is támogatottak. További információ: <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. klasszikus üzembe helyezés: A központi telepítési modellek és az erőforrások állapotának megismerése</a>című témakörben talál.</div><br/>

<div id="encryption"><sup>2.</sup> Minden tárfiók titkosítva van a storage service encryption (SSE) használatával az inaktív adatokhoz. További információ: <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption for Data at In.</a></div><br/>

<div id="archive"><sup>3</sup> Archív tárolás és blob szintű rétegezés csak támogatja a blokk blobok. Az archív szint csak egy adott blob szintjén érhető el, a tárfiók szintjén nem. További információ: <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob storage: Hot, Cool, and Archive storage tiers.</a></div><br/>

<div id="zone-redundant-storage"><sup>4.</sup> Zónaredundáns tárolás (ZRS) és a geozóna redundáns tárolás (GZRS/RA-GZRS) (előzetes verzió) csak a szabványos általános célú V2, BlockBlobStorage és FileStorage fiókok bizonyos régiókban érhetők el. Az Azure Storage redundanciabeállításairól az <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage redundanciája</a>című témakörben talál további információt.</div><br/>

<div id="premium-performance"><sup>5.</sup> Az általános célú v2-es és általános célú v1-fiókok prémium szintű teljesítménye csak lemez- és lapblobesetén érhető el. A blokk- vagy hozzáfűző blobok prémium szintű teljesítménye csak a BlockBlobStorage-fiókokban érhető el. A fájlok prémium teljesítménye csak a FileStorage-fiókokban érhető el.</div><br/>

<div id="data-lake-gen2"><sup>6.</sup> Az Azure Data Lake Storage Gen2 az Azure Blob storage-ra épülő big data-elemzési képességek összeskészlete. A Data Lake Storage Gen2 csak általános célú V2-tárfiókok esetén támogatott, hierarchikus névtér engedélyezve. A Data Lake Storage Gen2 szolgáltatásról további információt az <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2 – Bevezetés</a>című témakörben talál.</div>
