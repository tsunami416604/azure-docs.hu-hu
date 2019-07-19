---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 54050116f9894f9a7903dad53caf6b01228313b7
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326964"
---
Az alábbi táblázat az Azure általános célú v1, v2 és blob Storage-fiókok alapértelmezett korlátait ismerteti. A *bejövő* forgalom korlátja a Storage-fiókba küldött kérelmekből származó összes értékre vonatkozik. A *kimenő* forgalom korlátja a Storage-fiókból kapott válaszok összes adatértékére vonatkozik.

| Resource | Alapértelmezett korlát |
| --- | --- |
| A Storage-fiókok régiónként/előfizetések száma, beleértve a standard és a prémium szintű fiókokat is | 250 |
| Storage-fiók maximális kapacitása | 2 PB az USA és Európa számára, és 500 TB minden más régióban (beleértve az Egyesült Királyságot) <sup>1</sup>|
| BLOB-tárolók, blobok, fájlmegosztás, táblák, várólisták, entitások vagy üzenetek maximális száma Storage-fiókban | Korlátlan |
| Kérelmek maximális száma<sup>1</sup> – Storage-fiók | 20 000 kérelem másodpercenként |
| Maximális belépés<sup>1</sup> /Storage-fiókra (USA, Európa régiói) | 25 GB/s |
| Maximális bejövő forgalom<sup>1</sup> /Storage-fiókban (az USA-beli és európai régión kívül) | 5 GB/s if RA-GRS/GRS engedélyezve, 10 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v2-és blob Storage-fiókokhoz (minden régió) | 50 GB/s |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (USA-régiók) | 20 GB/s if RA-GRS/GRS engedélyezve, 30 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (nem USA-régiók) | 10 GB/s if RA-GRS/GRS engedélyezve, 15 GB/s a LRS/ZRS<sup>2</sup> esetében |

<sup>1</sup> Az Azure standard Storage-fiókok magasabb kapacitási korlátokat és magasabb korlátot támogatnak a bejövő forgalomra vonatkozóan. Ha szeretné megemelni a fiókokra vonatkozó korlátozásokat a bejövő forgalomhoz, forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/faq/). További információ: [nagyobb méretű, magasabb szintű Storage-fiókok](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)bejelentése.

<sup>2</sup> az [Azure Storage replikációs](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségei a következők:

- **RA-GRS**: Olvasási hozzáférés a Geo-redundáns tároláshoz. Ha az RA-GRS engedélyezve van, a másodlagos hely kimenő elérési céljai ugyanazok, mint az elsődleges helyen.
- **GRS**: Geo-redundáns tárolás.
- **ZRS**: Zóna – redundáns tárolás.
- **LRS**: Helyileg redundáns tárolás.

> [!NOTE]
> Javasoljuk, hogy a legtöbb forgatókönyv esetén használjon általános célú v2-es Storage-fiókot. Az általános célú v1-et vagy az Azure Blob Storage-fiókot egy általános célú v2-fiókra is frissítheti állásidő nélkül, és anélkül, hogy adatmásolásra lenne szükség.
>
> Az Azure Storage-fiókokkal kapcsolatos további információkért lásd: a [Storage-fiók áttekintése](../articles/storage/common/storage-account-overview.md).

Ha az alkalmazás igényei meghaladják egy adott Storage-fiók skálázhatósági céljait, az alkalmazást több Storage-fiók használatára is felépítheti. Ezután particionálhatja az adatobjektumokat a Storage-fiókok között. A mennyiségi díjszabással kapcsolatos információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Az összes Storage-fiók egy egyszerű hálózati topológián fut, és támogatja a jelen cikkben ismertetett skálázhatósági és teljesítményi célokat, függetlenül attól, hogy mikor hozták létre. Az Azure Storage lapos hálózati architektúrával és a méretezhetőséggel kapcsolatos további információkért lásd [Microsoft Azure Storage: Magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)-ellenőrzéssel.

