---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/27/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e29cdd56d1c43b3d0e8fc6ca233ac19d8b0004ff
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357433"
---
Az alábbi táblázat az Azure általános célú v1 (GPv1), v2 (GPv2), blob, Premium BlockBlob és Data Lake Gen2 engedélyezett Storage-fiókok alapértelmezett határértékeit ismerteti. A *bejövő* forgalom korlátja a Storage-fiókba küldendő összes adattal vonatkozik. A *kimenő* forgalom korlátja a Storage-fiókból kapott összes adattal vonatkozik.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A Storage-fiókok régiónként/előfizetések száma, beleértve a standard, a prémium és a Gen2-fiókokat is<sup>3</sup> | 250 |
| Storage-fiók maximális kapacitása | 5 PiB <sup>1</sup>|
| BLOB-tárolók, blobok, fájlmegosztás, táblák, várólisták, entitások vagy üzenetek maximális száma Storage-fiókban | Korlátlan |
| Kérelmek maximális száma<sup>1</sup> – Storage-fiók | 20 000 kérelem másodpercenként |
| Maximális belépés<sup>1</sup> /Storage-fiókra (USA, Európa régiói) | 25 GB/s |
| Maximális bejövő forgalom<sup>1</sup> /Storage-fiókban (az USA-beli és európai régión kívül) | 5 GB/s if RA-GRS/GRS engedélyezve, 10 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v2-és blob Storage-fiókokhoz (minden régió) | 50 GB/s |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (USA-régiók) | 20 GB/s if RA-GRS/GRS engedélyezve, 30 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (nem USA-régiók) | 10 GB/s if RA-GRS/GRS engedélyezve, 15 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Virtuális hálózati szabályok maximális száma Storage-fiókban | 200 |
| IP-címekre vonatkozó szabályok maximális száma Storage-fiókban | 200 |

<sup>1</sup> az Azure Storage standard fiókjai magasabb kapacitási korlátokat és magasabb korlátot támogatnak a bejövő forgalomra vonatkozóan. A fiók korlátozásának növeléséhez forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/faq/).

<sup>2</sup> ha a Storage-fiókhoz engedélyezve van a Geo-redundáns tárolás (ra-GRS) vagy a Geo-Zone-redundáns tárolás (ra-GZRS), akkor a másodlagos hely kimenő elérési céljai azonosak az elsődleges helyével. Az [Azure Storage replikációs](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségei a következők:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) az Azure Blob Storage-ra épülő, Big Data Analytics szolgáltatáshoz dedikált képességek összessége. Az Azure Storage és a blob Storage korlátozásai a Gen2-re vonatkoznak.

> [!NOTE]
> A Microsoft azt javasolja, hogy a legtöbb forgatókönyv esetén általános célú v2 Storage-fiókot használjon. Az általános célú v1-et vagy az Azure Blob Storage-fiókot egy általános célú v2-fiókra is frissítheti állásidő nélkül, és anélkül, hogy adatmásolásra lenne szükség. További információ: [frissítés általános célú v2 Storage-fiókra](../articles/storage/common/storage-account-upgrade.md).

Ha az alkalmazás igényei meghaladják egy adott Storage-fiók skálázhatósági céljait, az alkalmazást több Storage-fiók használatára is felépítheti. Ezután particionálhatja az adatobjektumokat a Storage-fiókok között. A mennyiségi díjszabással kapcsolatos információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Az összes Storage-fiók egy egyszerű hálózati topológián fut, függetlenül attól, hogy mikor jöttek létre. Az Azure Storage lapos hálózati architektúrával és a méretezhetőséggel kapcsolatos további információkért lásd [: Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)-használattal. A [Gen2-fiókhoz egy hierarchikus névtér is engedélyezhető](../articles/storage/blobs/data-lake-storage-namespace.md) a többprotokollos hozzáféréshez használható, a lapos névtér mellett. Az egyszerű és a hierarchikus névtérbeli tárolási fiókok egyaránt támogatják a jelen cikkben ismertetett skálázhatósági és teljesítménybeli célokat.
