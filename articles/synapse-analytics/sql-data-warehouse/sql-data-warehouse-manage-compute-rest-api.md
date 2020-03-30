---
title: Szünet, folytatás, méretezés REST API-kkal
description: Kezelje a számítási teljesítményt az Azure Synapse Analytics adattárházában a REST API-kon keresztül.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 322f1dfcb709727ddd3a97ea22dbe8243aedca20
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350353"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>REST API-k az Azure SQL Data Warehouse-hoz
REST API-k az Azure Synapse Analytics adattárában a számítási feladatok kezeléséhez.

## <a name="scale-compute"></a>Számítások méretezése
Az adatraktári egységek módosításához használja az Adatbázis REST [létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) API-t. A következő példa az adattárház egységekdw1000-re állítja a MySQLDW adatbázist, amely a MyServer kiszolgálón található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoportban található.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>A számítás szüneteltetése

Adatbázis szüneteltetéséhez használja az Adatbázis REST-ének [szüneteltetése](/rest/api/sql/databases/pause) API-t. A következő példa egy Server01 nevű kiszolgálón található Database02 nevű adatbázist szüneteltet. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoportban található.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Számítás folytatása

Adatbázis indításához használja a [Folytatás adatbázis](/rest/api/sql/databases/resume) REST API-t. A következő példa egy Database02 nevű adatbázist indít el egy Server01 nevű kiszolgálón. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoportban található. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Adatbázis állapotának ellenőrzése

> [!NOTE]
> Jelenleg Előfordulhat, hogy az adatbázis állapota az online állapotot adja vissza, miközben az adatbázis befejezi az online munkafolyamatot, ami csatlakozási hibákat eredményez. Előfordulhat, hogy 2–3 perces késleltetést kell hozzáadnia az alkalmazáskódhoz, ha ezt az API-hívást használja a csatlakozási kísérletek elindításához.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Karbantartási ütemterv beszerezni
Ellenőrizze az adattárházhoz beállított karbantartási ütemezést. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Karbantartási ütemezés beállítása
Karbantartási ütemezés beállítása és frissítése meglévő adattárházon.

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
További információt a [Számítási feladatok kezelése című témakörben talál.](sql-data-warehouse-manage-compute-overview.md)

