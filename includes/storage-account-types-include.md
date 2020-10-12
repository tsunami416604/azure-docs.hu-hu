---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "79371552"
---
Az Azure Storage számos különböző típusú Storage-fiókot kínál. Mindegyik típus támogatja a különböző funkciókat, és saját díjszabási modellel rendelkezik. Vegye figyelembe ezeket a különbségeket, mielőtt létrehoz egy Storage-fiókot az alkalmazások számára legmegfelelőbb fiók típusának megállapításához. A Storage-fiókok típusai a következők:

- **Általános célú v2-fiókok**: a blobokhoz, fájlokhoz, várólistákhoz és táblákhoz tartozó alapszintű tárolási fióktípus. Az Azure Storage-t használó legtöbb forgatókönyv esetén ajánlott.
- **Általános célú v1-fiókok**: a Blobok, fájlok, várólisták és táblák örökölt fiókjának típusa. Ha lehetséges, használja az általános célú v2-fiókokat.
- **BlockBlobStorage-fiókok**: a blobok és a Blobok hozzáfűzésére szolgáló prémium szintű teljesítménnyel rendelkező Storage-fiókok. Ajánlott olyan forgatókönyvek esetén, amelyeknél magas a tranzakciós arány, vagy kisebb objektumokat használó forgatókönyvek, vagy ha konzisztensen alacsony tárolási késésre van szükség.
- **FileStorage fiókok**: csak a prémium szintű teljesítménnyel rendelkező Storage-fiókok. Nagyvállalati vagy nagy teljesítményű alkalmazások esetében ajánlott.
- **BlobStorage-fiókok**: örökölt blob Storage-fiókok. Ha lehetséges, használja az általános célú v2-fiókokat.

Az alábbi táblázat a Storage-fiókok és azok képességeinek típusait ismerteti:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszint      | Támogatott hozzáférési szintek         | Replikációs lehetőségek               | Üzembehelyezési modell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Titkosítás<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Általános célú v2   | BLOB, fájl, üzenetsor, tábla, lemez és Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gyors elérésű, ritka elérésű Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (előzetes verzió), RA-GZRS (előzetes verzió)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Titkosított              |
| Általános célú v1   | BLOB, fájl, üzenetsor, tábla és lemez       | Standard, prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N.A.                            | LRS, GRS, RA-GRS                  | Resource Manager, klasszikus    | Titkosított              |
| BlockBlobStorage   | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | Prémium                       | N.A.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Titkosított              |
| FileStorage   | Csak fájl | Prémium                       | N.A.                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Titkosított              |
| BlobStorage         | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | Standard                      | Gyors elérésű, ritka elérésű Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Titkosított              |

<div id="deployment-model"><sup>1</sup> A Azure Resource Manager üzembe helyezési modell használata ajánlott. A klasszikus üzemi modellt használó Storage-fiókok bizonyos helyszíneken is létrehozhatók, és a meglévő klasszikus fiókok továbbra is támogatottak. További információkért lásd: <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager a klasszikus üzembe helyezéssel kapcsolatban: az üzembe helyezési modellek és az erőforrások állapotának ismertetése</a>.</div><br/>

<div id="encryption"><sup>2</sup> Az összes Storage-fiók titkosítása Storage Service Encryption (SSE) használatával történik a nyugalmi állapotban lévő adatokhoz. További információ: <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service encryption for</a>inaktív adatok.</div><br/>

<div id="archive"><sup>3</sup> az Archive Storage és a blob szintű rétegek csak a blokk blobokat támogatják. Az archív szint csak egy egyedi blob szintjén érhető el, nem pedig a Storage-fiók szintjén. További információkért lásd <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">: Azure Blob Storage: gyakori, ritka elérésű és archív tárolási szintek</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> A Zone-redundáns tárolás (ZRS) és a Geo-Zone-redundáns tárolás (GZRS/RA-GZRS) (előzetes verzió) csak a standard általános célú v2-, BlockBlobStorage-és FileStorage-fiókok esetében érhető el bizonyos régiókban. További információ az Azure Storage-redundancia lehetőségeiről: <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage redundancia</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> Az általános célú v2 és az általános célú v1-fiókok prémium szintű teljesítménye csak a lemezes és az oldal blobja esetében érhető el. A blokk-és hozzáfűző Blobok prémium szintű teljesítménye csak a BlockBlobStorage-fiókokon érhető el. A fájlok prémium szintű teljesítménye csak a FileStorage-fiókokon érhető el.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> A Azure Data Lake Storage Gen2 az Azure Blob Storage-ra épülő, big data Analytics szolgáltatáshoz dedikált képességek összessége. A Data Lake Storage Gen2 csak olyan általános célú v2-es Storage-fiókok esetében támogatott, amelyeken engedélyezve van a hierarchikus névtér. További információ a Data Lake Storage Gen2ről: a <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2 bemutatása</a>.</div>
