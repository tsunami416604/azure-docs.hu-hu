---
title: 'Rövid útmutató: Adatok betöltése az Azure Data Explorer Python-kódtárral'
description: Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan tölthet be adatokat az Azure Data Explorerbe a Python használatával.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 52be08006985ee2f2e1ea4427e0f63ebbeb6e8b2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900496"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-python-library"></a>Rövid útmutató: Adatok betöltése az Azure Data Explorer Python-kódtárral

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer két ügyfélkódtárat biztosít a Python számára: egy [betöltési kódtárat](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) és [egy adatkódtárat](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Ezekkel a kódtárakkal adatokat tölthet be egy fürtbe, illetve adatokat kérdezhet le a kódból. Ebben a rövid útmutatóban először létrehoz egy táblát és egy adatleképezést egy tesztfürtben. Ezután sorba helyezi a fürtbe való betöltést, és ellenőrzi az eredményeket.

Ez a rövid útmutató elérhető [Azure Notebookként](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb) is.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez az Azure-előfizetés mellett szüksége lesz a következőkre is:

* [Egy tesztfürt és -adatbázis](create-cluster-database-portal.md)

* A fejlesztői számítógépen telepített [Python](https://www.python.org/downloads/)

## <a name="install-the-data-and-ingest-libraries"></a>Az adatok telepítése és a kódtárak betöltése

Telepítse a következőket: *azure kusto-data* és *azure kusto-ingest*.

```python
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Importálási utasítások és állandók hozzáadása

Importáljon osztályokat a kódtárakból, valamint a *datetime* és a *pandas* adatelemzési kódtárból.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
import pandas as pd
import datetime
```

Az alkalmazás hitelesítéséhez az Azure Data Explorer az AAD-bérlő azonosítóját használja. A bérlőazonosító megkereséséhez használja a következő URL-címet úgy, hogy a *YourDomain* kifejezés helyére a saját tartományát írja be.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ha például a tartomány a *contoso.com*, az URL-cím a következő: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kattintson erre az URL-címre az eredmények megtekintéséhez; az első sor a következő. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A bérlőazonosító ebben az esetben a következő: `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. A kód futtatása előtt állítsa be az AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI és KUSTO_DATABASE értékeit.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE  = "<DatabaseName>"
```

Most hozza létre a kapcsolati sztringet. Ez a példa eszközhitelesítést használ a fürt eléréséhez. Emellett az AAD-alkalmazástanúsítványt, az AAD-alkalmazáskulcsot és az AAD-felhasználót és -jelszót is használhatja.

Egy későbbi lépésben fogja létrehozni a céltáblát és a leképezést.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>A forrásfájl adatainak beállítása

További osztályokat importálhat, és beállíthatja az adatforrásfájl állandóit. Ez a példa egy Azure Blob Storage-ban üzemeltetett mintafájlt használ. A **StormEvents** mintaadatkészlet a [környezeti adatok nemzeti központjaiból](https://www.ncdc.noaa.gov/stormevents/) származó, időjárással kapcsolatos adatokat tartalmaz.

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-test-cluster"></a>Tábla létrehozása a tesztfürtön

Hozzon létre egy táblát, amely megfelel a StormEvents.csv fájlban szereplő adatok sémájának. Amikor ez a kód fut, a következőhöz hasonló üzenetet ad vissza: *A bejelentkezéshez webböngészőben nyissa meg a https://microsoft.com/devicelogin oldalt, és írja be az F3W4VWZDM kódot a hitelesítéshez*. Kövesse a bejelentkezési lépéseket, majd térjen vissza a következő kódblokk futtatásához. A kapcsolatot létrehozó későbbi kódblokkokhoz ismét be kell jelentkeznie.

```python
KUSTO_CLIENT = KustoClient(KCSB_ENGINE)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

df_table_create_output = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND).primary_results[0].to_dataframe()

df_table_create_output
```

## <a name="define-ingestion-mapping"></a>Adatbetöltési leképezés meghatározása

Leképezheti a bejövő CSV-adatokat a tábla létrehozásakor használt oszlopnevekre és adattípusokra.

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

df_mapping_create_output = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND).primary_results[0].to_dataframe()

df_mapping_create_output
```

## <a name="queue-a-message-for-ingestion"></a>Üzenet várólistába helyezése a betöltéshez

Az üzeneteket várólistába helyezheti az adatokat a Blob Storage-ból való lekérése és az Azure Data Explorerbe való betöltése érdekében.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/en-us/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES  = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv, mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)  # 10 is the raw size of the data in bytes
INGESTION_CLIENT.ingest_from_blob(BLOB_DESCRIPTOR,ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')

```

## <a name="validate-that-data-was-ingested-into-the-table"></a>Az adatok táblába való betöltésének ellenőrzése

Várjon öt-tíz percet, amíg a várólistában lévő betöltés ütemezi a betöltést és betölti az adatokat az Azure Data Explorerbe. Ezután futtassa a következő kódot a StormEvents-táblában lévő rekordok számának lekérdezéséhez.

```python
QUERY = "StormEvents | count"

df = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY).primary_results[0].to_dataframe()

df
```

## <a name="run-troubleshooting-queries"></a>Hibaelhárítási lekérdezések futtatása

Jelentkezzen be a [https://dataexplorer.azure.com](https://dataexplorer.azure.com) oldalon, és csatlakozzon a fürthöz. Futtassa a következő parancsot az adatbázisban annak ellenőrzéséhez, hogy voltak-e betöltési hibák az elmúlt négy órában. A futtatás előtt cserélje le az adatbázis nevét.
    
```Kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Futtassa a következő parancsot az elmúlt négy órában végzett összes betöltési művelet állapotának megtekintéséhez. A futtatás előtt cserélje le az adatbázis nevét.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat. Ha nem szeretné, futtassa a következő parancsot az adatbázisban a StormEvents-tábla felesleges elemeinek eltávolításához.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lekérdezések írása](write-queries.md)
