---
title: 'Rövid útmutató: Tömeges adatbetöltés egyetlen T-SQL utasítás használatával'
description: Tömeges betöltési adatok a COPY utasítás használatával
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d03c7d72a0adf02959badac758f94e47fd81de5c
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992227"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Rövid útmutató: Tömeges betöltési adatok a COPY utasítás használatával

Ebben a rövid útmutatóban tömegesen töltheti be az adatokat az SQL-készletbe az egyszerű és rugalmas [COPY utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) használatával a nagy átviteli sebességű adatbetöltéshez. A COPY utasítás az ajánlott betöltési segédprogram, mivel lehetővé teszi az adatok zökkenőmentes és rugalmas betöltését azáltal, hogy a következő funkciókat biztosítja:

- Alacsonyabb jogosultsággal rendelkező felhasználók betöltésének engedélyezése az adattárház szigorú CONTROL engedélyei nélkül
- Csak egyetlen T-SQL utasítást használhat anélkül, hogy további adatbázis-objektumokat kellene létrehoznia
- Finomabb engedélymodell használata a tárfiók kulcsainak megosztási hozzáférési aláírások (SAS) használatával történő felfedése nélkül
- Másik tárolófiók megadása az ERRORFILE helyhez (REJECTED_ROW_LOCATION)
- Az egyes céloszlopok alapértelmezett értékeinek testreszabása és adott céloszlopokba betöltendő forrásadatmezők megadása
- Egyéni sorterminátor megadása CSV-fájlokhoz
- Escape-karakterlánc, mező- és sorhatárolók CSV-fájlokhoz
- Az SQL Server dátumformátumainak kihasználása CSV-fájlokhoz
- Helyettesítő karakterek és több fájl megadása a tárolási hely elérési útján

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy SQL-készlet. Ha még nem jött létre egy SQL-készlet, használja a [Létrehozás és a Csatlakozás – portál](create-data-warehouse-portal.md) rövid útmutatóját.

## <a name="create-the-target-table"></a>A céltábla létrehozása

Ebben a példában a New York-i taxi adatkészletből származó adatokat fogunk betölteni. Betöltünk egy Trip nevű táblázatot, amely egy éven belül megtett taxiutakat jelent. A tábla létrehozásához futtassa az alábbi futtatást:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>A COPY utasítás futtatása

Futtassa a következő COPY utasítást, amely betölti az adatokat az Azure blob storage-fiókból az Utazás táblába.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>A terhelés figyelése

Ellenőrizze, hogy a terhelés folyamatban van-e a következő lekérdezés rendszeres futtatásával:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>További lépések

- Az adatok betöltésével kapcsolatos gyakorlati tanácsok az [adatok betöltésének ajánlott módszerei](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data)című témakörben vannak.
- Az adatterhelések erőforrásainak kezeléséről a [Számítási feladatok elkülönítése című](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql)témakörben talál további információt. 
