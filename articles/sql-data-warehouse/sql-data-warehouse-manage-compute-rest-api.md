---
title: Szüneteltetés, folytatás, méretezés REST API-kkal
description: A számítási teljesítmény kezelése az Azure szinapszis Analytics-adattárházban REST API-kon keresztül.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4e3435fdaa505a73abf96b9463b061c623c192ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199393"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST API-k Azure SQL Data Warehousehoz
REST API-k a számítások kezeléséhez az Azure szinapszis Analytics-adattárházban.

## <a name="scale-compute"></a>Számítások méretezése
Az adatraktár-egységek módosításához használja az [adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) REST API. A következő példa beállítja az adatraktár-egységeket a DW1000 adatbázis-MySQLDW, amely a kiszolgáló MyServer található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Számítás szüneteltetése

Egy adatbázis szüneteltetéséhez használja az [adatbázis szüneteltetése](/rest/api/sql/databases/pause) REST API. Az alábbi példa egy Database02 nevű adatbázist szüneteltet egy Kiszolgalo01 nevű kiszolgálón. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Számítás folytatása

Az adatbázis elindításához használja az [adatbázis folytatása](/rest/api/sql/databases/resume) REST API. A következő példa egy Database02 nevű adatbázist indít el egy Kiszolgalo01 nevű kiszolgálón. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Adatbázis állapotának keresése

> [!NOTE]
> A jelenleg ellenőrzött adatbázis állapota ONLINE állapotba kerülhet, miközben az adatbázis befejezi az online munkafolyamatot, és kapcsolódási hibákat eredményez. Ha ezt az API-hívást használja a kapcsolódási kísérletek elindításához, előfordulhat, hogy az alkalmazás kódjában egy 2-3 perces késleltetést kell hozzáadnia.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Karbantartási ütemterv beolvasása
Keresse meg az adatraktárhoz beállított karbantartási ütemtervet. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Karbantartási ütemterv beállítása
Karbantartási ütemterv beállítása és frissítése egy meglévő adatraktáron.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>További lépések
További információ: a [számítások kezelése](sql-data-warehouse-manage-compute-overview.md).

