---
title: "Szüneteltetése, folytatása, az Azure SQL Data Warehouse REST méretezést |} Microsoft Docs"
description: "Számítási teljesítményt az SQL Data Warehouse REST API-k segítségével kezelheti."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: kfile
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/13/2018
ms.author: barbkess
ms.openlocfilehash: cb5b6221a5fc1d02ed1d93d56fd3db4858923307
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
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
Más felügyeleti feladatokat [kezelése-áttekintés](./sql-data-warehouse-overview-manage.md).

