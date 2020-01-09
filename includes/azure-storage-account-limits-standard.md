---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/13/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0ecb4f5fe54e9895898156893c1e686c6cc24e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392301"
---
Az alábbi táblázat az Azure általános célú v1, v2 és blob Storage-fiókok alapértelmezett korlátait ismerteti. A *bejövő* forgalom korlátja a Storage-fiókba küldött kérelmekből származó összes értékre vonatkozik. A *kimenő* forgalom korlátja a Storage-fiókból kapott válaszok összes adatértékére vonatkozik.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A Storage-fiókok régiónként/előfizetések száma, beleértve a standard és a prémium szintű fiókokat is | 250 |
| Storage-fiók maximális kapacitása | 2 PiB az Egyesült ÁLLAMOKban és Európában, valamint 500 TiB az összes többi régióban (beleértve az Egyesült Királyságot)<sup>1</sup>|
| BLOB-tárolók, blobok, fájlmegosztás, táblák, várólisták, entitások vagy üzenetek maximális száma Storage-fiókban | Korlátlan |
| Kérelmek maximális száma<sup>1</sup> – Storage-fiók | 20 000 kérelem másodpercenként |
| Maximális belépés<sup>1</sup> /Storage-fiókra (USA, Európa régiói) | 25 GB/s |
| Maximális bejövő forgalom<sup>1</sup> /Storage-fiókban (az USA-beli és európai régión kívül) | 5 GB/s if RA-GRS/GRS engedélyezve, 10 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v2-és blob Storage-fiókokhoz (minden régió) | 50 GB/s |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (USA-régiók) | 20 GB/s if RA-GRS/GRS engedélyezve, 30 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (nem USA-régiók) | 10 GB/s if RA-GRS/GRS engedélyezve, 15 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Virtuális hálózati szabályok maximális száma Storage-fiókban | 200 |
| IP-címekre vonatkozó szabályok maximális száma Storage-fiókban | 200 |

<sup>1</sup> Az Azure Storage standard fiókjai magasabb kapacitási korlátokat és magasabb korlátot támogatnak a bejövő forgalomra vonatkozóan. Ha szeretné megemelni a fiókokra vonatkozó korlátozásokat a bejövő forgalomhoz, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/faq/). További információ: [nagyobb méretű, magasabb szintű Storage-fiókok bejelentése](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>2</sup> ha a Storage-fiókhoz engedélyezve van a Geo-redundáns tárolás (ra-GRS) vagy a Geo-Zone-redundáns tárolás (ra-GZRS), akkor a másodlagos hely kimenő elérési céljai azonosak az elsődleges helyével. Az [Azure Storage replikációs](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségei a következők:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> A Microsoft azt javasolja, hogy a legtöbb forgatókönyv esetén általános célú v2 Storage-fiókot használjon. Az általános célú v1-et vagy az Azure Blob Storage-fiókot egy általános célú v2-fiókra is frissítheti állásidő nélkül, és anélkül, hogy adatmásolásra lenne szükség. További információ: [frissítés általános célú v2 Storage-fiókra](../articles/storage/common/storage-account-upgrade.md).

Ha az alkalmazás igényei meghaladják egy adott Storage-fiók skálázhatósági céljait, az alkalmazást több Storage-fiók használatára is felépítheti. Ezután particionálhatja az adatobjektumokat a Storage-fiókok között. A mennyiségi díjszabással kapcsolatos információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Az összes Storage-fiók egy egyszerű hálózati topológián fut, és támogatja a jelen cikkben ismertetett skálázhatósági és teljesítményi célokat, függetlenül attól, hogy mikor hozták létre. Az Azure Storage lapos hálózati architektúrával és a méretezhetőséggel kapcsolatos további információkért lásd [: Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)-használattal.
