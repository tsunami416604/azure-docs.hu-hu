---
title: Beépített jegyzetfüzetparancsok és -funkciók használata az Azure Cosmos DB-ben (előzetes verzió)
description: Ismerje meg, hogyan használhatja a beépített parancsokat és funkciókat az Azure Cosmos DB beépített jegyzetfüzeteivel végzett gyakori műveletekhez.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513399"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Beépített jegyzetfüzetparancsok és -funkciók használata az Azure Cosmos DB-ben (előzetes verzió)

Az Azure Cosmos DB beépített Jupyter-jegyzetfüzetei lehetővé teszik az Azure Portaladatainak elemzését és megjelenítését. Ez a cikk leírja, hogyan lehet beépített notebook-parancsokat és -funkciókat használni a gyakori műveletek végrehajtásához.

## <a name="install-a-new-package"></a>Új csomag telepítése
Miután engedélyezte a jegyzetfüzetek támogatását az Azure Cosmos-fiókokhoz, megnyithat egy új jegyzetfüzetet, és telepíthet egy csomagot.

Egy új kódcellában szúrja be és ``PackageToBeInstalled`` futtassa a következő kódot, és cserélje ki a kívánt Python-csomagot.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Ez a csomag az Azure Cosmos-fiók munkaterületén lévő bármely jegyzetfüzetből használható lesz. 

