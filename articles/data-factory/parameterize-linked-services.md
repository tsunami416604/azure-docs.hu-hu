---
title: Csatolt szolgáltatások paraméterezése az Azure Data Factoryban
description: Ismerje meg, hogyan paraméterezhet össze csatolt szolgáltatásokat az Azure Data Factoryban, és hogyan adhatja át a dinamikus értékeket futásidőben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: acc7284eb607d20ca1d62b478d802be56048bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440096"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Csatolt szolgáltatások paraméterezése az Azure Data Factoryban

Most már paraméterezhet egy összekapcsolt szolgáltatást, és futásidőben dinamikus értékeket adhat át. Ha például ugyanazon az Azure SQL Database-kiszolgálón lévő különböző adatbázisokhoz szeretne csatlakozni, most már paraméterezheti az adatbázis nevét a csatolt szolgáltatásdefinícióban. Ez megakadályozza, hogy az Azure SQL-adatbázis-kiszolgálón minden egyes adatbázishoz hozzon létre egy csatolt szolgáltatást. A csatolt szolgáltatásdefinícióban más tulajdonságokat is paraméterezhet – például *felhasználónév.*

Használhatja a Data Factory felhasználói felületaz Azure Portalon vagy egy programozási felület paraméterezése csatolt szolgáltatások.

> [!TIP]
> Azt javasoljuk, hogy ne paraméterezje a jelszavakat és a titkos kulcsokat. Az összes kapcsolati karakterláncot az Azure Key Vaultban tárolja, és paraméterezi a *titkos nevet.*

A funkció hétperces bemutatásához és bemutatásához tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Támogatott adattárak

Jelenleg a csatolt szolgáltatás paraméterezése az Azure Portalon található Data Factory felhasználói felülete a következő adattárakban támogatott. Az összes többi adattárak esetében paraméterezheti a csatolt szolgáltatást, ha a Kapcsolatok lapon a **Kód** ikont **választja,** és használja a JSON-szerkesztőt.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

![Dinamikus tartalom hozzáadása a csatolt szolgáltatás definíciójához](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Új paraméter létrehozása](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
