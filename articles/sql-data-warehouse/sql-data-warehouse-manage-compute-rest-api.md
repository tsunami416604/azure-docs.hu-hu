---
title: Szüneteltetése, folytatása, az Azure SQL Data Warehouse REST méretezést |} Microsoft Docs
description: Számítási teljesítményt az SQL Data Warehouse REST API-k segítségével kezelheti.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 21423d69bf2cf06bcd208082ce492bf5dd038e29
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790661"
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

