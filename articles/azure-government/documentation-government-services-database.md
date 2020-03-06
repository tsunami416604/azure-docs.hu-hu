---
title: Adatbázisok Azure Governmenta | Microsoft Docs
description: Ez összehasonlítja a szolgáltatásokat és útmutatást a Azure Government alkalmazások fejlesztéséhez
services: azure-government
cloud: gov
documentationcenter: ''
author: zakramer
manager: liki
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 03/06/2019
ms.author: zakramer
ms.openlocfilehash: cbf3d59bafa5b11be0fa037487dc64fdb9075619
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355733"
---
# <a name="azure-government-databases"></a>Adatbázisok Azure Government
## <a name="sql-database"></a>SQL Database
További információkért tekintse meg a<a href="https://msdn.microsoft.com/library/bb510589.aspx"> Microsoft Security Center SQL Database Engine</a> és a [Azure SQL Database dokumentációját](../sql-database/index.yml) , ahol további útmutatást találhat a metaadatok láthatóságának konfigurálásával és a védelem bevált eljárásaival kapcsolatban.

### <a name="variations"></a>Változata
Az SQL V12-es adatbázis általánosan elérhető Azure Governmentban.

A Azure Government SQL Azure-kiszolgálók címe eltér:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| SQL Database |*. database.windows.net |*. database.usgovcloudapi.net |

### <a name="considerations"></a>Megfontolások
Az alábbi információk az Azure SQL Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
| Az Microsoft Azure SQL-ben tárolt és feldolgozott összes adathalmaz Azure Government-szabályozású adattal is rendelkezhet. Adatbázis-eszközök használata Azure Government által szabályozott adatok átviteléhez. |Az Azure SQL-metaadatok nem tartalmazhatnak export-vezérelt adatokat. Ez a metaadatok tartalmazzák a tárolási termék létrehozásakor és karbantartásakor megadott összes konfigurációs adatát.  Ne adja meg a szabályozott/vezérelt adatokat a következő mezőkben: adatbázis neve, előfizetés neve, Erőforráscsoport, kiszolgálónév, kiszolgáló-rendszergazdai bejelentkezés, központi telepítési nevek, erőforrásnevek, erőforrás-Címkék |

## <a name="sql-data-warehouse"></a>SQL Data Warehouse
A szolgáltatásról és használatáról a [Azure SQL Data Warehouse dokumentációjában](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)talál további információt.

## <a name="sql-server-stretch-database"></a>SQL Server Stretch Database
A szolgáltatás részleteiről és használatáról az [Azure SQL Server stretch Database dokumentációjában](../sql-server-stretch-database/index.md) talál további információt.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
A szolgáltatásról és használatáról a [Azure Cosmos db dokumentációjában](../cosmos-db/index.yml)talál további információt.

### <a name="variations"></a>Változata
A Azure Cosmos DB általánosan elérhető a nyilvános verzió paritásos Azure Government. Az egyik kivétel az **Azure Cognitive Search** -függvény hozzáadása, amely jelenleg nem érhető el a Azure Government Cosmos db.

Emellett a Azure Government Cosmos DB elérésének URL-címei eltérőek:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| Cosmos DB | *. documents.azure.com | *. documents.azure.us |


### <a name="considerations"></a>Megfontolások
A következő információk a Azure Cosmos DB Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
| A Azure Cosmos DB tárolt és feldolgozott összes adathalmaz Azure Government-szabályozású adattal is rendelkezhet. |Azure Cosmos DB metaadatok nem tartalmazhatnak exportálásra vezérelt adatokat. Ne adja meg a szabályozott/vezérelt adattípusokat a következő mezőkben: **adatbázis neve, előfizetés neve, Erőforráscsoport, erőforrás-címkék**. |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
A szolgáltatás részleteiről és használatáról az [Azure cache for Redis dokumentációjában](../azure-cache-for-redis/index.yml)talál további információt.

### <a name="variations"></a>Változata
A Azure Government Redis az Azure cache eléréséhez és kezeléséhez használt URL-címek eltérőek:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| Gyorsítótár végpontja |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |

> [!NOTE]
> Minden parancsfájlnak és kódnak a megfelelő végpontokhoz és környezetekhez kell kapcsolódnia. További információ: [Kapcsolódás más felhőkhöz](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).
>
>

### <a name="considerations"></a>Megfontolások
Az alábbi információk a Redis Azure cache Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
| Az Azure cache-ben tárolt és feldolgozott összes Redis Azure Government-szabályozású adattal rendelkezhet. |Az Redis-metaadatokhoz tartozó Azure cache nem tartalmazhat export-vezérelt adatokat. Ne adja meg a szabályozott/vezérelt adattípusokat a következő mezőkben: **gyorsítótár neve, előfizetés neve, Erőforráscsoport, erőforrás-címkék, Redis tulajdonságai**. |

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
A szolgáltatásról és használatáról a [Azure Database for PostgreSQL dokumentációjában](../postgresql/index.yml)talál további információt.

