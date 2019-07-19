---
title: Az Azure Storage skálázhatósági és teljesítménybeli céljai – Storage-fiókok
description: Ismerje meg a méretezhetőséget és a teljesítményre vonatkozó célokat, beleértve a kapacitást, a kérelmek sebességét és a bejövő és kimenő sávszélességet az Azure Storage-fiókok esetében.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326949"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Az Azure Storage skálázhatósági és teljesítménybeli céljai a Storage-fiókok esetében

Ez a cikk az Azure Storage-fiókok méretezhetőségi és teljesítménybeli céljait részletezi. Az itt felsorolt méretezhetőségi és teljesítményi célok magas végponti célokat szolgálnak, de elérhetők. A Storage-fiók által elért kérelmek aránya és sávszélessége minden esetben a tárolt objektumok méretétől, a felhasznált hozzáférési mintáktól, valamint az alkalmazás által végrehajtott számítási feladatok típusától függ.

Ügyeljen arra, hogy tesztelje a szolgáltatást annak megállapítása érdekében, hogy a teljesítménye megfelel-e a követelményeknek. Ha lehetséges, kerülje a forgalom sebességének hirtelen kiugrását, és győződjön meg arról, hogy a forgalom megfelelően oszlik meg a partíciók között.

Ha az alkalmazás eléri azt a korlátot, amelyet a partíció képes kezelni a munkaterhelés esetében, az Azure Storage megkezdi a 503-as hibakód (a kiszolgáló foglalt) vagy a 500 (művelet időtúllépése) válaszait. Ha 503 hiba történt, érdemes lehet módosítani az alkalmazást, hogy az újrapróbálkozások exponenciális leállítási szabályzatot használjanak. Az exponenciális leállítási lehetővé teszi a partíció terhelésének csökkentését, valamint az adott partíció felé irányuló adatforgalom megkönnyítése érdekében.

## <a name="storage-account-scale-limits"></a>Storage-fiók méretezési korlátai

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Prémium szintű teljesítményű Storage-fiók méretezési korlátai

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Tárolási erőforrás-szolgáltató méretezési korlátai

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob Storage – méretezési célok

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok

A Azure Files és Azure File Sync méretezési és teljesítményi céljaival kapcsolatos további információkért lásd: [Azure Files skálázhatóság és teljesítmény-célkitűzések](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> A Storage-fiókra vonatkozó korlátozások minden megosztásra érvényesek. A legfeljebb a Storage-fiókok maximális skálázása csak akkor érhető el, ha a Storage-fiókhoz csak egy megosztás van.
>
> Az 5 TiB-nál nagyobb standard fájlmegosztás előzetes verzióban érhető el, és bizonyos korlátozásokkal rendelkezik.
> A korlátozások listájának megtekintéséhez és a nagyobb fájlmegosztás-méretek előzetes verziójának előkészítéséhez tekintse meg a Azure Files tervezési útmutatójának [szabványos fájlmegosztás](../files/storage-files-planning.md#standard-file-shares) szakaszát.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Prémium szintű fájlok méretezési céljai

A prémium szintű fájlok esetében három korlátozást kell figyelembe venni: a Storage-fiókokat, a megosztásokat és a fájlokat.

Példa: Egyetlen megosztás elérheti a 100 000 IOPS, és egyetlen fájl akár 5 000 IOPS is méretezhető. Tehát ha például három fájl található egy megosztásban, akkor az adott megosztásból lekérhető maximális IOPs 15 000.

#### <a name="premium-file-share-limits"></a>Prémium fájlmegosztás korlátai

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync méretezési célok

Azure File Sync a korlátlan használat céljával lett tervezve, de a korlátlan használat nem mindig lehetséges. Az alábbi táblázat a Microsoft tesztelésének határait mutatja be, és azt is jelzi, hogy mely célok rögzített határértékek:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure üzenetsor-tárolási méretezési célok

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table Storage – méretezési célok

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Lásd még

- [A Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/)
- [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-subscription-service-limits.md)
- [Azure Storage-replikáció](../storage-redundancy.md)
- [A Microsoft Azure Storage teljesítmény- és skálázhatósági ellenőrzőlistája](../storage-performance-checklist.md)
