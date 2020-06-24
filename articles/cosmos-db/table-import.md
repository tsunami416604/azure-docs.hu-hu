---
title: Meglévő adatáttelepítés Table API fiókba Azure Cosmos DB
description: Megtudhatja, hogyan telepítheti át vagy importálhatja a helyszíni vagy a Felhőbeli információkat az Azure Table API-fiókba Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3d1efc0a116a38686fa929a2058fa88e4c2cfa82
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119477"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Adatok migrálása az Azure Cosmos DB Table API-fiókba

Ez az oktatóanyag útmutatást nyújt az adatimportáláshoz a Azure Cosmos DB [Table API](table-introduction.md)való használatra. Ha az Azure Table Storage-ban tárolja az adatokat, az adatmigrálási eszközzel vagy az AzCopy segítségével importálhatja azokat az Azure Cosmos DB Table API-ba. Ha az Azure Cosmos DB Table API (előzetes verzió) szolgáltatásban létrehozott fiókban tárolja az adatokat, az adatmigrálási eszközt kell használnia az adatok migrálásához. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok importálása az adatmigrálási eszközzel
> * Adatok importálása az AzCopy használatával
> * Migrálás a Table API előzetes verziójából a Table API-ba 

## <a name="prerequisites"></a>Előfeltételek

* **Átviteli sebesség növelése:** Az adatok áttelepítésének időtartama az egyes tárolók vagy tárolók készlete számára beállított átviteli sebességtől függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Az átviteli sebesség Azure Portalon való növeléséről bővebben Az Azure Cosmos DB teljesítményszintjei és tarifacsomagjai című cikkben olvashat.

* **Azure Cosmos DB-erőforrások létrehozása:** Az adatok migrálásának indítása előtt hozza létre előre az összes táblát az Azure Portalról. Ha egy olyan Azure Cosmos DB-fiókba migrál, amely adatbázisszintű átviteli sebességgel rendelkezik, az Azure Cosmos DB-táblák létrehozásakor mindenképp adjon meg egy partíciókulcsot.

## <a name="data-migration-tool"></a>Adatmigrálási eszköz

Az Azure Cosmos DB parancssori adatmigrálási eszközének (dt.exe) segítségével importálhatja az Azure Table Storage-ban meglévő adatait a Table API általánosan elérhető verziójában létrehozott fiókba, vagy migrálhatja a Table API előzetes verziójában létrehozott fiókban található adatait a Table API általánosan elérhető verziójában létrehozott fiókba. Az egyéb források jelenleg nem támogatottak. A felhasználói felületen alapuló adatmigrálási eszköz (dtui.exe) jelenleg nem támogatott a Table API-fiókok esetében. 

Egy tábla adatainak migrálásához hajtsa végre az alábbi feladatokat:

