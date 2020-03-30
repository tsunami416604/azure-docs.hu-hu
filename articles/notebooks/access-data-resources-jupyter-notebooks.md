---
title: Hozzáférés az adatokhoz a Jupyter-jegyzetfüzetekben – Az Azure Notebooks előzetes verzió
description: Megtudhatja, hogyan érheti el a fájlokat, REST API-kat, adatbázisokat és különböző Azure Storage-erőforrásokat egy Jupyter-jegyzetfüzetből.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646347"
---
# <a name="access-cloud-data-in-a-notebook"></a>Felhőbeli adatok elérése egy jegyzetfüzetben

Érdekes munka egy Jupyter notebook adatokat igényel. Data, sőt, az éltető eleme notebookok.

Minden bizonnyal [import adatfájlokat a projekt](work-with-project-data-files.md) `curl` , még parancsok segítségével, mint a notebook letölteni egy fájlt közvetlenül. Valószínű azonban, hogy sokkal szélesebb körű adatokkal kell dolgoznia, amelyek nem fájlforrásokból, például REST API-kból, relációs adatbázisokból és felhőalapú tárolásból, például Azure-táblákból érhetők el.

Ez a cikk röviden ismerteti ezeket a különböző lehetőségeket. Mivel az adathozzáférés a legjobban látható működés közben, futtatható kódot találhat az [Azure Notebook minták – Az adatok elérése.](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>REST API-k

Általánosságban elmondható, hogy az internetről elérhető hatalmas mennyiségű adat nem fájlokon, hanem REST API-kon keresztül érhető el. Szerencsére, mivel a notebook cella tartalmazhat bármilyen kódot szeretne, kód segítségével küldhet kéréseket, és json adatokat. Ezután konvertálhatja a JSON-t bármilyen formátumba, például egy panda dataframe-re.

Az adatok rest API használatával való eléréséhez használja ugyanazt a kódot a jegyzetfüzet kódcelláiban, amelyet bármely más alkalmazásban használ. A kérési könyvtárat használó általános struktúra a következő:

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

## <a name="azure-sql-databases"></a>Azure SQL-adatbázisok

Az SQL Server-adatbázisok a pyodbc vagy pymssql könyvtárak segítségével érhetők el.

[A Python segítségével lekérdezheti az Azure SQL-adatbázis](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) utasításokat az AdventureWorks-adatokat tartalmazó adatbázis létrehozásához, és bemutatja, hogyan lehet lekérdezni ezeket az adatokat. Ugyanez a kód jelenik meg a cikk mintajegyzetfüzetében is.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage számos különböző típusú nem relációs tárolást biztosít, attól függően, hogy milyen típusú adatokkal rendelkezik, és hogyan kell elérnie őket:

- Table Storage: alacsony költségű, nagy mennyiségű tárolót biztosít a táblázatos adatokhoz, például az összegyűjtött érzékelőnaplókhoz, diagnosztikai naplókhoz és így tovább.
- Blob storage: fájlszerű tárolást biztosít bármilyen típusú adat számára.

A mintajegyzetfüzet bemutatja a táblákkal és a blobokkal való munkát, beleértve azt is, hogyan használhat közös hozzáférésű aláírást a blobok írásvédett elérésének engedélyezéséhez.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Az Azure Cosmos DB egy teljes indexelt NoSQL-tárolót biztosít a JSON-dokumentumokhoz). Az alábbi cikkek számos különböző módon dolgozhatnak a Python-ból származó Cosmos DB-vel:

- [SQL API-alkalmazás létrehozása pythonnal](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Flask-alkalmazás létrehozása az Azure Cosmos DB MongoDB-hoz való API-jával](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Graph-adatbázis létrehozása a Python és a Gremlin API használatával](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Cassandra-alkalmazás létrehozása pythonnal és Azure Cosmos DB-vel](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Table API-alkalmazás létrehozása pythonnal és Azure Cosmos DB-vel](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

A Cosmos DB használata során használhatja az [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) könyvtárat.

## <a name="other-azure-databases"></a>Egyéb Azure-adatbázisok

Az Azure számos más adatbázistípust is biztosít, amelyeket használhat. Az alábbi cikkek útmutatást nyújtanak az adatbázisok Pythonból való eléréséhez:

- [A PostgreSQL-hez készült Azure Database: Csatlakozás és adatlekérdezés a Python használatával](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Rövid útmutató: Az Azure Redis Cache használata Pythonnal](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [A MySQL-hez készült Azure-adatbázis: Csatlakozás és adatlekérdezés a Python használatával](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure-adatgyár](https://azure.microsoft.com/services/data-factory/)
  - [Másolás varázsló az Azure Data Factory számára](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>További lépések

- [Útmutató: Projektadatfájlok kalvaló megoldása](work-with-project-data-files.md)
