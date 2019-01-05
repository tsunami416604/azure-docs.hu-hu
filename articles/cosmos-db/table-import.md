---
title: Meglévő adatok áttelepítése az Azure Cosmos DB Table API-fiókot
description: Ismerje meg, hogyan áttelepítése vagy importálása a helyszíni vagy felhőbeli adatok Azure Table API-fiókba az Azure Cosmos DB-ben.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 20c7a065ec2a302f53c78fb4cfd348d17ab7bdab
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040285"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Adatok migrálása az Azure Cosmos DB Table API-fiókba

Ez az oktatóanyag útmutatást nyújt az adatok importálásához az Azure Cosmos DB [Table API](table-introduction.md)-val való használatra. Ha az Azure Table Storage-ban tárolja az adatokat, az adatmigrálási eszközzel vagy az AzCopy segítségével importálhatja azokat az Azure Cosmos DB Table API-ba. Ha az Azure Cosmos DB Table API (előzetes verzió) szolgáltatásban létrehozott fiókban tárolja az adatokat, az adatmigrálási eszközt kell használnia az adatok migrálásához. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok importálása az adatmigrálási eszközzel
> * Adatok importálása az AzCopy használatával
> * Migrálás a Table API előzetes verziójából a Table API-ba 

## <a name="prerequisites"></a>Előfeltételek

* **Átviteli sebesség növelése:** Az adatok migrálása idejére az átviteli sebesség beállítása egy egyéni tároló mennyiségét vagy egy tárolók függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. További információk az átviteli sebesség növeléséről az Azure Portalon: Teljesítményszintek és tarifacsomagok az Azure Cosmos DB-ben.

* **Azure Cosmos DB-erőforrások létrehozása:** Adatok áttelepítése előtt előre létrehozni az Azure Portalról a táblák. Ha egy olyan Azure Cosmos DB-fiókba migrál, amely adatbázisszintű átviteli sebességgel rendelkezik, az Azure Cosmos DB-táblák létrehozásakor mindenképp adjon meg egy partíciókulcsot.

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

### <a name="command-line-source-settings"></a>Parancssori forrásbeállítások

Használja az alábbi forrásbeállításokat, amikor egy Azure Table Storage-tárolót vagy a Table API előzetes verzióját adja meg a migrálás forrásaként.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Az Azure Table Storage-ból való importáláskor a forrás kapcsolati sztring lekéréséhez nyissa meg az Azure Portalt, kattintson a **Tárfiókok** > **Fiók** > **Hozzáférési kulcs** elemre, majd kattintson a másolásra szolgáló gombra a **Kapcsolati sztring** másolásához.

![Képernyőkép a HBase forrásbeállításairól](./media/table-import/storage-table-access-key.png)

Az Azure Cosmos DB Table API (előzetes verzió) szolgáltatásban létrehozott fiókból való importáláskor a forrás kapcsolati sztring lekéréséhez nyissa meg az Azure Portalt, kattintson az **Azure Cosmos DB** > **Fiók** > **Kapcsolati sztring** elemre, majd kattintson a másolásra szolgáló gombra a **Kapcsolati sztring** másolásához.

![Képernyőkép a HBase forrásbeállításairól](./media/table-import/cosmos-connection-string.png)

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
### <a name="sample-command-source-is-azure-table-storage"></a>Mintául szolgáló parancs: Forrás az Azure Table storage

Ez a parancssori példa bemutatja, hogyan importálhatók adatok az Azure Table Storage-ból a Table API-ba:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Mintául szolgáló parancs: Forrás az Azure Cosmos DB Table API (előzetes verzió)

Ez a parancssori példa bemutatja, hogyan importálhatók adatok a Table API előzetes verziójából a Table API általánosan elérhető verziójába:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Adatok migrálása az AzCopy használatával

Az AzCopy parancssori segédprogram használata a másik módja az adatok migrálásának az Azure Table Storage-ból az Azure Cosmos DB Table API-ba. Az AzCopy használatakor először exportálni kell az adatokat az [adatoknak a Table Storage-ból való exportálását](../storage/common/storage-use-azcopy.md#export-data-from-table-storage) ismertető cikkben leírt módon, majd importálni kell az adatokat az Azure Cosmos DB-be, az [Azure Cosmos DB Table API-val](../storage/common/storage-use-azcopy.md#import-data-into-table-storage) foglalkozó cikkben leírtak szerint.

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

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Adatok importálása az adatmigrálási eszközzel.
> * Adatok importálása az AzCopy használatával.
> * Migrálás a Table API előzetes verziójából a Table API-ba.

Továbbléphet a következő oktatóanyagra, amelyben megtudhatja, hogyan kérdezhet le adatokat az Azure Cosmos DB Table API használatával. 

> [!div class="nextstepaction"]
>[Hogyan kérdezhetők le adatok?](../cosmos-db/tutorial-query-table.md)
