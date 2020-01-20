---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6e8c0e1c7fef884844b8aaae9dc4c7e3eaa220a2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274553"
---
Az Azure Storage számos különböző típusú Storage-fiókot kínál. Mindegyik típus támogatja a különböző funkciókat, és saját díjszabási modellel rendelkezik. Vegye figyelembe ezeket a különbségeket, mielőtt létrehoz egy Storage-fiókot az alkalmazások számára legmegfelelőbb fiók típusának megállapításához. A Storage-fiókok típusai a következők:

- **Általános célú v2-fiókok**: a blobokhoz, fájlokhoz, várólistákhoz és táblákhoz tartozó alapszintű tárolási fióktípus. Az Azure Storage-t használó legtöbb forgatókönyv esetén ajánlott.
- **Általános célú v1-fiókok**: a Blobok, fájlok, várólisták és táblák örökölt fiókjának típusa. Ha lehetséges, használja az általános célú v2-fiókokat.
- **BlockBlobStorage-fiókok**: a blobok és a Blobok hozzáfűzésére szolgáló prémium szintű teljesítménnyel rendelkező Storage-fiókok. Ajánlott olyan forgatókönyvek esetén, amelyeknél magas a tranzakciós arány, vagy kisebb objektumokat használó forgatókönyvek, vagy ha konzisztensen alacsony tárolási késésre van szükség.
- **FileStorage fiókok**: csak a prémium szintű teljesítménnyel rendelkező Storage-fiókok. Nagyvállalati vagy nagy teljesítményű alkalmazások esetében ajánlott.
- **BlobStorage-fiókok**: örökölt blob Storage-fiókok. Ha lehetséges, használja az általános célú v2-fiókokat.

Az alábbi táblázat a Storage-fiókok és azok képességeinek típusait ismerteti:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszint      | Támogatott hozzáférési szintek         | Replikációs lehetőségek               | Üzemi modell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Titkosítás<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Általános célú v2   | BLOB, fájl, üzenetsor, tábla, lemez és Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gyors elérésű, ritka elérésű Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (előzetes verzió), RA-GZRS (előzetes verzió)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Erőforrás-kezelő             | Titkosított              |
| Általános célú v1   | BLOB, fájl, üzenetsor, tábla és lemez       | Standard, prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | –                            | LRS, GRS, RA-GRS                  | Resource Manager, klasszikus    | Titkosított              |
| BlockBlobStorage   | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | Prémium                       | –                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Erőforrás-kezelő             | Titkosított              |
| FileStorage   | Csak fájl | Prémium                       | –                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Erőforrás-kezelő             | Titkosított              |
| BlobStorage         | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | Standard                      | Gyors elérésű, ritka elérésű Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Erőforrás-kezelő             | Titkosított              |

<div id="deployment-model"><sup>1</sup> A Azure Resource Manager üzembe helyezési modell használata ajánlott. A klasszikus üzemi modellt használó Storage-fiókok bizonyos helyszíneken is létrehozhatók, és a meglévő klasszikus fiókok továbbra is támogatottak. További információkért lásd: <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager a klasszikus üzembe helyezéssel kapcsolatban: az üzembe helyezési modellek és az erőforrások állapotának ismertetése</a>.</div>

<div id="encryption"><sup>2</sup> Az összes Storage-fiók titkosítása Storage Service Encryption (SSE) használatával történik a nyugalmi állapotban lévő adatokhoz. További információ: <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service encryption for</a>inaktív adatok.</div>

<div id="archive"><sup>3</sup> Az archív szint csak egy egyedi blob szintjén érhető el, nem pedig a Storage-fiók szintjén. Csak a Blobok blokkolása és a hozzáfűző Blobok archiválása lehetséges. További információkért lásd <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">: Azure Blob Storage: gyakori, ritka elérésű és archív tárolási szintek</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> A Zone-redundáns tárolás (ZRS) és a Geo-Zone-redundáns tárolás (GZRS/RA-GZRS) (előzetes verzió) csak a standard általános célú v2-, BlockBlobStorage-és FileStorage-fiókok esetében érhető el bizonyos régiókban. További információ a ZRS <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">: Zone-redundáns tárolás (ZRS): a jól elérhető Azure Storage-alkalmazások</a>. További információ a GZRS/RA-GZRS: <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">geo-Zone-redundáns tárolás a nagyfokú rendelkezésre álláshoz és a maximális tartósság (előzetes verzió)</a>. További információ az egyéb replikációs lehetőségekről: az <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage replikációja</a>.</div>

<div id="premium-performance"><sup>5</sup> Az általános célú v2 és az általános célú v1-fiókok prémium szintű teljesítménye csak a lemezes és az oldal blobja esetében érhető el. A blokk-és hozzáfűző Blobok prémium szintű teljesítménye csak a BlockBlobStorage-fiókokon érhető el. A fájlok prémium szintű teljesítménye csak a FileStorage-fiókokon érhető el.</div>

<div id="data-lake-gen2"><sup>6</sup> A Azure Data Lake Storage Gen2 az Azure Blob Storage-ra épülő, big data Analytics szolgáltatáshoz dedikált képességek összessége. A Data Lake Storage Gen2 csak olyan általános célú v2-es Storage-fiókok esetében támogatott, amelyeken engedélyezve van a hierarchikus névtér. További információ a Data Lake Storage Gen2ről: a <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2 bemutatása</a>.</div>
