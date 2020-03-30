---
title: Az Azure Storage-elemzés segítségével naplók és metrikák adatait gyűjtheti | Microsoft dokumentumok
description: A Storage Analytics lehetővé teszi az összes tárolási szolgáltatás metrikaadatainak nyomon követését, valamint a Blob, a Queue és a Table storage naplóinak gyűjtését.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 86b399879807e480176ee9a3ca3feaba1ec5dd85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250885"
---
# <a name="storage-analytics"></a>Storage Analytics

Az Azure Storage Analytics naplózást hajt végre, és metrikaadatokat biztosít egy tárfiókhoz. Ezeket az adatokat a kérelmek nyomon követésére, a használati trendek elemzésére és a tárfiókkal kapcsolatos problémák diagnosztizálására használhatja.

A Storage Analytics használatához külön-külön kell engedélyeznie minden egyes figyelni kívánt szolgáltatáshoz. Az [Azure Portalon](https://portal.azure.com)engedélyezheti. További információt a [Tárfiók figyelése az Azure Portalon című témakörben talál.](storage-monitor-storage-account.md) A Storage Analytics programozott módon is engedélyezhető a REST API-n vagy az ügyfélkódtáron keresztül. Használja a [Blob szolgáltatás tulajdonságainak beállítása](/rest/api/storageservices/set-blob-service-properties), A [várólista-szolgáltatás tulajdonságainak beállítása](/rest/api/storageservices/set-queue-service-properties), a [Táblaszolgáltatás tulajdonságainak beállítása](/rest/api/storageservices/set-table-service-properties)és a [Fájlszolgáltatás tulajdonságainak beállítása](/rest/api/storageservices/Get-File-Service-Properties) műveleteket az egyes szolgáltatások storage-elemzésének engedélyezéséhez.

Az összesített adatok egy jól ismert blobban (naplózás) és jól ismert táblákban (metrikák) tárolódnak, amelyek a Blob szolgáltatás és a Table service API-k használatával érhetők el.

A Storage Analytics 20 TB-os korláttal rendelkezik a tárfiók teljes korlátáttól független tárolt adatok mennyiségére vonatkozóan. A tárfiókok korlátairól a [Méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz](scalability-targets-standard-account.md)című témakörben talál további információt.

A Storage Analytics és más eszközök használatával az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és elhárítására vonatkozó részletes útmutatót a [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md)című témakörben talál.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="billing-for-storage-analytics"></a>A storage-elemzés számlázása

Minden metrika adatok at egy tárfiók szolgáltatásai által írt. Ennek eredményeképpen a Storage Analytics által végrehajtott minden egyes írási művelet számlázható. Emellett a metrikák adatai által használt tárterület mennyisége is számlázható.

A Storage Analytics által végrehajtott alábbi műveletek számlázhatók:

* Blobok naplózáshoz való létrehozására irányuló kérelmek.
* A metrikák táblaentitások létrehozására vonatkozó kérések.

Ha adatmegőrzési szabályzatot konfigurált, nem kell fizetnie a törlési tranzakciókért, amikor a Storage Analytics törli a régi naplózási és metrikák adatait. Az ügyféltől származó tranzakciók törlése azonban számlázható. Az adatmegőrzési szabályzatokról a [Storage Analytics adatmegőrzési szabályzatának beállítása](https://msdn.microsoft.com/library/azure/hh343263.aspx)című témakörben talál további információt.

### <a name="understanding-billable-requests"></a>A számlázható kérelmek ismertetése

A fiók tárolási szolgáltatásához intézett minden kérés számlázható vagy nem számlázható. A Storage Analytics naplózza a szolgáltatáshoz intézett minden egyes kérést, beleértve egy állapotüzenetet is, amely jelzi a kérelem kezelésének módját. Hasonlóképpen a Storage Analytics a szolgáltatás és a szolgáltatás API-műveleteinek mutatóit is tárolja, beleértve bizonyos állapotüzenetek százalékos arányát és számát. Ezek a funkciók együttesen segíthetnek a számlázható kérelmek elemzésében, az alkalmazás továbbfejlesztésében és a szolgáltatásokkérésekkel kapcsolatos problémák diagnosztizálásában. A számlázással kapcsolatos további információkért [lásd: Az Azure Storage-számlázás – Sávszélesség, tranzakciók és kapacitás ismertetése.](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)

A Storage Analytics-adatok megtekintésekor a [Storage Analytics naplózott műveletek és állapotüzenetek témakörtábláival](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) meghatározhatja, hogy mely kérelmek számlázhatók. Ezután összehasonlíthatja a naplók és a metrikák adatait az állapotüzenetekkel, hogy lássa, hogy egy adott kérésért díjat kellett-e fizetnie. Az előző témakörben található táblák segítségével is megvizsgálhatja egy tárolási szolgáltatás vagy egyéni API-művelet rendelkezésre állását.

## <a name="next-steps"></a>További lépések
* [Tárfiók monitorozása az Azure Portalon](storage-monitor-storage-account.md)
* [Storage Analytics mérőszámok](storage-analytics-metrics.md)
* [Storage Analytics-naplózás](storage-analytics-logging.md)
