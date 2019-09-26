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
ms.openlocfilehash: 0e5c1974ecb1b3efb8df602c76700b7db04a88d7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310526"
---
Az Azure Storage számos különböző típusú Storage-fiókot kínál. Mindegyik típus támogatja a különböző funkciókat, és saját díjszabási modellel rendelkezik. Vegye figyelembe ezeket a különbségeket, mielőtt létrehoz egy Storage-fiókot az alkalmazások számára legmegfelelőbb fiók típusának megállapításához. A Storage-fiókok típusai a következők:

- **Általános célú v2-fiókok**: Alapszintű Storage-fióktípus blobokhoz, fájlokhoz, várólistákhoz és táblákhoz. Az Azure Storage-t használó legtöbb forgatókönyv esetén ajánlott.
- **Általános célú v1-fiókok**: A Blobok, fájlok, várólisták és táblák örökölt fióktípus. Ha lehetséges, használja az általános célú v2-fiókokat.
- **Blob Storage-fiókok letiltása**: Csak blob Storage-fiókok prémium teljesítményű jellemzőkkel. Ajánlott olyan forgatókönyvek esetén, amelyek nagy tranzakciós díjszabással, kisebb objektumok használatával vagy konzisztens, alacsony tárolási késést igényelnek.
- **FileStorage Storage-fiókok**: Csak a prémium szintű teljesítménnyel rendelkező Storage-fiókok. Nagyvállalati vagy nagy teljesítményű alkalmazások esetében ajánlott.
- **Blob Storage-fiókok**: Csak blob Storage-fiókok. Ha lehetséges, használja az általános célú v2-fiókokat.

Az alábbi táblázat a Storage-fiókok és azok képességeinek típusait ismerteti:

| Tárfiók típusa | Támogatott szolgáltatások                       | Támogatott teljesítményszint      | Támogatott hozzáférési szintek         | Replikációs lehetőségek               | Üzemi modell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Encryption<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Általános célú v2   | BLOB, fájl, üzenetsor, tábla és lemez       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gyors elérésű, ritka elérésű Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (előzetes verzió), RA-GZRS (előzetes verzió)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Titkosítva              |
| Általános célú v1   | BLOB, fájl, üzenetsor, tábla és lemez       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | –                            | LRS, GRS, RA-GRS                  | Resource Manager, klasszikus    | Titkosítva              |
| BLOB Storage letiltása   | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | Prémium                       | –                            | LRS                               | Resource Manager             | Titkosítva              |
| FileStorage   | Csak fájlok | Prémium                       | –                            | LRS                               | Resource Manager             | Titkosítva              |
| Blob Storage         | BLOB (csak a Blobok letiltása és a Blobok hozzáfűzése) | Standard                      | Gyors elérésű, ritka elérésű Archívum<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Titkosítva              |

<div id="deployment-model"><sup>1</sup> A Azure Resource Manager üzembe helyezési modell használata ajánlott. A klasszikus üzemi modellt használó Storage-fiókok bizonyos helyszíneken is létrehozhatók, és a meglévő klasszikus fiókok továbbra is támogatottak. További információ: <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager és klasszikus üzembe helyezés: Az üzembe helyezési modellek és az erőforrások</a>állapotának megismerése.</div>

<div id="encryption"><sup>2</sup> Az összes Storage-fiók titkosítása Storage Service Encryption (SSE) használatával történik a nyugalmi állapotban lévő adatokhoz. További információ: <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service encryption for</a>inaktív adatok.</div>

<div id="archive"><sup>3</sup> Az archív szint csak egy egyedi blob szintjén érhető el, nem pedig a Storage-fiók szintjén. Csak a Blobok blokkolása és a hozzáfűző Blobok archiválása lehetséges. További információt az Azure Blob <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Storage-ban talál: Gyakori, ritka elérésű és archív tárolási szintek</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup> A Zone-redundáns tárolás (ZRS) és a Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió) csak a standard általános célú v2-es Storage-fiókok esetében érhető el. A ZRS kapcsolatos további információkért lásd <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">: Zone-redundáns tárolás (ZRS): Magasan elérhető Azure Storage-</a>alkalmazások. További információ a GZRS: <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">geo-Zone-redundáns tárolás a nagyfokú rendelkezésre állás és a maximális tartósság érdekében (előzetes verzió)</a>. További információ az egyéb replikációs lehetőségekről: az <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage replikációja</a>.</div>

<div id="premium-performance"><sup>5</sup> Az általános célú v2 és az általános célú v1-fiókok prémium szintű teljesítménye csak a lemezes és az oldal blobja esetében érhető el.</div>
