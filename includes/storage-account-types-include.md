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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371552"
---
Az Azure Storage különböző típusú tárfiókokat kínál. Mindegyik típus más-más funkciókat támogat, és más a díjszabási modellje. Egy tárfiók létrehozása előtt vegye figyelembe ezeket a különbségeket, és döntse el, hogy az alkalmazásai számára melyik fióktípus használata jár a legtöbb előnnyel. A tárfiókok típusai a következők:

- **Általános célú v2-fiókok**: Alapszintű tárfióktípus blobokhoz, fájlokhoz, üzenetsorokhoz és táblákhoz. Az Azure Storage legtöbb használati forgatókönyvéhez javasolható.
- **Általános célú v1-fiókok**: Örökölt fióktípus blobokhoz, fájlokhoz, üzenetsorokhoz és táblákhoz. Ha lehetséges, az általános célú v2-fiókot használja helyette.
- **BlockBlobStorage-fiókok**: Ez a tárfióktípus prémium szintű teljesítményjellemzőket biztosít a blokkblobokhoz és hozzáfűző blobokhoz. Olyan forgatókönyvekhez ajánlott, amelyeknél magas a tranzakciósebesség, kisebb objektumokat kell használni, vagy egyenletesen alacsony tárolási késésre van szükség.
- **FileStorage-fiókok**: Csak fájlokhoz használható tárfióktípus, prémium szintű teljesítményjellemzőkkel. Nagyvállalati vagy nagy teljesítményű és nagy léptékben használt alkalmazásokhoz ajánlott.
- **BlobStorage-fiókok**: Örökölt, csak blobokhoz használható tárfiók. Ha lehetséges, az általános célú v2-fiókot használja helyette.

A következő táblázat ismerteti a tárfiókok típusait és képességeit:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszintek      | Támogatott hozzáférési szintek         | Replikációs lehetőségek               | Üzemi modell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Titkosítás<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Általános célú v2   | Blob, fájl, üzenetsor, tábla, lemez és Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gyakori elérésű, ritka elérésű, archív<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (előzetes verzió), RA-GZRS (előzetes verzió)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Titkosított              |
| Általános célú v1   | Blob, fájl, üzenetsor, tábla és lemez       | Standard, Prémium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS, GRS, RA-GRS                  | Resource Manager, klasszikus    | Titkosított              |
| BlockBlobStorage   | Blob (csak blokkblobok és hozzáfűző blobok) | Prémium                       | N/A                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Titkosított              |
| FileStorage   | Csak fájl | Prémium                       | N/A                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Titkosított              |
| BlobStorage         | Blob (csak blokkblobok és hozzáfűző blobok) | Standard                      | Gyakori elérésű, ritka elérésű, archív<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Titkosított              |

<div id="deployment-model"><sup>1</sup>Javasolt az Azure Resource Manager-alapú üzemi modell használata. Egyes helyszíneken továbbra is létrehozhatók klasszikus üzemi modellt használó tárfiókok, és a meglévő klasszikus fiókok továbbra is támogatottak. További információk: <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Az Azure Resource Manager-alapú és a klasszikus üzemelő példányok: Az üzemi modellek és az erőforrások állapotának ismertetése</a>.</div><br/>

<div id="encryption"><sup>2</sup>Minden tárfiókban a Storage Service Encryption (SSE) használatával vannak titkosítva az inaktív adatok. További információk: <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption inaktív adatokhoz</a>.</div><br/>

<div id="archive"><sup>3</sup> Az archív tárolás és a blobszintű rétegezés csak a blokkblobokat támogatja. Az archív szint csak a különálló blobok szintjén érhető el, a tárfiók szintjén nem. További információk: <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: A gyakori és ritka elérésű, valamint az archív tárolási szintek</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>A zónaredundáns tárolás (ZRS) és a földrajzizóna-redundáns tárolás (GZRS/RA-GZRS) (előzetes verziója) csak a standard általános célú V2-, BlockBlobStorage- és FileStorage-fiókokhoz érhető el egyes régiókban. További információk az Azure Storage redundanciabeállításairól: <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage-redundancia</a>.</div><br/>

<div id="premium-performance"><sup>5</sup>A prémium szintű teljesítmény az általános célú v2- és az általános célú v1-fiókokban csak a lemezekhez és a lapblobokhoz érhető el. A prémium szintű teljesítmény a blokk- és a hozzáfűző blobokhoz csak a BlockBlobStorage-fiókokban érhető el. A prémium szintű teljesítmény a fájlokhoz csak a FileStorage-fiókokban érhető el.</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Az Azure Data Lake Storage Gen2 az Azure Blob Storage-ra épülő big data-elemzésekhez dedikált képességek gyűjteménye. A Data Lake Storage Gen2-t csak az olyan általános célú V2-tárfiókok támogatják, amelyekben engedélyezve van a hierarchikus névtér. További információk a Data Lake Storage Gen2-ről: <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Az Azure Data Lake Storage Gen2 bemutatása</a>.</div>
