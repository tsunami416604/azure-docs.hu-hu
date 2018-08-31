---
title: Szüneteltetése, folytatása, méretezése az Azure SQL Data Warehouse REST-tel |} A Microsoft Docs
description: Az SQL Data Warehouse – REST API-kon keresztül a számítási teljesítmény kezelése.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 8db4d5cb69b65e60cd77d85d743798168bc6d813
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300833"
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


## <a name="next-steps"></a>További lépések
További információkért lásd: [számítások kezelése](sql-data-warehouse-manage-compute-overview.md).

