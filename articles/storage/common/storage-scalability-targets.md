---
title: Az Azure Storage méretezhetőségi és Teljesítménycéljai |} A Microsoft Docs
description: További információk a méretezhetőségi és teljesítménycéljai az Azure Storage, beleértve a kapacitás, a kérelmek arányának és a bejövő és kimenő sávszélesség mindkét standard és prémium szintű storage-fiókok. Ismerje meg, teljesítmény-határértékeit belül az Azure Storage minden partíciók.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 1db7219beead4d7ee228baff4c802c105419ba1c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524603"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Az Azure Storage méretezhetőségi és teljesítménycéljai
## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a méretezhetőség és teljesítmény-témakörök Azure Storage-hoz. Más Azure-korlátozások összefoglalása, lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md).

> [!NOTE]
> Az összes storage-fiókok az új egybesimított hálózati topológia fut, és támogatja a következő cikkben ismertetett, függetlenül attól, ha létrehozták őket a méretezhetőségi és teljesítménycéljai. További információ az Azure Storage egybesimított hálózati architektúra és a méretezhetőség: [a Microsoft Azure Storage: A magas rendelkezésre álló felhős Társzolgáltatás erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Az itt felsorolt méretezhetőségi és teljesítménycéljai csúcskategóriás célozza, de elérhető. Minden esetben a kérések aránya és sávszélességet, hogy a tárolási fiók attól függ, hogy az objektumok tárolja, a használt fel, hozzáférési minták mérete és a munkaterhelés típusától az alkalmazás végrehajtja. Győződjön meg arról, a szolgáltatást annak meghatározásához, hogy a teljesítmény megfelel-e a követelmények ellenőrzéséhez. Ha lehetséges kerülje a forgalom arányát hirtelen kiugrások és ellenőrizze, hogy forgalmat jól elosztott partíciók között.
> 
> Az alkalmazás eléri a korlátot, mi partíció képes kezelni a számítási feladatok számára, amikor megkezdi az Azure Storage hibakód 503-as (foglalt kiszolgáló) vagy hibakód: 500 (művelet időtúllépése) választ adnak vissza. Ha ezek a hibák is megjelenhetnek, majd az alkalmazás kell használnia az exponenciális visszatartással házirend újrapróbálkozások. Az exponenciális visszatartással lehetővé teszi, hogy a terhelés csökkentéséhez és az adott partíció adatforgalmi kiugrások megkönnyítése érdekében a partíción.
> 
> 

Ha az alkalmazása igényeit meghaladja a skálázhatósági célokat, az egy tárfiókban, hozhat létre az alkalmazás több tárfiók használata. Ezután az adatobjektumok particionáló ezen a tárfiókon keresztül. Lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/) számára a mennyiségi díjszabásról.

## <a name="scalability-targets-for-a-storage-account"></a>A tárfiók skálázási célértékei
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Az Azure Blob storage méretezési célok
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok
Az Azure Files és az Azure File Sync a méretezéshez és teljesítményhez célokon további információkért lásd: [Azure Files méretezhetőségi és teljesítménycéljai](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync méretezési célok
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Az Azure Queue storage méretezési célok
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Az Azure Table storage méretezési célok
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Lásd még:
* [A Storage szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/storage/)
* [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../../azure-subscription-service-limits.md)
* [Az Azure Storage replikáció](../storage-redundancy.md)
* [A Microsoft Azure Storage teljesítmény- és skálázhatósági ellenőrzőlistája](../storage-performance-checklist.md)
* [A Microsoft Azure Storage: Magas rendelkezésre állású Felhőbeli Tárolószolgáltatásba erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

