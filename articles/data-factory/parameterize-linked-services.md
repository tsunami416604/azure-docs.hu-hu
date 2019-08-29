---
title: Parametrizálja társított szolgáltatások a Azure Data Factoryban | Microsoft Docs
description: Megtudhatja, hogyan parametrizálja a társított szolgáltatásokat a Azure Data Factoryban, és hogyan adhat át dinamikus értékeket a futási időben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 285b7c182fc218a590b7a3980e43175c76555106
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140953"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizálja társított szolgáltatások Azure Data Factory

Mostantól parametrizálja egy társított szolgáltatást, és átadhatja a dinamikus értékeket a futási időben. Ha például ugyanazon a Azure SQL Database-kiszolgálón lévő különböző adatbázisokhoz szeretne csatlakozni, most már parametrizálja az adatbázis nevét a társított szolgáltatás definíciójában. Ezzel megakadályozható, hogy az Azure SQL Database-kiszolgálón minden adatbázishoz hozzon létre egy társított szolgáltatást. A társított szolgáltatás definíciójában más tulajdonságokat is parametrizálja, például a *felhasználónevet.*

A társított szolgáltatások parametrizálja használhatja az Azure Portalon a Data Factory felhasználói felületet vagy egy programozási felületet.

> [!TIP]
> Azt javasoljuk, hogy ne parametrizálja jelszavakat vagy titkos kódokat. A Azure Key Vault az összes kapcsolódó karakterláncot tárolja, és parametrizálja a *titkos nevet*.

A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Támogatott adattárak

A társított szolgáltatás paraméterezés jelenleg a Azure Portal Data Factory felhasználói felületén, a következő adattárakban támogatott. Minden más adattár esetében a társított szolgáltatás parametrizálja a **kapcsolatok** lapon, a JSON-szerkesztővel pedig a **kód** ikonra kattintva.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

![Dinamikus tartalom hozzáadása a társított szolgáltatás definíciója](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Új paraméter létrehozása](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
