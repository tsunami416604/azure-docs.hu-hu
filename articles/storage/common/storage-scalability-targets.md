---
title: Az Azure Storage méretezhetőségi és teljesítménycéljai - storage-fiókok
description: Ismerje meg a skálázhatósági és teljesítménycéljait, beleértve a kapacitás, a kérelmek arányának és a bejövő és kimenő sávszélesség, az Azure storage-fiókok.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 96322c730300e360ed03f4b623db2a7f18825f55
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267701"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Az Azure Storage méretezhetőségi és teljesítménycéljai storage-fiókok

Ez a cikk ismerteti az Azure storage-fiókok a méretezhetőségi és teljesítménycéljai. Az itt felsorolt méretezhetőségi és teljesítménycéljai csúcskategóriás célozza, de elérhető. Minden esetben a kérések aránya és sávszélességet, hogy a tárolási fiók attól függ, hogy az objektumok tárolja, a használt fel, hozzáférési minták mérete és a munkaterhelés típusától az alkalmazás végrehajtja.

Győződjön meg arról, a szolgáltatást annak meghatározásához, hogy a teljesítmény megfelel-e a követelmények ellenőrzéséhez. Ha lehetséges kerülje a forgalom arányát hirtelen kiugrások és ellenőrizze, hogy forgalmat jól elosztott partíciók között.

Az alkalmazás eléri a korlátot, mi partíció képes kezelni a számítási feladatok számára, amikor megkezdi az Azure Storage hibakód 503-as (foglalt kiszolgáló) vagy hibakód: 500 (művelet időtúllépése) választ adnak vissza. 503-as hiba fordul elő, ha fontolja meg az újrapróbálkozások az exponenciális visszatartással házirend használatához az alkalmazás módosítása. Az exponenciális visszatartással lehetővé teszi, hogy a terhelés csökkentéséhez és az adott partíció adatforgalmi kiugrások megkönnyítése érdekében a partíción.

## <a name="storage-account-scale-limits"></a>Storage-fiók méretkorlátai

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Prémium szintű teljesítmény storage-fiók méretkorlátai

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Storage erőforrás-szolgáltató méretkorlátai

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Az Azure Blob storage méretezési célok

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok

Az Azure Files és az Azure File Sync a méretezéshez és teljesítményhez célokon további információkért lásd: [Azure Files méretezhetőségi és teljesítménycéljai](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Prémium files tárolók skálázása

A prémium szintű fájlok megfontolandó korlátozások három kategóriába sorolhatók: storage-fiókok, a megosztások és a fájlokat.

Példa: Egyetlen megosztása 100 000 IOPS érhető el, és egyetlen fájl akár 5000 IOPS skálázhatja. Így például, ha már három egy megosztásban lévő fájlokat, a maximális iops-értékkel el azt a megosztást, 15 000.

#### <a name="premium-file-share-limits"></a>Prémium szintű fájl megosztási korlátozások

> [!IMPORTANT]
> Tárfiókok korlátai megosztások a alkalmazni. Méretezés akár a storage-fiókok maximális száma csak akkor megvalósítható, ha csak egy megosztás tárfiókonként.

|Terület  |Cél  |
|---------|---------|
|Min. méret                        |100 GiB      |
|Maximális méret                        |100 TiB      |
|Minimális méret növelése vagy csökkentése    |1 GiB      |
|Alapkonfiguráció IOPS    |1 GIB-ra legfeljebb 100 000 IOPS|
|Tartalékkapacitás IOPS    |3 x IOPS száma legfeljebb 100 000 GiB|
|Minimális sávszélesség                     |100        |
|Bandwidth |0,1 akár 5 GB/s GiB gépenként MB/s     |
|A pillanatképek maximális száma        |200       |

#### <a name="premium-file-limits"></a>Prémium szintű fájlokra vonatkozó korlátok

|Terület  |Cél  |
|---------|---------|
|Méret                  |1 TiB         |
|Fájlonkénti maximális iops-érték     |5000         |
|Egyidejű kezeli    |2,000         |

### <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync méretezési célok

Az Azure File Sync a cél az korlátlan használat úgy lett kialakítva, de a korlátlan használat értéke nem mindig lehetséges. Az alábbi táblázat azt jelzi, hogy a Microsoft-tesztelés határain és is jelzi, hogy mely célok a szigorú korlátozásokat:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Az Azure Queue storage méretezési célok

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Az Azure Table storage méretezési célok

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Lásd még:

- [A Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)
- [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md)
- [Az Azure Storage replikáció](../storage-redundancy.md)
- [A Microsoft Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistája](../storage-performance-checklist.md)