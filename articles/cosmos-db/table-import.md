---
title: Importálhat adatokat az Azure Cosmos DB tábla API való használatra |} Microsoft Docs
description: Megtudhatja, hogyan importálhat az adatokat az Azure Cosmos DB tábla API használata.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: sngun
ms.openlocfilehash: 7a8eaaaaa8bbfa8cf9397ce52dfa93d71b0df30c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Adatok importálása az Azure Cosmos DB tábla API való használatra

Ez az oktatóanyag utasításokkal szolgál az adatok az Azure Cosmos DB való használatra importálása [tábla API](table-introduction.md). Azure Table storage-ban tárolt adatok az adatok importálásához használhatja az adatáttelepítés eszközzel vagy az AzCopy. Ha egy Azure Cosmos DB tábla API (előzetes verzió) fiókban tárolt adatok, az adatok áttelepítése eszközt kell használnia az adatok áttelepítéséhez. Az adatok importálása után a prémium szintű képességek kihasználásához Azure Cosmos DB ajánlatok kulcsrakész globális terjesztési, dedikált átviteli, egyjegyű ezredmásodperces késések fordulnak elő, az 99th aránya a garantált magas rendelkezésre állású, például képes lesz és másodlagos indexelő.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az adatáttelepítés eszközzel adatok importálása
> * Az AzCopy adatok importálása
> * Tábla API tábla API (előzetes verzió) áttelepítése 

## <a name="data-migration-tool"></a>Az adatáttelepítési eszköz

A parancssori Azure Cosmos DB adatáttelepítési eszközét (dt.exe) segítségével importálja a meglévő Azure Table storage adatok GA tábla API-fiókra, vagy adatokat telepítsen át egy tábla API (előzetes verzió) fiók egy tábla API GA figyelembe. Más forrásokból jelenleg nem támogatottak. A felhasználói felületen alapuló adatáttelepítési eszköz (dtui.exe) jelenleg nem támogatott a tábla API fiókok. 

A tábla adatai az áttelepítés a következő feladatokat kell végrehajtani:

1. Az áttelepítési eszközt [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Futtatás `dt.exe` a parancssori argumentumokat használni a forgatókönyv segítségével.

DT.exe parancs hajtja végre a következő formátumban:

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

A parancs lehetőségek közül választhat:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Parancssori adatforrás-beállítások

A következő beállításokkal forrás Azure Table Storage vagy a tábla API preview meghatározásakor az áttelepítés forrásaként.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Az Azure Table storage importálása során a forrás kapcsolati karakterlánc lekéréséhez nyissa meg az Azure-portálon, és kattintson **tárfiókok** > **fiók**  >   **Hívóbetűk**, majd a Másolás gombra segítségével másolja a **kapcsolati karakterlánc**.

![Képernyőfelvétel a HBase forrására vonatkozó beállítások](./media/table-import/storage-table-access-key.png)

A forrás kapcsolati karakterlánc lekéréséhez, Azure Cosmos DB tábla API (előzetes verzió) fiókból importálásakor, nyissa meg az Azure portálon kattintson **Azure Cosmos DB** > **fiók**  >  **Kapcsolati karakterlánc** és a Másolás gombra követve másolja át a **kapcsolati karakterlánc**.

![Képernyőfelvétel a HBase forrására vonatkozó beállítások](./media/table-import/cosmos-connection-string.png)

[A minta Azure Table Storage parancs](#azure-table-storage)

[A minta Azure Cosmos DB tábla API (előzetes verzió) parancs](#table-api-preview)

### <a name="command-line-target-settings"></a>Parancssori tárolóbeállítások

A következő beállításokkal cél Azure Cosmos DB tábla API és a cél az áttelepítés meghatározásakor.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Parancs mintát: forrás az Azure Table storage

Íme egy parancssori minta bemutatja, hogyan importálhatja az Azure Table storage tábla API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Parancs mintát: forrása Azure Cosmos DB tábla API (előzetes verzió)

Íme egy parancssori minta tábla API GA tábla API preview importálhatja:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>AzCopy parancs

Az AzCopy parancssori segédprogram használata áttelepítéséhez a másik lehetőség az Azure Cosmos DB tábla API-t az Azure Table storage adatokat. AzCopy használatához először exportálnia az adatok a [exportál adatokat az a Table storage](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), majd adatok importálása az Azure Cosmos DB leírtak [Azure Cosmos DB tábla API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Az importálás az Azure Cosmos DB, tekintse meg a következő mintát. Vegye figyelembe, hogy a/dest cosmosdb, nem core használja.

Példa importálási parancs:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Tábla API tábla API (előzetes verzió) áttelepítése

> [!WARNING]
> Ha azt szeretné, azonnal teszik az általánosan elérhető táblák, majd telepítse át a meglévő preview megadott e szakasz táblázatai, ellenkező esetben azt fogja kell automatikus-áttelepítésekkel hajthatja végre a meglévő-előnézet ügyfeleknek az elkövetkező hetektől, vegye figyelembe azonban, hogy automatikus áttelepített preview táblák kell bizonyos korlátozások, amely az újonnan létrehozott nem táblák lesz rájuk.
> 

A tábla API mostantól általánosan elérhető (GA). A kép és a táblák, mind a kódban, a felhőben, valamint az ügyfélen futó kódot futtató GA verziói között különbségek vannak. Ezért nem javasoljuk megpróbálja preview SDK-ügyfél kombinálhatók GA tábla API-fiókkal, és ez fordítva is igaz. Tábla API preview felhasználók továbbra is használhatja a meglévő táblák, de az éles környezetben kell a GA környezet át az előzetes kiadásban, vagy várja meg, automatikus áttelepítéséhez. Ha automatikus áttelepítéséhez vár, a rendszer értesíti, az áttelepített táblák vonatkozó korlátozások. Az áttelepítés után meg tudják korlátozás nélkül a meglévő fiókot hozzon létre új táblákat (csak az áttelepített táblák korlátozások lesz).

Az általánosan elérhető tábla API áttelepítésére tábla API (előzetes verzió):

1. Hozzon létre egy új Azure Cosmos DB fiókot és annak API típusát az Azure tábla leírtak [adatbázisfiók létrehozása](create-table-dotnet.md#create-a-database-account).

2. Az ügyfelek számára az egy GA kiadásában módosítása a [tábla API SDK-k](table-sdk-dotnet.md).

3. Az ügyfél adatok áttelepítését preview táblázatokból GA táblák az adatáttelepítés eszközzel. Ismerteti az adatok áttelepítési eszköz használatával erre a célra [adatáttelepítési eszközét](#data-migration-tool). 

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Az adatáttelepítés eszközzel adatok importálása
> * Adatok importálása az AzCopy
> * Tábla API tábla API (előzetes verzió) áttelepítése

Most már továbbléphet a következő oktatóanyagot, és megtudhatja, hogyan kérdezhet le adatokat az Azure Cosmos DB tábla API-val. 

> [!div class="nextstepaction"]
>[Hogyan kérdezhet le adatokat?](../cosmos-db/tutorial-query-table.md)
