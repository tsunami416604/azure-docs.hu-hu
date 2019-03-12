---
title: Az Azure Data Factory társított szolgáltatásokat paraméterezni |} A Microsoft Docs
description: Ismerje meg, hogyan paraméterezni az Azure Data Factory társított szolgáltatásokat, és a dinamikus értékeket adja át a futási időben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 0239c53f98fba201b6d70e1e2212eea36134e30d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574627"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Az Azure Data Factory társított szolgáltatásokat paraméterezése

Mostantól paraméterezni egy társított szolgáltatást, és a dinamikus értékeket adja át a futási időben. Például szeretne csatlakozni a különböző adatbázisok Azure SQL Database ugyanarra a kiszolgálóra, ha akkor is most parametrizálja az adatbázis nevét, a társított szolgáltatás definíciójában. Ez megakadályozza, hogy meg kellene az egyes adatbázisokhoz társított szolgáltatás létrehozása az Azure SQL-kiszolgálón. Egyéb tulajdonságokat is – a társított szolgáltatás definíciójában például lehet paraméterezni *felhasználónevet.*

A Data Factory felhasználói felületének az Azure Portal vagy egy alkalmazásprogramozási felületet használhatja a társított szolgáltatások paraméterezni.

> [!TIP]
> Jelszó vagy titkos kulcsok paraméterezni, nem ajánlott. Az összes kapcsolati karakterláncok inkább Store az Azure Key Vaultban, és paraméterezni a *titkos kód nevét*.

A 7 perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Támogatott adattárak

Jelenleg társított szolgáltatás paraméterezés a Data Factory felhasználói felülete az Azure Portalon a következő adattárakat támogatja. Minden más adattárakban kiválasztásával lehet paraméterezni a társított szolgáltatás a **kód** ikonra a **kapcsolatok** lapra, és a JSON-szerkesztő használatával.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

![Dinamikus tartalom hozzáadása a társított szolgáltatás definíciójában](media/parameterize-linked-services/parameterize-linked-services-image1.png)

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
