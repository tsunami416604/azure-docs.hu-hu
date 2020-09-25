---
title: Parametrizálja társított szolgáltatások Azure Data Factory
description: Megtudhatja, hogyan parametrizálja a társított szolgáltatásokat a Azure Data Factoryban, és hogyan adhat át dinamikus értékeket a futási időben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 081d19cc845750f1392e2c1a14229a51d0df4cbc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276455"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizálja társított szolgáltatások Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mostantól parametrizálja egy társított szolgáltatást, és átadhatja a dinamikus értékeket a futási időben. Ha például ugyanahhoz a logikai SQL-kiszolgálóhoz szeretne csatlakozni különböző adatbázisokhoz, most már parametrizálja az adatbázis nevét a társított szolgáltatás definíciójában. Ez megakadályozza, hogy a logikai SQL-kiszolgálón lévő összes adatbázishoz hozzon létre egy társított szolgáltatást. A társított szolgáltatás definíciójában más tulajdonságokat is parametrizálja, például a *felhasználónevet.*

A Data Factory felhasználói felületét a Azure Portal vagy egy programozási felületen is használhatja a társított szolgáltatások parametrizálja.

> [!TIP]
> Azt javasoljuk, hogy ne parametrizálja jelszavakat vagy titkos kódokat. A Azure Key Vault az összes kapcsolódó karakterláncot tárolja, és parametrizálja a *titkos nevet*.

A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Támogatott adattárak

Bármilyen típusú társított szolgáltatást parametrizálja.
A társított szolgáltatás felhasználói felületen való létrehozásakor Data Factory a következő típusú összekötők számára biztosít beépített paraméterezés élményt. A társított szolgáltatás létrehozása/szerkesztése panelen megtalálhatja az új paraméterek beállításait, és hozzáadhat dinamikus tartalmakat is.

- Amazon Redshift
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure SQL Database
- Azure Synapse Analytics (korábban SQL DW)
- MySQL
- Oracle
- SQL Server
- Általános HTTP
- Általános REST

Más típusok esetén a parametrizálja a JSON szerkesztésével a felhasználói felületen:

- A társított szolgáltatás létrehozása/szerkesztése panelen – > bontsa ki a "Advanced" (speciális) lehetőséget az alul-> jelölje be a "dinamikus tartalom JSON formátumban" jelölőnégyzetet – > határozza meg a társított szolgáltatás JSON-adattartalmát. 
- Vagy a társított szolgáltatás paraméterezés nélküli létrehozása után a [felügyeleti központ](author-visually.md#management-hub) – > társított szolgáltatások – > keresse meg az adott társított szolgáltatást – > kattintson a "code" (gomb " {} ") gombra a JSON szerkesztéséhez. 

A [JSON sample](#json) ` parameters` Paraméterek definiálásához és a (z) paraméterrel való hivatkozásához tekintse meg a JSON-mintát a Hozzáadás szakaszhoz ` @{linkedService().paraName} ` .

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
