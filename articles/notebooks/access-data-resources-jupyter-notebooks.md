---
title: Jupyter-jegyzetfüzetekben tárolt adathozzáférések – Azure Notebooks előzetes verzió
description: Megtudhatja, hogyan érheti el a fájlokat, a REST API-kat, az adatbázisokat és a különböző Azure Storage-erőforrásokat egy Jupyter-jegyzetfüzetből.
ms.topic: how-to
ms.date: 12/04/2018
ms.custom: tracking-python
ms.openlocfilehash: a7e242c000b57cbbfafebc1e11191199a7a4b95f
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85319135"
---
# <a name="access-cloud-data-in-a-notebook"></a>Felhőbeli adatok elérése egy jegyzetfüzetben

A Jupyter-jegyzetfüzetben érdekes munkát kell végezni. Az adat valóban a jegyzetfüzetek éltető eleme.

Az [adatfájlok természetesen egy projektbe is importálhatók](work-with-project-data-files.md), akár `curl` egy jegyzetfüzetből származó parancsok használatával is, közvetlenül a fájl letöltéséhez. Azonban valószínű, hogy a nem fájlokból, például a REST API-kkal, a rokon adatbázisokkal és a Felhőbeli tárolással, például az Azure Tables szolgáltatással elérhető, sokkal átfogóbb adatokkal kell dolgoznia.

Ez a cikk röviden ismerteti ezeket a különböző lehetőségeket. Mivel az adathozzáférés a legjobb működésben látható, futtatható kódot talál a [Azure Notebooks-mintákban – hozzáférhet az adataihoz](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>REST API-k

Általánosságban elmondható, hogy az internetről elérhető nagy mennyiségű adatok nem a fájlokon, hanem a REST API-kon keresztül érhetők el. Szerencsére, mivel egy jegyzetfüzet-cella bármilyen kódot tartalmazhat, a kérelmek küldéséhez és a JSON-adat fogadásához kód is használható. Ezután átalakíthatja a JSON-t a használni kívánt formátumba, például egy Panda dataframe is.

Ha REST API használatával kívánja elérni az adatelérést, használja ugyanazt a kódot egy jegyzetfüzet kódjában, amelyet bármely más alkalmazásban használ. A kérelmeket tartalmazó függvénytárat használó általános struktúra a következő:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database és az SQL felügyelt példánya

A pyodbc-vagy pymssql-kódtárak segítségével elérheti SQL Database vagy SQL felügyelt példányban található adatbázisokat.

A [Python használata Azure SQL Database-adatbázis lekérdezéséhez](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) útmutatást nyújt az adatbázisok létrehozásához a AdventureWorks-t tartalmazó SQL Databaseban, és bemutatja, hogyan lehet lekérdezni ezeket az információkat. Ugyanez a kód jelenik meg a minta jegyzetfüzetben ehhez a cikkhez.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage számos különböző típust biztosít a nem kapcsolódó tároláshoz, az Ön által használt adattípustól és annak eléréséhez szükségestől függően:

- Table Storage: alacsony díjszabású, nagy mennyiségű tárolást biztosít a táblázatos adatokhoz, például az érzékelők naplóihoz, a diagnosztikai naplókhoz és így tovább.
- BLOB Storage: bármilyen típusú adattípushoz biztosít fájl-szerű tárolást.

A minta notebook a táblázatok és a Blobok használatát mutatja be, beleértve a közös hozzáférésű aláírás használatát is, hogy csak olvasási hozzáférést engedélyezzen a blobokhoz.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

A Azure Cosmos DB egy teljes körűen indexelt NoSQL-tárolót biztosít a JSON-dokumentumokhoz). A következő cikkek számos különböző módszert biztosítanak a Python-Cosmos DB való együttműködéshez:

- [SQL API-alkalmazás létrehozása a Python használatával](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [MongoDB-hez készült Azure Cosmos DB API-val rendelkező lombik-alkalmazás létrehozása](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Graph-adatbázis létrehozása a Python és a Gremlin API használatával](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Cassandra-alkalmazás létrehozása a Python és a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Table API-alkalmazás létrehozása a Python és a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Cosmos DB használatakor használhatja az [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) könyvtárat.

## <a name="other-azure-databases"></a>Egyéb Azure-adatbázisok

Az Azure számos más adatbázis-típust biztosít, amelyeket használhat. Az alábbi cikkek útmutatást nyújtanak az adatbázisok Pythonból való eléréséhez:

- [A PostgreSQL-hez készült Azure Database: Csatlakozás és adatlekérdezés a Python használatával](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Rövid útmutató: Az Azure Redis Cache használata Pythonnal](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [A MySQL-hez készült Azure-adatbázis: Csatlakozás és adatlekérdezés a Python használatával](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Azure Data Factory másolási varázslója](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>További lépések

- [Útmutató: Project-adatfájlok használata](work-with-project-data-files.md)