### <a name="variations"></a>Változata
A komplex veszélyforrások elleni védelem, a lekérdezési teljesítményre vonatkozó ajánlások és a Azure Database for PostgreSQL teljesítményére vonatkozó javaslatok **nem** érhetők el Azure Governmentokban.

A Azure Government Azure Database for PostgreSQL eléréséhez és kezeléséhez használt URL-címek különbözőek:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| PostgreSQL-végpont |*. postgres.database.azure.com |*. postgres.database.usgovcloudapi.net |

### <a name="considerations"></a>Megfontolások
A következő információk a Azure Database for PostgreSQL Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
| A Azure Database for PostgreSQL tárolt és feldolgozott összes adathalmaz Azure Government-szabályozású adattal is rendelkezhet. Adatbázis-eszközök használata Azure Government által szabályozott adatok átviteléhez. |Azure Database for PostgreSQL metaadatok nem tartalmazhatnak exportálásra vezérelt adatokat. Ez a metaadatok tartalmazzák a tárolási termék létrehozásakor és karbantartásakor megadott összes konfigurációs adatát.  Ne adja meg a szabályozott/vezérelt adatokat a következő mezőkben: adatbázis neve, előfizetés neve, Erőforráscsoport, kiszolgálónév, kiszolgáló-rendszergazdai bejelentkezés, központi telepítési nevek, erőforrásnevek, erőforrás-címkék. |

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
A szolgáltatásról és használatáról a [Azure Database for MariaDB dokumentációjában](../mariadb/index.yml)talál további információt.

### <a name="variations"></a>Változata
A Azure Database for MariaDB **nem** érhető el a lekérdezés teljesítményére vonatkozó és a teljesítménnyel kapcsolatos javaslatok a Azure Governmentban.

A Azure Government Azure Database for MariaDB eléréséhez és kezeléséhez használt URL-címek különbözőek:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| MariaDB végpont |*. mariadb.database.azure.com |*. mariadb.database.usgovcloudapi.net |

### <a name="considerations"></a>Megfontolások
A következő információk a Azure Database for MariaDB Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
| A Azure Database for MariaDB tárolt és feldolgozott összes adathalmaz Azure Government-szabályozású adattal is rendelkezhet. Adatbázis-eszközök használata Azure Government által szabályozott adatok átviteléhez. |Azure Database for MariaDB metaadatok nem tartalmazhatnak exportálásra vezérelt adatokat. Ez a metaadatok tartalmazzák a tárolási termék létrehozásakor és karbantartásakor megadott összes konfigurációs adatát.  Ne adja meg a szabályozott/vezérelt adatokat a következő mezőkben: adatbázis neve, előfizetés neve, Erőforráscsoport, kiszolgálónév, kiszolgáló-rendszergazdai bejelentkezés, központi telepítési nevek, erőforrásnevek, erőforrás-címkék. |

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
A szolgáltatásról és használatáról a [Azure Database for MySQL dokumentációjában](../mysql/index.yml)talál további információt.

### <a name="variations"></a>Változata
A komplex veszélyforrások elleni védelem, a lekérdezési teljesítményre vonatkozó ajánlások és a Azure Database for MySQL teljesítményére vonatkozó javaslatok **nem** érhetők el Azure Governmentokban.

A Azure Government Azure Database for MySQL eléréséhez és kezeléséhez használt URL-címek különbözőek:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| MySQL-végpont |*. mysql.database.azure.com |*. mysql.database.usgovcloudapi.net |

### <a name="considerations"></a>Megfontolások
A következő információk a Azure Database for MySQL Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
| A Azure Database for MySQL tárolt és feldolgozott összes adathalmaz Azure Government-szabályozású adattal is rendelkezhet. Adatbázis-eszközök használata Azure Government által szabályozott adatok átviteléhez. |Azure Database for MySQL metaadatok nem tartalmazhatnak exportálásra vezérelt adatokat. Ez a metaadatok tartalmazzák a tárolási termék létrehozásakor és karbantartásakor megadott összes konfigurációs adatát.  Ne adja meg a szabályozott/vezérelt adatokat a következő mezőkben: adatbázis neve, előfizetés neve, Erőforráscsoport, kiszolgálónév, kiszolgáló-rendszergazdai bejelentkezés, központi telepítési nevek, erőforrásnevek, erőforrás-címkék. |

## <a name="next-steps"></a>Következő lépések
Kiegészítő információk és frissítések előfizethetnek a <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government blogra.</a>