> [!TIP]
> Ha a jegyzetfüzethez egyéni csomag szükséges, javasoljuk, hogy adjon hozzá egy cellát a jegyzetfüzethez a csomag telepítéséhez, mivel a csomagok törlődnek, ha [alaphelyzetbe állítja a munkaterületet.](#reset-notebooks-workspace)  

## <a name="run-a-sql-query"></a>SQL-lekérdezés futtatása

A ``%%sql`` magic paranccsal [sql-lekérdezést](sql-query-getting-started.md) futtathat a fiók bármely tárolóján. Használja a szintaxist:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Cserélje ``{database_id}`` ``{container_id}`` le, és a nevét, az adatbázis és a tároló a Cosmos-fiókban. Ha ``--database`` a ``--container`` és az argumentumok nem adottak meg, a lekérdezés az [alapértelmezett adatbázison és tárolón lesz végrehajtva.](#set-default-database-for-queries)
- Bármilyen SQL-lekérdezést futtathat, amely érvényes az Azure Cosmos DB-ben. A lekérdezés szövegének új sorban kell lennie.

Példa: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Futtassa ```%%sql?``` egy cellában, hogy a súgó dokumentációját az sql magic parancs a notebook.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL-lekérdezés futtatása és kimenete Pandas DataFrame-en

A ``%%sql`` lekérdezés eredményeit kimenetelét [pandas dataframe](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Használja a szintaxist: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Cserélje ``{database_id}`` ``{container_id}`` le, és a nevét, az adatbázis és a tároló a Cosmos-fiókban. Ha ``--database`` a ``--container`` és az argumentumok nem adottak meg, a lekérdezés az [alapértelmezett adatbázison és tárolón lesz végrehajtva.](#set-default-database-for-queries)
- Cserélje ``{outputDataFrameVar}`` le az eredményeket tartalmazó DataFrame változó nevére.
- Bármilyen SQL-lekérdezést futtathat, amely érvényes az Azure Cosmos DB-ben. A lekérdezés szövegének új sorban kell lennie. 

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
A ``%%upload`` magic paranccsal adatokat tölthet fel egy JSON-fájlból egy megadott Azure Cosmos-tárolóba. Az elemek feltöltéséhez használja a következő parancsot:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Cserélje ``{database_id}`` ``{container_id}`` le, és az Azure Cosmos-fiókban az adatbázis és a tároló nevét. Ha ``--database`` a ``--container`` és az argumentumok nem adottak meg, a lekérdezés az [alapértelmezett adatbázison és tárolón lesz végrehajtva.](#set-default-database-for-queries)
- Cserélje ``{url_location_of_file}`` le a JSON-fájl helyét. A fájlnak érvényes JSON-objektumok tömbjének kell lennie, és a nyilvános interneten keresztül elérhetőnek kell lennie.

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
A kimeneti statisztikákkal kiszámíthatja a cikkek feltöltéséhez használt effektív RU-kat. Ha például 25 000 több mint 38 másodperc alatt használ fel 25 000 VT-t, az effektus/s 25 000 VT / 38 másodperc = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Lekérdezések alapértelmezett adatbázisának beállítása
Beállíthatja, hogy ```%%sql``` a jegyzetfüzethez alapértelmezett adatbázis-parancsokat használjon. Cserélje ```{database_id}``` le az adatbázis nevére.

```bash
%database {database_id}
```
Futtassa ```%database?``` egy cellában a jegyzetfüzet dokumentációjának megtekintéséhez.

## <a name="set-default-container-for-queries"></a>Alapértelmezett tároló beállítása lekérdezésekhez
Beállíthatja, hogy ```%%sql``` a jegyzetfüzethez alapértelmezett tárolóparancsokat használjanak. Cserélje ```{container_id}``` le a tároló nevét.

```bash
%container {container_id}
```
Futtassa ```%container?``` egy cellában a jegyzetfüzet dokumentációjának megtekintéséhez.

## <a name="use-built-in-nteract-data-explorer"></a>Beépített nteract adatkezelő használata
A beépített [nteract adatkezelő](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) segítségével szűrheti és jelenítheti meg a DataFrame-et. A szolgáltatás engedélyezéséhez állítsa ``pd.options.display.html.table_schema`` ``True`` be ``pd.options.display.max_rows`` a kívánt értékre ``pd.options.display.max_rows`` és ``None`` a kívánt értékre vonatkozó beállítást (beállíthatja, hogy az összes eredmény megjelenjen).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract adatkezelő](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>A beépített Python SDK használata
Az Azure [Cosmos DB Python SDK SQL API-hoz](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 4-es verziója telepítve van, és az Azure Cosmos-fiók jegyzetfüzetkörnyezetében található.

A beépített ``cosmos_client`` példány segítségével bármely SDK-művelet futtatásához. 

Példa:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Lásd: [Python SDK-minták.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples) 

> [!IMPORTANT]
> A beépített Python SDK csak az SQL (Core) API-fiókok támogatott. Más API-k esetén telepítenie kell [a megfelelő Python-illesztőprogramot,](#install-a-new-package) amely megfelel az API-nak. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Egyéni példány létrehozása``cosmos_client``
A nagyobb rugalmasság érdekében létrehozhat egy ``cosmos_client`` egyéni példányt a következők érdekében:

- A [kapcsolatházirend](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) testreszabása
- Műveletek futtatása egy másik Azure Cosmos-fiókon, mint amelyben van

Az aktuális számla kapcsolati karakterláncát és elsődleges kulcsát a [környezeti változókon](#access-the-account-endpoint-and-primary-key-env-variables)keresztül érheti el. 

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
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Hozzáférés a fiókvégponthoz és az elsődleges kulcsenv változókhoz
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> A ``COSMOS_ENDPOINT`` ``COSMOS_KEY`` környezeti változók csak SQL API-ra alkalmazhatók. Más API-k esetén keresse meg a végpontot és a kulcsot az Azure Cosmos-fiók **kapcsolati karakterláncok** vagy **kulcsok** panelen.  

## <a name="reset-notebooks-workspace"></a>Jegyzetfüzet-munkaterület visszaállítása
Ha vissza szeretné állítani a jegyzetfüzetek munkaterületét az alapértelmezett beállításokra, válassza a munkaterület **visszaállítása** lehetőséget a parancssávon. Ezzel eltávolítja az egyéni telepített csomagokat, és újraindítja a Jupyter-kiszolgálót. A jegyzetfüzetek, a fájlok és az Azure Cosmos-erőforrások nem érinti.  

![Jegyzetfüzet-munkaterület visszaállítása](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure Cosmos DB Jupyter-jegyzetfüzetek](cosmosdb-jupyter-notebooks.md) előnyeit
- Ismerje meg az [Azure Cosmos DB Python SDK sql API-hoz készült](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
