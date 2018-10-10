---
title: Az Azure Data Factory társított szolgáltatásokat paraméterezni |} A Microsoft Docs
description: Ismerje meg, hogyan paraméterezni az Azure Data Factory társított szolgáltatásokat, és a dinamikus értékeket adja át a futási időben.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 287dcdedede5cab575aa0b9a73ec3e122556dc93
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900723"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Az Azure Data Factory társított szolgáltatásokat paraméterezése

Mostantól paraméterezni egy társított szolgáltatást, és a dinamikus értékeket adja át a futási időben. Például szeretne csatlakozni a különböző adatbázisok Azure SQL Database ugyanarra a kiszolgálóra, ha akkor is most parametrizálja az adatbázis nevét, a társított szolgáltatás definíciójában. Ez megakadályozza, hogy meg kellene az egyes adatbázisokhoz társított szolgáltatás létrehozása az Azure SQL-kiszolgálón. Egyéb tulajdonságokat is – a társított szolgáltatás definíciójában például lehet paraméterezni *felhasználónevet.*

A Data Factory felhasználói felületének az Azure Portal vagy egy alkalmazásprogramozási felületet használhatja a társított szolgáltatások paraméterezni.

> [!TIP]
> Jelszó vagy titkos kulcsok paraméterezni, nem ajánlott. Az összes kapcsolati karakterláncok inkább Store az Azure Key Vaultban, és paraméterezni a *titkos kód nevét*.

## <a name="supported-data-stores"></a>Támogatott adattárak

Jelenleg társított szolgáltatás paraméterezés a Data Factory felhasználói felülete az Azure Portalon a következő adattárakat támogatja. Minden más adattárakban kiválasztásával lehet paraméterezni a társított szolgáltatás a **kód** ikonra a folyamat fülre, és a JSON-szerkesztő használatával.
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
