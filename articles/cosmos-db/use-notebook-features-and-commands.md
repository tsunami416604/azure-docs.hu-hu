---
title: Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB
description: Ismerje meg, hogyan használhatók a beépített parancsok és szolgáltatások a Azure Cosmos DB beépített jegyzetfüzetekkel való gyakori műveletek végrehajtásához.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: f5ab1491c8561c90b06374a0a58f160cbcdd1cad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444652"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Beépített notebook-parancsok és-szolgáltatások használata Azure Cosmos DB

A Azure Cosmos DB beépített Jupyter notebookok lehetővé teszik az adatok elemzését és megjelenítését a Azure Portal. Ez a cikk leírja, hogyan lehet beépített notebook-parancsokat és -funkciókat használni a gyakori műveletek végrehajtásához.

## <a name="install-a-new-package"></a>Új csomag telepítése
Miután engedélyezte az Azure Cosmos-fiókok notebook-támogatását, megnyithat egy új jegyzetfüzetet, és telepítheti a csomagot.

Helyezzen be és futtassa a következő kódot egy új kódrészletben, és cserélje le a ``PackageToBeInstalled``t a kívánt Python-csomagra.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Ez a csomag minden jegyzetfüzetből elérhető lesz az Azure Cosmos-fiók munkaterületen. 

> [!TIP]
> Ha a notebookjának egyéni csomagra van szüksége, javasoljuk, hogy adjon hozzá egy cellát a jegyzetfüzetben a csomag telepítéséhez, mivel a rendszer eltávolítja a csomagokat, ha [alaphelyzetbe állítja a munkaterületet](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>SQL-lekérdezés futtatása

A ``%%sql`` Magic paranccsal [SQL-lekérdezést](sql-query-getting-started.md) futtathat a fiókjában lévő bármelyik tárolón. Használja a szintaxist:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Cserélje le a ``{database_id}`` és a ``{container_id}``t az adatbázis és a tároló nevére a Cosmos-fiókban. Ha a ``--database`` és ``--container`` argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
- A Azure Cosmos DBban érvényes SQL-lekérdezéseket futtathat. A lekérdezés szövegének új sorban kell lennie.

Példa: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Futtassa ```%%sql?``` egy cellában, és tekintse meg a jegyzetfüzetben található SQL Magic parancs súgóját.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL-lekérdezés és kimenet futtatása pandák DataFrame

Egy ``%%sql`` lekérdezés eredményét egy [Panda DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)is kiválaszthatja. Használja a szintaxist: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Cserélje le a ``{database_id}`` és a ``{container_id}``t az adatbázis és a tároló nevére a Cosmos-fiókban. Ha a ``--database`` és ``--container`` argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
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
## <a name="upload-json-items-to-a-container"></a>JSON-elemek feltöltése tárolóba
A ``%%upload`` Magic paranccsal adatok tölthetők fel egy JSON-fájlból egy megadott Azure Cosmos-tárolóba. Az elemek feltöltéséhez használja az alábbi parancsot:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Cserélje le a ``{database_id}`` és az ``{container_id}``t az Azure Cosmos-fiókban található adatbázis és tároló nevére. Ha a ``--database`` és ``--container`` argumentumok nincsenek megadva, a rendszer végrehajtja a lekérdezést az [alapértelmezett adatbázison és tárolón](#set-default-database-for-queries).
- Cserélje le a ``{url_location_of_file}``t a JSON-fájljának helyére. A fájlnak érvényes JSON-objektumokból álló tömbnek kell lennie, és a nyilvános interneten keresztül elérhetőnek kell lennie.

Példa:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
A kimeneti statisztikával kiszámíthatja az elemek feltöltéséhez használt hatályos RU/s értékeit. Ha például az 25 000 RUs-t 38 másodpercen keresztül használták, a hatályos RU/s értéke 25 000 RUs/38 másodperc = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Alapértelmezett adatbázis beállítása lekérdezésekhez
Beállíthatja, hogy az alapértelmezett adatbázis ```%%sql``` parancsokat a rendszer a jegyzetfüzethez használja. Cserélje le a ```{database_id}```t az adatbázis nevére.

```bash
%database {database_id}
```
A jegyzetfüzetben lévő dokumentáció megtekintéséhez futtasson ```%database?``` a cellában.

## <a name="set-default-container-for-queries"></a>Alapértelmezett tároló beállítása lekérdezésekhez
Beállíthatja az alapértelmezett tárolót, ```%%sql``` a parancsokat a rendszer a jegyzetfüzethez használja. Cserélje le a ```{container_id}```t a tároló nevére.

```bash
%container {container_id}
```
A jegyzetfüzetben lévő dokumentáció megtekintéséhez futtasson ```%container?``` a cellában.

## <a name="use-built-in-nteract-data-explorer"></a>Beépített nteract adatkezelő használata
A beépített [nteract adatkezelő](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) használatával szűrheti és megjelenítheti a DataFrame. A szolgáltatás engedélyezéséhez állítsa a ``pd.options.display.html.table_schema`` beállítást ``True`` és ``pd.options.display.max_rows`` a kívánt értékre (az összes eredmény megjelenítéséhez beállíthatja ``pd.options.display.max_rows`` ``None``.

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract adatkezelő](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>A beépített Python SDK használata
A [Azure Cosmos db PYTHON SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 4-es verziója telepítve van, és tartalmazza az Azure Cosmos-fiók notebook-környezetében.

Az SDK-műveletek futtatásához használja a beépített ``cosmos_client``-példányt. 

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

## <a name="create-a-custom-instance-of-cosmos_client"></a>``cosmos_client`` egyéni példányának létrehozása
A nagyobb rugalmasság érdekében a következő esetekben hozhat létre ``cosmos_client`` egyéni példányát:

- A [kapcsolatok házirendjének](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) testreszabása
- Futtasson műveleteket egy másik Azure Cosmos-fiókon, mint a

Az aktuális fiók kapcsolati sztringjét és elsődleges kulcsát a [környezeti változók](#access-the-account-endpoint-and-primary-key-env-variables)segítségével érheti el. 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Hozzáférés a fiók végpontja és az elsődleges kulcs env változói
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> A ``COSMOS_ENDPOINT`` és ``COSMOS_KEY`` környezeti változók csak az SQL API-ra alkalmazhatók. Más API-k esetén keresse meg a végpontot és a kulcsot az Azure Cosmos-fiókjának **kapcsolatok karakterláncok** vagy **kulcsok** paneljén.  

## <a name="reset-notebooks-workspace"></a>Jegyzetfüzetek alaphelyzetbe állítása munkaterület
Ha a jegyzetfüzetek munkaterületet az alapértelmezett beállításokra szeretné visszaállítani, válassza a parancssáv **munkaterület alaphelyzetbe** állítása lehetőséget. Ezzel eltávolítja az összes egyéni telepített csomagot, majd újraindítja a Jupyter-kiszolgálót. A jegyzetfüzeteket, a fájlokat és az Azure Cosmos-erőforrásokat nem érinti a rendszer.  

![Jegyzetfüzetek alaphelyzetbe állítása munkaterület](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit
- Tudnivalók a [Azure Cosmos db PYTHON SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) -ról
