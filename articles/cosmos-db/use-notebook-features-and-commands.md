---
title: Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB
description: Ismerje meg, hogyan használhatók a beépített parancsok és szolgáltatások a Azure Cosmos DB beépített jegyzetfüzetekkel való gyakori műveletek végrehajtásához.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 1eda8271a3b8aa2c9e247252bd755279d23b6e10
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310346"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB

A Azure Cosmos DB beépített Jupyter notebookok lehetővé teszik az adatok elemzését és megjelenítését a Azure Portal. Ez a cikk azt ismerteti, hogyan használhatók a beépített notebook-parancsok és-szolgáltatások a gyakori műveletek végrehajtásához.

## <a name="install-a-new-package"></a>Új csomag telepítése
Miután engedélyezte az Azure Cosmos-fiókok notebook-támogatását, megnyithat egy új jegyzetfüzetet, és telepítheti a csomagot.

Az új kód cellában szúrja be és futtassa a következő kódot, ``PackageToBeInstalled`` és cserélje le a kívánt Python-csomagra.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Ez a csomag az Azure Cosmos-fiókban lévő bármelyik jegyzetfüzetből elérhető lesz. 

## <a name="run-a-sql-query"></a>SQL-lekérdezés futtatása

A ``%%sql`` Magic paranccsal [SQL-lekérdezést](sql-query-getting-started.md) futtathat a fiókjában lévő bármelyik tárolón. Használja a szintaxist:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Cserélje ``{database_id}`` le ``{container_id}`` a és a nevet az adatbázis és a tároló nevére a Cosmos-fiókban. Ha a ``--database`` és ``--container`` az argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
- A Azure Cosmos DBban érvényes SQL-lekérdezéseket futtathat. A lekérdezés szövegének új sorban kell lennie.

Példa: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Futtassa ```%%sql?``` a parancsot egy cellában, és tekintse meg a jegyzetfüzetben található SQL Magic parancs súgóját.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL-lekérdezés és kimenet futtatása pandák DataFrame

A ``%%sql`` lekérdezés eredményeit egy [Panda DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)is kiválaszthatja. Használja a szintaxist: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Cserélje ``{database_id}`` le ``{container_id}`` a és a nevet az adatbázis és a tároló nevére a Cosmos-fiókban. Ha a ``--database`` és ``--container`` az argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
- A ``{outputDataFrameVar}`` helyére írja be annak a DataFrame-változónak a nevét, amely az eredményeket fogja tartalmazni.
- A Azure Cosmos DBban érvényes SQL-lekérdezéseket futtathat. A lekérdezés szövegének új sorban kell lennie. 

Példa:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>Alapértelmezett adatbázis beállítása lekérdezésekhez
Beállíthatja, hogy a rendszer ```%%sql``` az alapértelmezett adatbázis-parancsokat használja a jegyzetfüzethez. Cserélje ```{database_id}``` le az nevet az adatbázis nevére.

```bash
%database {database_id}
```
Futtasson ```%database?``` egy cellában, és tekintse meg a dokumentációt a jegyzetfüzetben.

## <a name="set-default-container-for-queries"></a>Alapértelmezett tároló beállítása lekérdezésekhez
Beállíthatja, hogy a rendszer ```%%sql``` az alapértelmezett tároló-parancsokat használja a jegyzetfüzethez. Cserélje ```{container_id}``` le a nevet a tároló nevére.

```bash
%container {container_id}
```
Futtasson ```%container?``` egy cellában, és tekintse meg a dokumentációt a jegyzetfüzetben.

## <a name="use-the-built-in-python-sdk"></a>A beépített Python SDK használata
A [Azure Cosmos db PYTHON SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 4-es verziója telepítve van, és tartalmazza a Cosmos-fiók notebook-környezetében.

A beépített ``cosmos_client`` példány használatával futtasson bármely SDK-műveletet. 

Példa:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Lásd: [PYTHON SDK-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> A beépített Python SDK csak az SQL (Core) API-fiókok esetében támogatott. Más API-k esetén [telepítenie kell az](#install-a-new-package) API-nak megfelelő Python-illesztőprogramot. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Egyéni példány létrehozása``cosmos_client``
A rugalmasság érdekében létrehozhat egy egyéni példányt ``cosmos_client`` a következőhöz:

- A [kapcsolatok házirendjének](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) testreszabása
- Futtasson műveleteket egy másik Cosmos-fiókon, mint a

Az aktuális fiók kapcsolati sztringjét és elsődleges kulcsát a [környezeti változók](#access-the-account-endpoint-and-primary-key-env-variables)segítségével érheti el. 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Hozzáférés a fiók végpontja és az elsődleges kulcs env változói
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> A ``COSMOS_ENDPOINT`` és ``COSMOS_KEY`` a környezeti változók csak az SQL API-ra alkalmazhatók. Más API-k esetén keresse meg a végpontot és a kulcsot a Cosmos-fiókjának **kapcsolatok karakterláncok** vagy **kulcsok** paneljén.  

## <a name="next-steps"></a>További lépések

- Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit
- Tudnivalók a [Azure Cosmos db PYTHON SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) -ról
