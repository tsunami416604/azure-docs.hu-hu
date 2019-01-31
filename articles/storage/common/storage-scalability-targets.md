---
title: Az Azure Storage skálázhatósági és teljesítmény-határértékei
description: Ismerje meg a skálázhatósági és teljesítménycéljait, beleértve a kapacitás, a kérelmek arányának és a standard szintű Azure storage-fiókok a bejövő és kimenő sávszélesség.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: f8227495d7ca2e1d43b74066d359d0d59e456263
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451206"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>Az Azure Storage méretezhetőségi és teljesítménycéljai standard storage-fiókok

Ez a cikk részletesen, a méretezhetőségi és teljesítménycéljai standard szintű Azure storage-fiókok. Az itt felsorolt méretezhetőségi és teljesítménycéljai csúcskategóriás célozza, de elérhető. Minden esetben a kérések aránya és sávszélességet, hogy a tárolási fiók attól függ, hogy az objektumok tárolja, a használt fel, hozzáférési minták mérete és a munkaterhelés típusától az alkalmazás végrehajtja. 

Győződjön meg arról, a szolgáltatást annak meghatározásához, hogy a teljesítmény megfelel-e a követelmények ellenőrzéséhez. Ha lehetséges kerülje a forgalom arányát hirtelen kiugrások és ellenőrizze, hogy forgalmat jól elosztott partíciók között.

Az alkalmazás eléri a korlátot, mi partíció képes kezelni a számítási feladatok számára, amikor megkezdi az Azure Storage hibakód 503-as (foglalt kiszolgáló) vagy hibakód: 500 (művelet időtúllépése) választ adnak vissza. 503-as hiba fordul elő, ha fontolja meg az újrapróbálkozások az exponenciális visszatartással házirend használatához az alkalmazás módosítása. Az exponenciális visszatartással lehetővé teszi, hogy a terhelés csökkentéséhez és az adott partíció adatforgalmi kiugrások megkönnyítése érdekében a partíción.

## <a name="standard-storage-account-scale-limits"></a>Standard szintű storage-fiók méretkorlátai
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="storage-resource-provider-scale-limits"></a>Storage erőforrás-szolgáltató méretkorlátai 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Az Azure Blob storage méretezési célok
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok
Az Azure Files és az Azure File Sync a méretezéshez és teljesítményhez célokon további információkért lásd: [Azure Files méretezhetőségi és teljesítménycéljai](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync méretezési célok
Az Azure File Sync a cél az korlátlan használat úgy lett kialakítva, de a korlátlan használat értéke nem mindig lehetséges. Az alábbi táblázat azt jelzi, hogy a Microsoft-tesztelés határain és is jelzi, hogy mely célok a szigorú korlátozásokat:

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

