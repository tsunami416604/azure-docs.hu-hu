---
title: Az Azure Storage méretezhetőségi és teljesítménycéloknak |} Microsoft Docs
description: További tudnivalók a méretezhetőségi és Teljesítménycélok az Azure Storage, beleértve a kapacitás, a lekérdezési gyakorisága és a bejövő és kimenő sávszélesség mindkét standard és prémium szintű storage-fiókok. Ismerje meg az Azure Storage szolgáltatások belül partíciók teljesítménycéloknak.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: e393bb9e7615b893699caf5a931ede5803046892
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Az Azure Storage méretezhetőségi és teljesítménycéljai
## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a méretezhetőség és teljesítmény témakörök az Azure Storage. Egyéb Azure korlátozását összefoglalását lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../../azure-subscription-service-limits.md).

> [!NOTE]
> Az összes tárfiók az új egyszerű hálózati topológia futtatnak, és támogatja a méretezhetőségi és Teljesítménycélok, a cikkben ismertetett, függetlenül attól, amikor a létrehozás. További információ az Azure Storage egyszerű hálózati architektúra és a méretezhetőség: [Microsoft Azure Storage: A magas rendelkezésre álló felhőalapú tárolási szolgáltatásba az erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Az itt felsorolt méretezhetőségi és Teljesítménycélok csúcskategóriás célozza, de elérhető. Minden olyan esetben, a lekérdezési gyakorisága és a sávszélesség érhető el a tárolási fiók attól függ, hogy az objektumok tárolja, a hozzáférési minták funkcióját, mérete, és milyen típusú az alkalmazás hajt végre. Mindenképpen ellenőrizze a szolgáltatást annak meghatározásához, hogy a teljesítményét, megfelel-e a követelményeknek. Ha lehetséges elkerülése érdekében a forgalom arányát hirtelen teljesítményt, és győződjön meg arról, hogy forgalom jól elosztott partíciók között.
> 
> Amikor az alkalmazás eléri a korlátot, mi partíció kezelni tud a munkaterhelés számára, Azure Storage elkezdi 503-as (kiszolgáló elfoglalt) vagy a hiba 500 (a művelet időkorlátja lejár) válaszok adja vissza. Ezek hiba fordul elő, ha majd az alkalmazás a használata ajánlott az exponenciális leállítási házirend újrapróbálkozások. Az exponenciális leállítási lehetővé teszi a terhelés a partíción, és kimenő forgalmának kiszolgálására adott partíció igényeiben jelentkező megkönnyítése érdekében.
> 
> 

Ha az alkalmazás igényeinek túllépi a méretezhetőségi célok egyetlen tárfiók, több tárfiókot használja az alkalmazást hozhat létre. Az adatok objektumok is majd particionálása, ezek tárfiókok között. Lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) a mennyiségi díjszabásról.

## <a name="scalability-targets-for-a-storage-account"></a>A tárfiók vonatkozó méretezhetőségi célok
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Az Azure Blob storage méretezési célok
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Az Azure fájlok méretezési célok
A méretezési és teljesítménycéljaival kapcsolatos információkért Azure fájlok és az Azure fájlszinkronizálás, lásd: [Azure fájlok méretezhetőségi és Teljesítménycélok](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Az Azure fájlszinkronizálás méretezési célok
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Az Azure várólista tárolási méretezési célok
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Az Azure tábla tárolási méretezési célok
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Lásd még:
* [Tárolás díjszabása](https://azure.microsoft.com/pricing/details/storage/)
* [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../../azure-subscription-service-limits.md)
* [Az Azure Storage replikáció](../storage-redundancy.md)
* [A Microsoft Azure Storage teljesítmény- és skálázhatósági ellenőrzőlistája](../storage-performance-checklist.md)
* [A Microsoft Azure Storage: Egy magas rendelkezésre állású felhőalapú tárolási szolgáltatásba erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

