---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334733"
---
Az alábbi táblázat ismerteti az Azure általános célú v1, v2, Blob storage, blokk blob storage és a Data Lake Storage Gen2 engedélyezett tárfiókok alapértelmezett korlátait. A *be- és visszatárolási* korlát a tárfiókba küldött összes adatra vonatkozik. A *kimenő forgalom* korlát a tárfiókból kapott összes adatra vonatkozik.

| Erőforrás | Korlát |
| --- | --- |
| A tárfiókok száma régiónként előfizetésenként, beleértve a standard, prémium szintű és a Data Lake Storage Gen2 engedélyezett tárfiókok. <sup>3. 20 0</sup> | 250 |
| A tárfiók maximális kapacitása | 5 PiB <sup>1</sup>|
| Blob-tárolók, blobok, fájlmegosztások, táblák, várólisták, entitások vagy üzenetek tárfiókonkénti maximális száma | Nincs korlátozás |
| Maximális kérelemarány<sup>1</sup> tárfiókonként | 20 000 kérelem másodpercenként |
| Maximális be- és beutazás<sup>1</sup> tárfiókonként (USA, Európa régiói) | 25 Gbps |
| Maximális be- és<sup>beutazás 1</sup> tárfiókonként (az USA-n és Európán kívüli régiók) | 5 Gbps, ha az RA-GRS/GRS engedélyezve van, 10 Gb/s LRS/ZRS<sup>2</sup> esetén |
| Az általános célú v2- és Blob-tárfiókok maximális kimenő egysége (minden régió) | 50 Gbps |
| Az általános célú v1-es tárfiókok maximális kimenő egysége (USA-beli régiók) | 20 Gb/s, ha az RA-GRS/GRS engedélyezve van, 30 Gb/s LRS/ZRS<sup>2</sup> esetén |
| Az általános célú v1-es tárfiókok maximális kimenő egysége (nem amerikai régiók) | 10 Gb/s, ha az RA-GRS/GRS engedélyezve van, 15 Gb/s LRS/ZRS<sup>2</sup> esetén |
| Virtuális hálózati szabályok maximális száma tárfiókonként | 200 |
| Az IP-címszabályok maximális száma tárfiókonként | 200 |

<sup>1</sup> Az Azure Storage standard fiókok támogatása magasabb kapacitáskorlátok és a magasabb korlátozások a be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- A fiókkorlátok növelésének kérelmezéséhez forduljon az [Azure-támogatáshoz.](https://azure.microsoft.com/support/faq/)

<sup>2</sup> Ha a tárfiók olvasási hozzáférése engedélyezve van a georedundáns tárolással (RA-GRS) vagy a geozóna redundáns tárolásával (RA-GZRS), akkor a másodlagos hely kimenő tárolói megegyeznek az elsődleges hely kilépési célértékével. [Az Azure Storage replikációs](https://docs.microsoft.com/azure/storage/common/storage-redundancy) beállításai a következők:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Az Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) az Azure Blob storage-ra épülő big data-elemzési képességek összeskészlete. Az Azure Storage és a blob storage korlátai a Data Lake Storage Gen2-re vonatkoznak.

> [!NOTE]
> A Microsoft azt javasolja, hogy a legtöbb esetben használjon általános célú v2-es tárfiókot. Könnyedén frissíthet egy általános célú v1-es vagy egy Azure Blob-tárfiókot egy általános célú v2-fiókra állásidő nélkül, és anélkül, hogy adatokat kellene másolnia. További információ: [Frissítés általános célú v2-es tárfiókra.](../articles/storage/common/storage-account-upgrade.md)

Ha az alkalmazás igényei meghaladják az egyetlen tárfiók méretezhetőségi céljait, létrehozhatja az alkalmazást több tárfiók használatához. Ezután particionálhatja az adatobjektumokat azok között a tárfiókok között. A kötetdíjszabásról az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/)című témakörben talál további információt.

Minden tárfiók egy sima hálózati topológián fut, függetlenül attól, hogy mikor hozták létre őket. Az Azure Storage sík hálózati architektúrájával és méretezhetőségével kapcsolatos további információkért lásd: [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