1. Töltse le a migrálási eszközt a [GitHubról](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Futtassa a `dt.exe` fájlt a forgatókönyvnek megfelelő parancssori argumentumokkal. A `dt.exe` a következő formátumban fogad el parancsokat:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

A parancshoz használható kapcsolók:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All
    /EnableCosmosTableLog: Optional. Direct the log to a cosmos table account. If set, this defaults to destination account connection string unless /CosmosTableLogConnectionString is also provided. This is useful if multiple instances of DT are being run simultaneously.
    /CosmosTableLogConnectionString: Optional. ConnectionString to direct the log to a remote cosmos table account. 

### <a name="command-line-source-settings"></a>Parancssori forrásbeállítások

Használja az alábbi forrásbeállításokat, amikor egy Azure Table Storage-tárolót vagy a Table API előzetes verzióját adja meg a migrálás forrásaként.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Ha az Azure Table Storage-ból való importáláskor szeretné lekérni a forrás kapcsolati karakterláncot, nyissa meg a Azure Portal, és kattintson a **Storage**  >  **-fiók**  >  **hozzáférési kulcsa**elemre, majd a Másolás gombra kattintva másolja a **kapcsolati karakterláncot**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Képernyőkép a HBase forrás beállításairól":::

Ha Azure Cosmos db Table API (előzetes verzió) fiókból történő importáláskor szeretné lekérni a forrás-kapcsolódási karakterláncot, nyissa meg a Azure Portal, kattintson **Azure Cosmos db**a  >  **fiók**  >  **kapcsolódási karakterlánca** elemre, majd a Másolás gombra kattintva másolja a **kapcsolódási karakterláncot**.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Képernyőkép a HBase forrás beállításairól":::

[Példaparancs az Azure Table Storage esetén](#azure-table-storage)

[Példaparancs az Azure Cosmos DB Table API (előzetes verzió) esetén](#table-api-preview)

### <a name="command-line-target-settings"></a>Parancssori célbeállítások

Használja az alábbi célbeállításokat, amikor az Azure Cosmos DB Table API-t adja meg a migrálás céljaként.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Példaparancs: a forrás az Azure Table Storage

Ez a parancssori példa bemutatja, hogyan importálhatók adatok az Azure Table Storage-ból a Table API-ba:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Példaparancs: a forrás az Azure Cosmos DB Table API (előzetes verzió)

Ez a parancssori példa bemutatja, hogyan importálhatók adatok a Table API előzetes verziójából a Table API általánosan elérhető verziójába:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Adatok migrálása az AzCopy használatával

Az AzCopy parancssori segédprogram használata a másik módja az adatok migrálásának az Azure Table Storage-ból az Azure Cosmos DB Table API-ba. Az AzCopy használatakor először exportálni kell az adatokat az [adatoknak a Table Storage-ból való exportálását](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) ismertető cikkben leírt módon, majd importálni kell az adatokat az Azure Cosmos DB-be, az [Azure Cosmos DB Table API-val](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) foglalkozó cikkben leírtak szerint.

Az Azure Cosmos DB-be való importálás végrehajtásakor tekintse meg az alábbi példát. Vegye figyelembe, hogy a /Dest értékként a cosmosdb, nem pedig a core értéket használja.

Importálási példaparancs:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrálás a Table API előzetes verziójából a Table API-ba.

> [!WARNING]
> Ha azonnal szeretné kihasználni az általánosan elérhető táblák előnyeit, akkor migrálja a meglévő előzetes verziójú táblákat a jelen szakaszban leírtak szerint. Ha ezt elmulasztja, a következő hetekben automatikusan elvégezzük a migrálást az előzetes verziót használó meglévő ügyfelek számára. Vegye azonban figyelembe, hogy az automatikusan migrált előzetes verziójú táblákra bizonyos korlátozások vonatkozhatnak, az újonnan létrehozott táblákra vonatkozóan viszont nincsenek ilyen korlátozások.
> 

A Table API mostantól általánosan elérhető. Különbségek vannak az előzetes verzió táblái és az általános elérhető verzió táblái között a felhőben futó kód és az ügyfélnél futó kód szempontjából egyaránt. Ezért nem javasoljuk, hogy egy előzetes verziójú SDK-ügyfelet egy általánosan elérhető Table API-fiókkal együtt használjon, vagy fordítva. Ha a Table API előzetes verziójának felhasználói továbbra is használni szeretnék a meglévő tábláikat éles környezetben, akkor az előzetes verziójú környezetből az általánosan elérhető verziójú környezetbe kell migrálniuk, vagy meg kell várniuk az automatikus migrálást. Ha megvárja az automatikus migrálást, értesítést kap majd a migrált táblákra vonatkozó korlátozásokról. A migrálást követően korlátozás nélkül hozhat majd létre új táblákat a meglévő fiókjában (a korlátozások csak a migrált táblákra vonatkoznak).

Migrálás a Table API előzetes verziójából a Table API általánosan elérhető verziójába:

1. Hozzon létre egy új Azure Cosmos DB-fiókot, és állítsa be a fiók API-típusát az Azure Table értékre az [Adatbázisfiók létrehozása](create-table-dotnet.md#create-a-database-account) című szakaszban leírtak szerint.

2. Módosítsa az ügyfeleket, hogy azok a [Table API SDK-k](table-sdk-dotnet.md) általánosan elérhető kiadását használják.

3. Migrálja az ügyféladatokat az előzetes verzió tábláiból az általánosan elérhető verzió tábláiba az adatmigrálási eszközzel. Az adatmigrálási eszköz ilyen célú használatára vonatkozó utasításokat az [adatmigrálási eszközzel](#data-migration-tool) foglalkozó szakasz ismerteti. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatok importálása az adatmigrálási eszközzel.
> * Adatok importálása az AzCopy használatával.
> * Migrálás a Table API előzetes verziójából a Table API-ba.

Továbbléphet a következő oktatóanyagra, amelyben megtudhatja, hogyan kérdezhet le adatokat az Azure Cosmos DB Table API használatával. 

> [!div class="nextstepaction"]
>[Hogyan kérdezhetők le adatok?](../cosmos-db/tutorial-query-table.md)
