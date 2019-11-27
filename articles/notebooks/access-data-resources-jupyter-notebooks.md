---
title: Az Azure-ban a Jupyter notebookokból származó adatok erőforrások eléréséhez
description: Hogyan lehet a Jupyter notebook fájlok, a REST API-k, az adatbázisok és a különböző Azure Storage-erőforrások eléréséhez.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 23acdf99f6cb69f100e484e236580f3b2b43ba94
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277695"
---
# <a name="access-cloud-data-in-a-notebook"></a>Hozzáférés felhőbeli adatok történő használatát

A Jupyter notebook érdekes munkát végző szükséges adatokat. Adatok, sőt, a lifeblood a jegyzetfüzetek el.

Az [adatfájlokat természetesen egy projektbe importálhatja](work-with-project-data-files.md), akár olyan parancsokat is használhat, mint például a `curl` egy jegyzetfüzetből a fájlok közvetlen letöltéséhez. Valószínű azonban, hogy szeretne-e jóval szélesebb körű, amely elérhető REST API-k, relációs adatbázisok, például nem fájlalapú forrásból származó adatok és a felhőbeli tárhelyen, például az Azure-táblákat.

Ez a cikk röviden ismerteti a különböző lehetőségek közül. Mivel az adathozzáférés a legjobb működésben látható, futtatható kódot talál a [Azure Notebooks-mintákban – hozzáférhet az adataihoz](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>REST API-k

Elérhető az internetről érkező adatok hatalmas mennyiségű általánosan fogalmazva, érhető el fájlok keresztül nem, de a REST API-kon keresztül. Szerencsére a notebook cella tartalmazhat bármilyen kód, mivel segítségével kód kéréseket küldeni és fogadni a JSON-adatokat. Ezt a JSON-függetlenül szeretné használni, például a pandas dataframe formátumra alakítható.

A REST API használata az adatok eléréséhez, használja ugyanazt a kódot egy jegyzetfüzetet kód cellák minden olyan alkalmazást használó. Az általános struktúra, a kérelmek kódtár használatával a következőképpen történik:

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

## <a name="azure-sql-databases"></a>Az Azure SQL-adatbázisok

Az SQL Server-adatbázisok a pyodbc vagy pymssql kódtárak segítségével érheti el.

A [Python használatával lekérdezheti az Azure SQL Database-t](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) , és útmutatást nyújt a AdventureWorks tartalmazó adatbázisok létrehozásához, és bemutatja, hogyan lehet lekérdezni ezeket az információkat. Ugyanazt a kódot a minta notebook ebben a cikkben látható.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage nem relációs tárolási, adat, és hogyan kell-e férni típusától függően számos különböző típusú biztosít:

- TABLE Storage: költséghatékony, nagy mennyiségű tárolást biztosít táblázatos adatok, például érzékelő összegyűjtött naplók, diagnosztikai naplók és így tovább.
- A BLOB storage: fájlok-szerű tárolást biztosít bármilyen típusú adatot.

A minta notebook blobok, beleértve a közös hozzáférésű jogosultságkódok használata, hogy a csak olvasási hozzáférés a blobokhoz és a táblák használatának mutatja be.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Az Azure Cosmos DB egy teljes körűen indexelt nosql-alapú tárolót biztosít JSON-dokumentumok). A következő cikkek nyújtanak számos különböző módon működik a Cosmos DB használatával, a Python:

- [SQL API-alkalmazás létrehozása a Python használatával](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [MongoDB-hez készült Azure Cosmos DB API-val rendelkező lombik-alkalmazás létrehozása](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Graph-adatbázis létrehozása a Python és a Gremlin API használatával](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Cassandra-alkalmazás létrehozása a Python és a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Table API-alkalmazás létrehozása a Python és a Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Cosmos DB használatakor használhatja az [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) könyvtárat.

## <a name="other-azure-databases"></a>Más Azure-adatbázisok

Az Azure számos más adatbázistípusok használható. Ezeket az adatbázisokat a Python elérése útmutatást nyújtanak az az alábbi cikkeket:

- [Azure Database for PostgreSQL: a Python használata a kapcsolódáshoz és az adatlekérdezéshez](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Gyors útmutató: Azure Redis Cache használata a Python használatával](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: a Python használata a kapcsolódáshoz és az adatlekérdezéshez](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Azure Data Factory másolási varázslója](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Következő lépések

- [Útmutató: Project-adatfájlok használata](work-with-project-data-files.md)
