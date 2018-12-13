---
title: Az Azure-ban a Jupyter notebookokból származó adatok erőforrások eléréséhez
description: Hogyan lehet a Jupyter notebook fájlok, a REST API-k, az adatbázisok és a különböző Azure Storage-erőforrások eléréséhez.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 425c1019a9c67ac726d03955d60d1389bef7cae2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084940"
---
# <a name="access-cloud-data-in-a-notebook"></a>Hozzáférés felhőbeli adatok történő használatát

A Jupyter notebook érdekes munkát végző szükséges adatokat. Adatok, sőt, a lifeblood a jegyzetfüzetek el.

Természetesen is [az adatfájlokat a projektbe importálja](work-with-project-data-files.md), még akkor is, például a parancsokkal `curl` a jegyzetfüzet-fájl letöltésére közvetlenül belül. Valószínű azonban, hogy szeretne-e jóval szélesebb körű, amely elérhető REST API-k, relációs adatbázisok, például nem fájlalapú forrásból származó adatok és a felhőbeli tárhelyen, például az Azure-táblákat.

Ez a cikk röviden ismerteti a különböző lehetőségek közül. Adatelérési legjobb látható, a művelet, mert a futtatható kódját megtalálja a [notebookok minták az Azure - adatok elérése](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>REST API-k

Elérhető az internetről érkező adatok hatalmas mennyiségű általánosan fogalmazva, érhető el fájlok keresztül nem, de a REST API-kon keresztül. Szerencsére a notebook cella tartalmazhat bármilyen kód, mivel segítségével kód kéréseket küldeni és fogadni a JSON-adatokat. Ezt a JSON-függetlenül szeretné használni, például a pandas dataframe formátumra alakítható.

A REST API használata az adatok eléréséhez, használja ugyanazt a kódot egy jegyzetfüzetet kód cellák minden olyan alkalmazást használó. Az általános struktúra, a kérelmek kódtár használatával a következőképpen történik:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Az Azure SQL-adatbázisok

Az SQL Server-adatbázisok a pyodbc vagy pymssql kódtárak segítségével érheti el.

[A Python használata Azure SQL Database-adatbázis lekérdezéséhez](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) AdventureWorks adatokat tartalmazó adatbázisok létrehozásával útmutatást nyújt, és bemutatja, hogyan kérdezhet le adatokat. Ugyanazt a kódot a minta notebook ebben a cikkben látható.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage nem relációs tárolási, adat, és hogyan kell-e férni típusától függően számos különböző típusú biztosít:

- TABLE Storage: költséghatékony, nagy mennyiségű tárolást biztosít táblázatos adatok, például érzékelő összegyűjtött naplók, diagnosztikai naplók és így tovább.
- A BLOB storage: fájlok-szerű tárolást biztosít bármilyen típusú adatot.

A minta notebook blobok, beleértve a közös hozzáférésű jogosultságkódok használata, hogy a csak olvasási hozzáférés a blobokhoz és a táblák használatának mutatja be.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Az Azure Cosmos DB egy teljes körűen indexelt nosql-alapú tárolót biztosít JSON-dokumentumok). A következő cikkek nyújtanak számos különböző módon működik a Cosmos DB használatával, a Python:

- [Python használatával hozhat létre egy SQL API-alkalmazás](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Készíthet olyan Flask alkalmazást a MongoDB API-ban](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [A Python és a Gremlin API gráfadatbázis létrehozása](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [A Python és az Azure Cosmos DB Cassandra alkalmazás felépítése](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Egy táblát a Python és az Azure Cosmos DB API-alkalmazás létrehozása](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Ha működik a Cosmos DB használatával, használhatja a [azure-cosmos DB-table](https://pypi.org/project/azure-cosmosdb-table/) könyvtár.

## <a name="other-azure-databases"></a>Más Azure-adatbázisok

Az Azure számos más adatbázistípusok használható. Ezeket az adatbázisokat a Python elérése útmutatást nyújtanak az az alábbi cikkeket:

- [PostgreSQL-hez készült Azure Database: csatlakozás és adatlekérdezés a Python](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Gyors útmutató: Az Azure Redis Cache használata Pythonnal](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: csatlakozás és adatlekérdezés a Python](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Az Azure Data Factory másolás varázsló](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>További lépések

- [Útmutató: adatok soubory projektu használata](work-with-project-data-files.md)
