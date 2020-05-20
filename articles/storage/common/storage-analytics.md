---
title: Naplók és metrikák adatainak összegyűjtése az Azure Storage Analytics használatával | Microsoft Docs
description: Storage Analytics lehetővé teszi az összes tárolási szolgáltatás metrikáinak nyomon követését, valamint a blob-, üzenetsor-és Table Storage-naplók gyűjtését.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 9a081a28d4c96e3c38986cbb3c0990bc89c5ab99
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684478"
---
# <a name="storage-analytics"></a>Storage Analytics

Azure Storage Analytics naplózást végez, és metrikai adatokat biztosít egy Storage-fiókhoz. Ezeket az adatokat felhasználhatja a kérelmek nyomon követéséhez, a használati trendek elemzéséhez és a Storage-fiókkal kapcsolatos problémák diagnosztizálásához.

Storage Analytics használatához külön kell engedélyeznie a figyelni kívánt szolgáltatásokhoz. Engedélyezheti a [Azure Portal](https://portal.azure.com). További információ: [Storage-fiók figyelése a Azure Portalban](storage-monitor-storage-account.md). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A [blob szolgáltatás tulajdonságainak](/rest/api/storageservices/set-blob-service-properties)beállítása, a [várólista-szolgáltatás tulajdonságainak](/rest/api/storageservices/set-queue-service-properties)beállítása, a [Table szolgáltatás tulajdonságainak beállítása](/rest/api/storageservices/set-table-service-properties)és a [Fájlszolgáltatások tulajdonságainak beállítása](/rest/api/storageservices/Get-File-Service-Properties) művelettel engedélyezheti a Storage Analytics az egyes szolgáltatásokhoz.

Az összesített adatokat egy jól ismert blobban (naplózáshoz) és jól ismert táblákban (a metrikák esetében) tárolják, amelyek a Blob service és az Table service API-k használatával érhetők el.

A Storage Analytics 20 TB-os korláttal rendelkezik a Storage-fiókhoz tartozó teljes korláttól független tárolt adat mennyiségétől függően. A Storage-fiók korlátaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi és teljesítménybeli céljait](scalability-targets-standard-account.md)ismertető témakört.

Az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és hibaelhárítására vonatkozó részletes útmutató a Storage Analytics és egyéb eszközök használatáról: [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Számlázás Storage Analytics

Az összes mérőszám adatait egy Storage-fiók szolgáltatásai írják le. Ennek eredményeképpen minden Storage Analytics által végrehajtott írási művelet számlázható. Emellett a metrikák adatai által használt tárterület mennyisége is számlázható.

A Storage Analytics által végrehajtott következő műveletek számlázandóek:

* Kérelmek a Blobok létrehozásához a naplózáshoz.
* A táblázat entitások metrikák létrehozásához szükséges kérelmek.

Ha az adatmegőrzési szabályzatot konfigurálta, akkor nem kell fizetnie a törlési tranzakcióért, ha Storage Analytics törli a régi naplózási és metrikái adatokat. Az ügyfél tranzakcióinak törlése azonban számlázható. További információ az adatmegőrzési házirendekről: [Storage Analytics adatmegőrzési szabályzat beállítása](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Számlázandó kérelmek ismertetése

A fiók tárolási szolgáltatására tett minden kérelem számlázható vagy nem számlázható. Storage Analytics naplózza a szolgáltatáshoz intézett egyes kéréseket, beleértve a kérés kezelésének módját jelző állapotüzenetek egyikét is. Hasonlóképpen, Storage Analytics a szolgáltatáshoz és az API-műveletekhez tartozó metrikákat is tárolja, beleértve az egyes állapotüzenetek százalékos arányát és számát is. Ezek a funkciók együttesen segíthetnek a számlázható kérelmek elemzésében, az alkalmazások tökéletesítésében és a szolgáltatásokkal kapcsolatos kérések diagnosztizálásában. További információ a számlázással kapcsolatban: az [Azure Storage számlázási szolgáltatásának ismertetése – sávszélesség, tranzakciók és kapacitás](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

Storage Analytics adatok megtekintésekor használhatja a [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) témakör táblázatait, amelyekkel meghatározhatja, hogy mely kérések legyenek számlázva. Ezután összehasonlíthatja a naplókat és a metrikákat az állapotüzenetek adataival, hogy megtudja, van-e terhelve egy adott kérelemért. Az előző témakör tábláival is megvizsgálhatja a tárolási szolgáltatás vagy az egyes API-műveletek rendelkezésre állását.

## <a name="next-steps"></a>További lépések
* [Tárfiók monitorozása az Azure Portalon](storage-monitor-storage-account.md)
* [Storage Analytics metrikák](storage-analytics-metrics.md)
* [Storage Analytics-naplózás](storage-analytics-logging.md)
