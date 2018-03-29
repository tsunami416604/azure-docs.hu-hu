---
title: Szüneteltetése, folytatása, az Azure SQL Data Warehouse REST méretezést |} Microsoft Docs
description: Számítási teljesítményt az SQL Data Warehouse REST API-k segítségével kezelheti.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: kfile
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2018
ms.author: barbkess
ms.openlocfilehash: 518bbe23f1dcb9ffdffcfb67f875165617762c78
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse REST API-k
REST API-khoz, az Azure SQL Data Warehouse számítási kezelése.

## <a name="scale-compute"></a>Számítások méretezése
A adattárházegységek módosításához használja a [létrehozás vagy frissítés adatbázis](/rest/api/sql/databases/createorupdate) REST API-t. Az alábbi példa állítja be a adattárházegységek DW1000 az adatbázis MySQLDW, amely a MyServer kiszolgáló üzemelteti. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforrás-csoportban.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Felfüggesztés számítási

Egy adatbázis felfüggesztése, használja a [adatbázis felfüggesztése](/rest/api/sql/databases/pause) REST API-t. A következő példa egy kiszolgalo01 nevű kiszolgálón található Database02 nevű adatbázis felfüggesztése. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforrás-csoportban.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Folytatás számítási

Egy adatbázis indításához használja a [folytatása adatbázis](/rest/api/sql/databases/resume) REST API-t. A következő példában elindul kiszolgalo01 nevű kiszolgálón található Database02 nevű adatbázis. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforrás-csoportban. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Ellenőrizze az adatbázis állapota

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>További lépések
További információkért lásd: [számítási kezelése](sql-data-warehouse-manage-compute-overview.md).

