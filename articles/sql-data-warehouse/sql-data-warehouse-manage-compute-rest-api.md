---
title: Szüneteltetése, folytatása, méretezése az Azure SQL Data Warehouse REST-tel |} A Microsoft Docs
description: Az SQL Data Warehouse – REST API-kon keresztül a számítási teljesítmény kezelése.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 658b35163e20d024118bc7a3142c86614540f00c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790246"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Az Azure SQL Data warehouse-hoz, a REST API-k
REST API-k kezelése az Azure SQL Data Warehouse számítási.

## <a name="scale-compute"></a>Számítások méretezése
Az adattárházegységek módosításához használja a [létrehozás vagy frissítés adatbázis](/rest/api/sql/databases/createorupdate) REST API-t. A következő példa az adattárházegységek dw1000 értékre állítja az adatbázis MySQLDW, amely a MyServer kiszolgáló üzemel. A kiszolgáló egy Azure-erőforráscsoportot ResourceGroup1 szerepel.

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

Egy adatbázis szüneteltetése, használja a [adatbázis szüneteltetése](/rest/api/sql/databases/pause) REST API-t. Az alábbi példában egy adatbázist a kiszolgalo01 nevű kiszolgáló által üzemeltetett Database02 felfüggesztése. A kiszolgáló egy Azure-erőforráscsoportot ResourceGroup1 szerepel.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Számítási folytatása

Egy adatbázis indításához használja a [az adatbázis folytatása](/rest/api/sql/databases/resume) REST API-t. A következő példa elindítja a kiszolgalo01 nevű kiszolgáló által üzemeltetett Database02 nevű adatbázis. A kiszolgáló egy Azure-erőforráscsoportot ResourceGroup1 szerepel. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Adatbázis állapotának ellenőrzése

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Karbantartási ütemezés lekérése
Ellenőrizze a karbantartási ütemezés, amely egy data warehouse-hoz van beállítva. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Karbantartási ütemezés beállítása
Állítsa be, és a egy maintnenance ütemezés egy meglévő data warehouse frissítése.

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
További információkért lásd: [számítások kezelése](sql-data-warehouse-manage-compute-overview.md).

