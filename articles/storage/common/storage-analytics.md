---
title: Azure Storage analytics naplók és mérőszámok adatok összegyűjtéséhez használja |} A Microsoft Docs
description: A Storage Analytics lehetővé teszi, hogy nyomon követheti a metrikák adatait a storage-szolgáltatások, és a Blob, Queue és Table storage naplóinak összegyűjtése.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: eb85f8c756e7373a8dedabbce362cfa534e56fd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849176"
---
# <a name="storage-analytics"></a>Storage Analytics

A Storage szolgáltatás analitikai funkciói naplózást végeznek, valamint elérhetővé teszik a tárfiókok mérőszámadatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat.

A Storage Analytics használatához minden egyes figyelni kívánt szolgáltatás külön-külön kell engedélyeznie. Ön is engedélyezheti a [az Azure portal](https://portal.azure.com). További információkért lásd: [monitorozása az Azure Portal tárfiók](storage-monitor-storage-account.md). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. Használja a [állítsa be a Blobszolgáltatás tulajdonságain](/rest/api/storageservices/set-blob-service-properties), [Queue szolgáltatás tulajdonságainak beállítása](/rest/api/storageservices/set-queue-service-properties), [Table Service tulajdonságainak beállítása](/rest/api/storageservices/set-table-service-properties), és [szolgáltatás tulajdonságainak](/rest/api/storageservices/Get-File-Service-Properties)minden egyes szolgáltatás a Storage Analytics engedélyezésének műveletei.

Az összesített adatokat tárolja egy jól ismert blob (naplózás) és a jól ismert táblák (a metrikákat), amely a Blob és Table szolgáltatások API-k használatával is elérhetők.

A Storage Analytics esetében a 20 TB-ig, amely független a tárfiók teljes maximális tárolt adatok mennyisége. Információ a tárfiókok korlátairól további információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md).

A Storage Analytics és más eszközök használatával azonosítsa, diagnosztizálása és hibaelhárítása az Azure Storage szolgáltatással kapcsolatos problémák a részletes útmutatót lásd: [figyelése, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>A Storage Analytics díjszabása

A tárfiók szolgáltatások összes mérőszámadatokat írja. Ennek eredményeképpen minden egyes írási műveletnél a Storage Analytics által végrehajtott számlázható. Ezenkívül egyben számlázható mérőszámadatokat által felhasznált tárterület mennyisége.

A következő műveleteket végzi a Storage Analytics számlázhatók:

* Kérés létrehozása a blobok a naplózáshoz.
* Kérés létrehozása metrikákhoz táblaentitások.

Ha az adatmegőrzési házirend van beállítva, nem díjkötelesek törlési tranzakció során a Storage Analytics törli a régi naplózás és mérőszámok adatait. Azonban a törlési tranzakció egy ügyfél olyan számlázható. Adatmegőrzési házirendek kapcsolatos további információkért lásd: [Storage Analytics adatmegőrzési házirend beállítása](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Számlázható kérelmének ismertetése

Minden fiók storage szolgáltatás felé irányuló kérések, a számlázható, vagy a nem számlázható. A Storage Analytics naplók egy szolgáltatáshoz, köztük egy állapotüzenetet, amely azt jelzi, hogy a kérelem kezelése történt meg minden egyes kérelem. Ehhez hasonlóan a Storage Analytics tárolja a metrikákat egy szolgáltatás és a szolgáltatáshoz, beleértve a százalékok és bizonyos állapotüzenetek száma az API műveleteit. Együtt ezek a funkciók segítségével elemezheti a számlázható kérések, az alkalmazás továbbfejlesztése és diagnosztizálhatja problémáit a szolgáltatásokra irányuló kérések. Számlázással kapcsolatos további információkért lásd: [ismertetése az Azure Storage Billing – sávszélesség, tranzakciók és a kapacitás](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ha megnézzük a Storage Analytics-adatait, használhatja a táblák a [Storage Analytics naplózott műveletek és az állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) témakör annak meghatározására, hogy mely kérelmek számlázható. Ezután összehasonlíthatja a naplókat és az állapotüzenetek megtekintéséhez, ha meg lett díj felszámítva az egy adott kérés. Használhatja a táblák az előző témakörben tárolási szolgáltatás vagy egyéni API-művelet rendelkezésre állási vizsgálatára.

## <a name="next-steps"></a>További lépések
* [Az Azure Portal tárfiók figyelése](storage-monitor-storage-account.md)
* [Storage Analytics Metrics](storage-analytics-metrics.md)
* [A Storage Analytics naplózási](storage-analytics-logging.md)
