---
title: Adatbázis-áttelepítési eszköz Azure Cosmos DB-hez | Microsoft Docs
description: Ez a dokumentum ismerteti, hogy hogyan telepíthet át adatokat az Azure Cosmos DB nyílt forráskódú adatáttelepítési eszközeivel az Azure Cosmos DB-be különféle forrásokból, például MongoDB-, SQL Server-, Table Storage- és Amazon DynamoDB-adatbázisokból, illetve CSV- és JSON-fájlokból. A CSV-fájlokat JSON formátumba konvertálhatja.
keywords: csv–json, adatbázis-áttelepítési eszközök, csv konvertálása json formátumba
services: cosmos-db
author: deborahc
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 03/30/2018
ms.author: dech
ms.custom: mvc
ms.openlocfilehash: 43092a12535ed18a5c91f924e3fdf72ad6d47d7b
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "41918345"
---
# <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB: Adatáttelepítési eszköz

Ez az oktatóanyag bemutatja az Azure Cosmos DB adatáttelepítési eszközének használatát, mellyel adatokat importálhat különböző forrásokból Azure Cosmos DB-gyűjteményekbe és -táblákba. Importálhat JSON- és CSV-fájlokat, SQL-, MongoDB-, Azure Table Storage- és Amazon DynamoDB-adatbázisokat, illetve akár Azure Cosmos DB SQL API-gyűjteményeket is. Az adatokat gyűjteményekbe és táblákba helyezheti az Azure Cosmos DB-ben való használathoz. Az adatáttelepítési eszközzel emellett SQL API-beli egypartíciós gyűjteményt is telepíthet át többpartíciós gyűjteménybe.

Melyik API-t szeretné használni az Azure Cosmos DB-vel? 
* **[SQL API](documentdb-introduction.md)** – Az adatáttelepítési eszközben elérhető bármelyik forráslehetőséggel importálhat adatokat.
* **[Table API](table-introduction.md)** – Az adatok importálásához használhatja az adatáttelepítési eszközt vagy az AzCopy segédprogramot. További információért olvassa el az [Adatok importálása az Azure Cosmos DB Table API-val való használathoz](table-import.md) című témakört.
* **[MongoDB API](mongodb-introduction.md)** – Az adatáttelepítési eszköz jelenleg nem támogatja az Azure Cosmos DB MongoDB API-t sem forrásként, sem célként. Ha MongoDB API-gyűjteményekbe vagy azokból máshova szeretne adatokat áttelepíteni az Azure Cosmos DB-ben, ehhez utasításokat az [Azure Cosmos DB: Adatok áttelepítése a MongoDB API-val](mongodb-migrate.md) című cikkben találhat. Az adatáttelepítési eszközzel azonban exportálhat adatokat MongoDB-ből Azure Cosmos DB SQL API-gyűjteményekbe az SQL API-val való használathoz. 
* **[Graph API](graph-introduction.md)** – Az adatáttelepítési eszköz importálási eszközként való használata jelenleg nem támogatott a Graph API-fiókok esetében. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az adatáttelepítési eszköz telepítése
> * Adatok importálása különböző adatforrásokból
> * Exportálás Azure Cosmos DB-ből JSON-ba

## <a id="Prerequisites"></a>Előfeltételek
A jelen cikkben lévő utasítások követése előtt győződjön meg róla, hogy telepítette az alábbiakat:

* [Microsoft .NET-keretrendszer 4.51](https://www.microsoft.com/download/developer-tools.aspx) vagy újabb

* Átviteli sebesség növelése: Az adatáttelepítés időtartamát az egyéni gyűjteményhez vagy a gyűjteménycsoporthoz beállított átviteli sebesség határozza meg. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Az átviteli sebesség Azure Portalon való növeléséről bővebben Az Azure Cosmos DB teljesítményszintjei és tarifacsomagjai című cikkben olvashat.

## <a id="Overviewl"></a>Áttekintés
Az adatáttelepítési eszköz egy nyílt forráskódú megoldás, mellyel adatokat importálhat az Azure Cosmos DB-be különféle forrásokból, ideértve az alábbiakat:

* JSON-fájlok
* MongoDB
* SQL Server
* CSV-fájlok
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure Cosmos DB-gyűjtemények

Az importálási eszköz tartalmaz egy grafikus felhasználói felületet (dtui.exe), de használhatja a parancssori verziót is (dt.exe). Akár azt is megteheti, hogy beállítja az importálást a grafikus felhasználói felületen, majd megjeleníti a vonatkozó parancsot. A táblázatos adatforrások (például SQL Server-adatbázisok vagy CSV-fájlok) átalakíthatók úgy, hogy hierarchikus kapcsolatok (aldokumentumok) jöjjenek létre az importálás során. Az alábbiakban többet is megtudhat a választható forrásokról, mintaparancsokat találhat az egyes forrástípusokból való importáláshoz, és áttekintheti az importálás eredményét.

## <a id="Install"></a>Telepítés
Az áttelepítési eszköz forráskódját elérheti [ebben a GitHub-adattárban](https://github.com/azure/azure-documentdb-datamigrationtool). Letöltheti a forráskódot, és lefordíthatja helyben a megoldást, vagy [letöltheti az előre lefordított bináris fájlokat is](https://cosmosdbportalstorage.blob.core.windows.net/datamigrationtool/2018.02.28-1.8.1/dt-1.8.1.zip). Ezután futtathatja az alábbiak egyikét:

* **Dtui.exe**: Az eszköz grafikus felhasználói felületet használó verziója.
* **Dtui.exe**: Az eszköz grafikus parancssori verziója.

## <a name="select-data-source"></a>Adatforrás kiválasztása

Az eszköz telepítése után megkezdheti az adatok importálását. Milyen típusú adatokat szeretne importálni?

* [JSON-fájlok](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB-exportfájlok](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-fájlok](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos DB-gyűjtemények](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB – tömeges importálás](#SQLBulkImport)
* [Azure Cosmos DB – szekvenciális rekordimportálás](#DocumentDSeqTarget)


## <a id="JSON"></a>JSON-fájlok importálása
Forrásként JSON-fájlokat választva importálhat egy vagy több egydokumentumos JSON-fájlt, illetve olyan JSON-fájlokat, amelyek mindegyike JSON-dokumentumok egy tömbjét tartalmazza. Ha JSON-fájlokat tartalmazó mappát ad meg az importáláshoz, akkor választhatja az almappákban lévő fájlok rekurzív megkeresését.

![Képernyőkép a JSON-fájlok importálási beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/jsonsource.png)

Néhány parancssori példa JSON-fájlok importálásához:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Importálás MongoDB-ből

> [!IMPORTANT]
> Ha MongoDB-t támogató Azure Cosmos DB-fiókba importál adatokat, kövesse [ezeket az utasításokat](mongodb-migrate.md).
> 
> 

Forrásként MongoDB-t választva importálhat adatokat egy adott MongoDB-gyűjteményből, és opcionálisan szűrheti a dokumentumokat egy lekérdezéssel és/vagy módosíthatja a dokumentumstruktúrát egy leképezés használatával.  

![Képernyőkép a MongoDB forrás beállításairól](./media/import-data/mongodbsource.png)

A kapcsolati sztring a szabványos MongoDB-formátumot követi:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott MongoDB-példány.
> 
> 

Adja meg annak a gyűjteménynek a nevét, amelyből adatokat kíván importálni. Igény esetén adhat meg manuálisan vagy fájlként egy lekérdezést (például {pop: {$gt:5000}} ) és/vagy egy leképezést (például {loc:0} ), melyekkel szűrheti és alakíthatja az importálandó adatokat.

Néhány parancssori példa MongoDB-adatbázisból való importáláshoz:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>MongoDB-exportfájlok importálása

> [!IMPORTANT]
> Ha MongoDB-t támogató Azure Cosmos DB-fiókba importál adatokat, kövesse [ezeket az utasításokat](mongodb-migrate.md).
> 
> 

Forrásként MongoDB-exportálási JSON-fájlokat választva importálhat egy vagy több olyan JSON-fájlt, amelyet a mongoexport segédprogrammal hozott létre.  

![Képernyőkép a MongoDB-exportfájlok importálási beállításairól](./media/import-data/mongodbexportsource.png)

Ha MongoDB-exportálási JSON-fájlokat tartalmazó mappát ad meg az importáláshoz, akkor választhatja az almappákban lévő fájlok rekurzív megkeresését.

Néhány parancssori példa MongoDB-exportálási JSON-fájlok importáláshoz:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Importálás SQL Server-adatbázisból
Forrásként SQL-t választva importálhat egy adott SQL Server-adatbázist, és opcionálisan szűrheti az importálandó rekordokat egy lekérdezéssel. Emellett módosíthatja a dokumentumstruktúrát egy beágyazási elválasztó megadásával (erre alább visszatérünk még).  

![Képernyőkép az SQL forrás beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/sqlexportsource.png)

A kapcsolati sztring a szabványos SQL-kapcsolati sztringek formátumát követi.

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott SQL Server-példány.
> 
> 

A beágyazási elválasztó tulajdonsággal hierarchikus kapcsolatokat (aldokumentumokat) hozhat létre az importálás során. Vizsgálja meg a következő SQL-lekérdezést:

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Mely az alábbi (részleges) eredményt adja vissza:

![Képernyőkép az SQL-lekérdezés eredményéről](./media/import-data/sqlqueryresults.png)

Megfigyelheti az Address.AddressType és az Address.Location.StateProvinceName áljelet. A „.” beágyazási elválasztó megadása miatt az importáló eszköz létrehozza az Address és az Address.Location aldokumentumot az importálás során. Ez például az alábbi Azure Cosmos DB-beli végleges dokumentumot eredményezheti:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Néhány parancssori példa SQL Server-adatbázisból való importáláshoz:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>CSV-fájlok importálása és CSV-fájlok konvertálása JSON formátumba
Forrásként CSV-fájlt választva importálhat egy vagy több CSV-fájlt. Ha CSV-fájlokat tartalmazó mappát ad meg az importáláshoz, akkor választhatja az almappákban lévő fájlok rekurzív megkeresését.

![Képernyőkép a CSV forrás beállításairól – CSV konvertálása JSON formátumba](media/import-data/csvsource.png)

Az SQL forráshoz hasonlóan itt is használhatja a beágyazási elválasztó tulajdonságot, ha hierarchikus kapcsolatokat (aldokumentumokat) kíván létrehozni az importálás során. Vizsgálja meg például az alábbi CSV-fejlécsort és -adatsorokat:

![Képernyőkép a CSV-mintarekordokról – CSV konvertálása JSON formátumba](./media/import-data/csvsample.png)

Megfigyelheti a DomainInfo.Domain_Name és a RedirectInfo.Redirecting áljelet. A „.” beágyazási elválasztó megadása miatt az importáló eszköz létrehozza a DomainInfo és a RedirectInfo aldokumentumot az importálás során. Ez például az alábbi Azure Cosmos DB-beli végleges dokumentumot eredményezheti:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Az importálási eszköz megkísérli kikövetkeztetni a CSV-fájlokban lévő nem idézőjeles értékek típusát (az idézőjelbe foglalt értékeket mindig sztringként kezeli).  A típusokat a következő sorrendben azonosítja: szám, datetime, boolean.  

Két további dolgot is fontos tudnia a CSV-importálásról:

1. Az eszköz a nem idézőjeles értékekről alapértelmezés szerint mindig levágja a tabulátor- és szóközkaraktereket, míg az idézőjeles értékeket pontosan megőrzi. Ezt a viselkedést felülbírálhatja a „Trim quoted values” (Idézőjeles értékek csonkolása) jelölőnégyzettel vagy a /s.TrimQuoted parancssori kapcsolóval.
2. Alapértelmezés szerint a nem idézőjeles nullértéket null értékként kezeli az eszköz. Ezt a viselkedést felülbírálhatja (vagyis a nem idézőjeles nullértéket egy „null” sztringként kezelheti) a „Treat unquoted NULL as string” (Nem idézőjeles NULL kezelése sztringként) jelölőnégyzettel vagy a /s.NoUnquotedNulls parancssori kapcsolóval.

Parancssori példa CSV-fájl importáláshoz:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Importálás Azure Table Storage-ből
Forrás Azure Table Storage-et választva importálhat egy adott Azure Table Storage-táblát. Igény esetén szűrheti is az importálandó táblaentitásokat. 

Az Azure Table Storage-ből importált adatokat elhelyezheti Azure Cosmos DB-táblákban és -entitásokban a Table API-val való használathoz, vagy gyűjteményekben és dokumentumokban az SQL API-val való használathoz. A Table API azonban csak a parancssori segédprogramban érhető el célként, vagyis nem tud Table API-ba exportálni az adatáttelepítési eszköz grafikus felhasználói felületén keresztül. További információért olvassa el az [Adatok importálása az Azure Cosmos DB Table API-val való használathoz](table-import.md) című témakört. 

![Képernyőkép az Azure Table Storage forrás beállításairól](./media/import-data/azuretablesource.png)

Az Azure Table Storage kapcsolati sztringjének formátuma a következő:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Table Storage-példány.
> 
> 

Adja meg annak az Azure-táblának a nevét, amelyből importálni kíván. Igény esetén adhat meg egy [szűrőt](../vs-azure-tools-table-designer-construct-filter-strings.md) is.

Az Azure Table Storage importálási forráshoz a következő további beállításokat adhatja meg:

1. Include Internal Fields (Belső mezők belefoglalása)
   1. All (Mind) – Az összes belső mező (PartitionKey, RowKey és Timestamp) belefoglalása
   2. None (Egyik sem) – Az összes belső mező kizárása
   3. RowKey – Csak a RowKey mező belefoglalása
2. Select Columns (Oszlopok kijelölése)
   1. Az Azure Table Storage-szűrők nem támogatják a leképezéseket. Ha csak bizonyos Azure Table Storage-entitástulajdonságokat szeretne importálni, adja őket hozzá a Select Columns listához. Az összes többi entitástulajdonságot figyelmen kívül hagyja az eszköz.

Parancssori példa Azure Table Storage-ből való importáláshoz:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Importálás Amazon DynamoDB-ből
Forrásként Amazon DynamoDB-t választva importálhat egy adott Amazon DynamoDB-táblát, és opcionálisan szűrheti az importálandó entitásokat. Számos beépített sablon a rendelkezésére áll, hogy a lehető legegyszerűbben beállíthassa az importálást.

![Képernyőkép az Amazon DynamoDB forrás beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/dynamodbsource1.png)

![Képernyőkép az Amazon DynamoDB forrás beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/dynamodbsource2.png)

Az Amazon DynamoDB kapcsolati sztringjének formátuma a következő:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Amazon DynamoDB-példány.
> 
> 

Parancssori példa Amazon DynamoDB-ből való importáláshoz:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Importálás Azure Blob Storage-ből
Forrásként JSON-fájlt, MongoDB-exportfájlt vagy CSV-fájlt választva importálhat egy vagy több Azure Blob Storage-beli fájlt. Miután megadta a blobtároló URL-címét és fiókkulcsát, adjon meg egy reguláris kifejezést is az importálandó fájlok kijelöléséhez.

![Képernyőkép a Blob forrás beállításairól](./media/import-data/blobsource.png)

Parancssori példa Azure Blob Storage-beli JSON-fájlok importáláshoz:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="SQLSource"></a>Importálás egy SQL API-gyűjteményből
Forrásként Azure Cosmos DB-t választva importálhat adatokat egy vagy több Azure Cosmos DB-gyűjteményből, és opcionálisan szűrheti a dokumentumokat egy lekérdezéssel.  

![Képernyőkép az Azure Cosmos DB forrás beállításairól](./media/import-data/documentdbsource.png)

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Az Azure Cosmos DB-fiók kapcsolati sztringjét beszerezheti az Azure Portal Kulcsok lapjáról az [Azure Cosmos DB-fiók kezelése](manage-account.md) című cikkben ismertetett módon, az adatbázis nevét azonban hozzá kell fűznie a kapcsolati sztringhez a következő formátumot követve:

    Database=<CosmosDB Database>;

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.
> 
> 

Egyetlen Azure Cosmos DB-gyűjtemény importálásához írja be annak a gyűjteménynek a nevét, amelyből importálni kívánja az adatokat. Ha több Azure Cosmos DB-gyűjteményből szeretne importálni, adjon meg egy olyan reguláris kifejezést, amely illeszkedik egy vagy több gyűjteménynévre (például gyűjtemény01 | gyűjtemény02 | gyűjtemény03). Igény esetén adhat meg manuálisan vagy fájlként egy lekérdezést, mellyel szűrheti és alakíthatja az importálandó adatokat.

> [!NOTE]
> Mivel a gyűjteménymező támogatja a reguláris kifejezéseket, ha egyetlen olyan gyűjteményből importál, amelynek a neve tartalmaz a reguláris kifejezésekben használt speciális karaktereket, akkor ezeket a karaktereket escape-karakterrel kell ellátnia.
> 
> 

Az Azure Cosmos DB importálási forráshoz a következő speciális beállításokat adhatja meg:

1. Include Internal Fields (Belső mezők belefoglalása): Meghatározza, hogy az exportálás tartalmazza-e az Azure Cosmos DB dokumentumrendszerének tulajdonságait (például _rid, _ts).
2. Number of Retries on Failure (Újrapróbálkozások számára sikertelenség esetén): Meghatározza, hogy az eszköz hány alkalommal kísérelje meg ismét az Azure Cosmos DB-hez való csatlakozást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
3. Retry Interval (Újrapróbálkozások időköze): Meghatározza, hogy az eszköz mennyi ideig várjon, mielőtt ismét megkísérli az Azure Cosmos DB-hez való kapcsolódást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
4. Connection Mode (Kapcsolatmód): Meghatározza az Azure Cosmos DB-hez használandó kapcsolatmódot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

![Képernyőkép az Azure Cosmos DB forrás speciális beállításairól](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.
> 
> 

Néhány parancssori példa Azure Cosmos DB-ből való importáláshoz:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Az Azure Cosmos DB adatimportálási eszköze támogatja az adatok [Azure Cosmos DB Emulatorból](local-emulator.md) való importálását is. Adatok helyi emulátorból való importálásakor állítsa a végpontot a `https://localhost:<port>` értékre. 
> 
> 

## <a id="HBaseSource"></a>Importálás HBase-ből
Forrásként HBase-t választva importálhat adatokat egy HBase-táblából, és opcionálisan szűrheti az adatokat. Számos beépített sablon a rendelkezésére áll, hogy a lehető legegyszerűbben beállíthassa az importálást.

![Képernyőkép a HBase forrás beállításairól](./media/import-data/hbasesource1.png)

![Képernyőkép a HBase forrás beállításairól](./media/import-data/hbasesource2.png)

A HBase Stargate kapcsolati sztringjének formátuma a következő:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott HBase-példány.
> 
> 

Parancssori példa HBase-ből való importáláshoz:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="SQLBulkTarget"></a>Importálás SQL API-ba (tömeges importálás)
Az Azure Cosmos DB tömeges importálójával egy Azure Cosmos DB-beli tárolt eljárás segítségével hatékonyabban importálhat bármelyik választható forrásból. Az eszköz támogatja az egyetlen egypartíciós Azure Cosmos DB-gyűjteménybe való importálást, illetve a particionált importálást is, mely esetén az adatok több egypartíciós Azure Cosmos DB-gyűjteményre kiterjedően vannak particionálva. Az adatok particionálásával kapcsolatban további információkat az [Azure Cosmos DB particionálási és méretezési eljárásait](partition-data.md) ismertető cikkben talál. Az eszköz létrehozza, végrehajtja, majd törli a tárolt eljárást a célgyűjtemény(ek)ben.  

![Képernyőkép az Azure Cosmos DB tömeges importálási beállításairól](./media/import-data/documentdbbulk.png)

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Az Azure Cosmos DB-fiók kapcsolati sztringjét beszerezheti az Azure Portal Kulcsok lapjáról az [Azure Cosmos DB-fiók kezelése](manage-account.md) című cikkben ismertetett módon, az adatbázis nevét azonban hozzá kell fűznie a kapcsolati sztringhez a következő formátumot követve:

    Database=<CosmosDB Database>;

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.
> 
> 

Egyetlen gyűjtemény importálásához írja be annak a gyűjteménynek a nevét, amelyből importálni kívánja az adatokat, majd kattintson az Add (Hozzáadás) gombra. Több gyűjtemény importálásához írja be külön-külön a gyűjtemények nevét, vagy használja a következő szintaxist: *gyűjtemény_előtagja*[kezdő index - befejező index]. Ha az utóbbi módszerrel ad meg több gyűjteményt, vegye figyelembe a következőket:

1. Csak az integer adattípuson alapuló minták támogatottak. A „gyűjtemény[0-3]” minta például a következő gyűjteményeket hozza létre: gyűjtemény0, gyűjtemény1, gyűjtemény2, gyűjtemény3.
2. Használhat rövidített szintaxist: A „gyűjtemény[3]” minta ugyanúgy az 1. lépésben említett gyűjteménykészletet hozza létre.
3. Adhat meg több behelyettesítést is. A „gyűjtemény[0-1] [0-9]” minta például 20 gyűjteménynevet generál kezdő nulla használatával (gyűjtemény01, ..02, ..03).

A gyűjteménynév (vagy nevek) megadása után válassza ki a gyűjtemény(ek)hez használni kívánt átviteli sebességet (400 – 10 000 kérelemegység). Nagyobb importálási teljesítményhez válasszon magasabb átviteli sebességet. A teljesítményszintekről bővebben [Az Azure Cosmos DB teljesítményszintjei](performance-levels.md) című témakörben tájékozódhat.

> [!NOTE]
> A beállított átviteli sebesség csak a gyűjtemény létrehozásakor érvényesül. Ha a megadott gyűjtemény már létezik, akkor annak átviteli sebessége nem módosul.
> 
> 

Több gyűjtemény importálásakor az importálási eszköz támogatja a kivonatalapú particionálást. Ilyen esetekben adja meg a használni kívánt dokumentumtulajdonságot a Partition Key (Particionálási kulcs) mezőben (ha a Partition Key mezőt üresen hagyja, akkor az eszköz a dokumentumokat véletlenszerűen particionálja a célgyűjteményekben).

Igény esetén megadhatja, hogy az importálási forrás melyik mezőjét használja a rendszer az Azure Cosmos DB dokumentumazonosító tulajdonságaként az importálás során (ha a dokumentumok nem tartalmazzák ezt a tulajdonságot, akkor az importálási eszköz létrehoz egy egyedi azonosítót e tulajdonság értékeként).

Elérhető néhány speciális beállítás is importáláskor. Például az eszköz tartalmaz egy alapértelmezett tömeges importálási tárolt eljárást (BulkInsert.js), de adhat meg helyette saját importálási tárolt eljárást is:

 ![Képernyőkép az Azure Cosmos DB tömeges beszúrási tárolt eljárásának beállításáról](./media/import-data/bulkinsertsp.png)

Emellett dátum típusú értékek importálásakor (például SQL Serverből vagy MongoDB-ből) három különböző importálási lehetőség közül választhat:

 ![Képernyőkép az Azure Cosmos DB importálási dátum- és időbeállításáról](./media/import-data/datetimeoptions.png)

* String (Sztring): Megőrzés sztringértékként
* Epoch (Alapidőszak): Megőrzés Epoch típusú számértékként
* Both (Mindkettő): Megőrzés mind sztringként, mind pedig Epoch típusú számértékként. Ez a beállítás létrehoz egy aldokumentumot, például: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Az Azure Cosmos DB tömeges importálási eszköze a következő speciális beállításokat támogatja:

1. Batch Size (Kötegméret): Az alapértelmezett kötegméret 50.  Nagyméretű dokumentumok importálásakor célszerű csökkenteni a kötegméretet, kisméretű dokumentumok importálásakor pedig növelni a kötegméretet.
2. Max Script Size (bytes) (Maximális szkriptfájlméret (bájt)): Az alapértelmezett maximális szkriptfájlméret 512 KB.
3. Disable Automatic Id Generation (Automatikus azonosítógenerálás letiltása): Ha az összes importálandó dokumentum tartalmaz egy azonosítómezőt, akkor ennek a beállításnak a választása felgyorsíthatja az importálást. Az egyedi azonosítómezőt nem tartalmazó dokumentumokat nem importálja az eszköz.
4. Update Existing Documents (Meglévő dokumentumok frissítése): Az eszköz alapértelmezés szerint nem cseréli le a meglévő dokumentumokat azonosítóütközés esetén. Ennek a beállításnak a választásával felülírhatja a meglévő dokumentumokat az azonos azonosítójú importált dokumentumokkal. Ez a funkció a meglévő dokumentumokat frissítő ütemezett adatáttelepítésekhez hasznos.
5. Number of Retries on Failure (Újrapróbálkozások számára sikertelenség esetén): Meghatározza, hogy az eszköz hány alkalommal kísérelje meg ismét az Azure Cosmos DB-hez való csatlakozást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
6. Retry Interval (Újrapróbálkozások időköze): Meghatározza, hogy az eszköz mennyi ideig várjon, mielőtt ismét megkísérli az Azure Cosmos DB-hez való kapcsolódást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
7. Connection Mode (Kapcsolatmód): Meghatározza az Azure Cosmos DB-hez használandó kapcsolatmódot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

![Képernyőkép az Azure Cosmos DB speciális tömeges importálási beállításairól](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.
> 
> 

## <a id="SQLSeqTarget"></a>Importálás az SQL API-ba (szekvenciális rekordimportálás)
Az Azure Cosmos DB szekvenciális rekordimportálójával rekordról rekordra haladva importálhat bármelyik választható forrásból. Ez a lehetőség akkor lehet hasznos, ha olyan meglévő gyűjteménybe importál, amelynek már kimerült a tárolt eljárási kvótája. Az eszköz támogatja az egyetlen (egypartíciós vagy többpartíciós) Azure Cosmos DB-gyűjteménybe való importálást, illetve a particionált importálást is, mely esetén az adatok több egypartíciós és/vagy többpartíciós Azure Cosmos DB-gyűjteményre kiterjedően vannak particionálva. Az adatok particionálásával kapcsolatban további információkat az [Azure Cosmos DB particionálási és méretezési eljárásait](partition-data.md) ismertető cikkben talál.

![Képernyőkép az Azure Cosmos DB szekvenciális rekordimportálási beállításairól](./media/import-data/documentdbsequential.png)

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

Az Azure Cosmos DB-fiók kapcsolati sztringjét beszerezheti az Azure Portal Kulcsok lapjáról az [Azure Cosmos DB-fiók kezelése](manage-account.md) című cikkben ismertetett módon, az adatbázis nevét azonban hozzá kell fűznie a kapcsolati sztringhez a következő formátumot követve:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.
> 
> 

Egyetlen gyűjtemény importálásához írja be annak a gyűjteménynek a nevét, amelynek az adatait importálni kívánja, és kattintson az Add (Hozzáadás) gombra. Több gyűjtemény importálásához írja be külön-külön a gyűjtemények nevét, vagy használja a következő szintaxist: *gyűjtemény_előtagja*[kezdő index - befejező index]. Ha az utóbbi módszerrel ad meg több gyűjteményt, vegye figyelembe a következőket:

1. Csak az integer adattípuson alapuló minták támogatottak. A „gyűjtemény[0-3]” minta például a következő gyűjteményeket hozza létre: gyűjtemény0, gyűjtemény1, gyűjtemény2, gyűjtemény3.
2. Használhat rövidített szintaxist: A „gyűjtemény[3]” minta ugyanúgy az 1. lépésben említett gyűjteménykészletet hozza létre.
3. Adhat meg több behelyettesítést is. A „gyűjtemény[0-1] [0-9]” minta például 20 gyűjteménynevet hoz létre kezdő nulla használatával (gyűjtemény01, ..02, ..03).

A gyűjteménynév (vagy nevek) megadása után válassza ki a gyűjtemény(ek)hez használni kívánt átviteli sebességet (400 – 250 000 kérelemegység). Nagyobb importálási teljesítményhez válasszon magasabb átviteli sebességet. A teljesítményszintekről bővebben [Az Azure Cosmos DB teljesítményszintjei](performance-levels.md) című témakörben tájékozódhat. A 10 000 kérelemegységnél magasabb átviteli sebességű gyűjteményekbe való importáláshoz partíciókulcsot kell használnia. Ha 250 000 kérelemegységet meghaladó átviteli sebességet kíván használni, akkor külön kérelmeznie kell a portálon a növelés végrehajtását.

> [!NOTE]
> A beállított átviteli sebesség csak a gyűjtemény vagy adatbázis létrehozásakor érvényesül. Ha a megadott gyűjtemény már létezik, akkor annak átviteli sebessége nem módosul.
> 
> 

Több gyűjtemény importálásakor az importálási eszköz támogatja a kivonatalapú particionálást. Ilyen esetekben adja meg a használni kívánt dokumentumtulajdonságot a Partition Key (Particionálási kulcs) mezőben (ha a Partition Key mezőt üresen hagyja, akkor az eszköz a dokumentumokat véletlenszerűen particionálja a célgyűjteményekben).

Igény esetén megadhatja, hogy az importálási forrás melyik mezőjét használja a rendszer az Azure Cosmos DB dokumentumazonosító tulajdonságaként az importálás során (ha a dokumentumok nem tartalmazzák ezt a tulajdonságot, akkor az importálási eszköz létrehoz egy egyedi azonosítót e tulajdonság értékeként).

Elérhető néhány speciális beállítás is importáláskor. Dátum típusú értékek importálásakor (például SQL Serverből vagy MongoDB-ből) három különböző importálási lehetőség közül választhat:

 ![Képernyőkép az Azure Cosmos DB importálási dátum- és időbeállításáról](./media/import-data/datetimeoptions.png)

* String (Sztring): Megőrzés sztringértékként
* Epoch (Alapidőszak): Megőrzés Epoch típusú számértékként
* Both (Mindkettő): Megőrzés mind sztringként, mind pedig Epoch típusú számértékként. Ez a beállítás létrehoz egy aldokumentumot, például: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Az Azure Cosmos DB szekvenciális rekordimportáló eszköze a következő speciális beállításokat támogatja:

1. Number of Parallel Requests (Párhuzamos kérelmek száma): A párhuzamos kérelmek alapértelmezett száma kettő. Kisméretű dokumentumok importálásakor célszerű növelni a párhuzamos kérelmek számát. Ha azonban túl nagy értéket ad meg, akkor a rendszer korlátozhatja az importálás sebességét.
2. Disable Automatic Id Generation (Automatikus azonosítógenerálás letiltása): Ha az összes importálandó dokumentum tartalmaz egy azonosítómezőt, akkor ennek a beállításnak a választása felgyorsíthatja az importálást. Az egyedi azonosítómezőt nem tartalmazó dokumentumokat nem importálja az eszköz.
3. Update Existing Documents (Meglévő dokumentumok frissítése): Az eszköz alapértelmezés szerint nem cseréli le a meglévő dokumentumokat azonosítóütközés esetén. Ennek a beállításnak a választásával felülírhatja a meglévő dokumentumokat az azonos azonosítójú importált dokumentumokkal. Ez a funkció a meglévő dokumentumokat frissítő ütemezett adatáttelepítésekhez hasznos.
4. Number of Retries on Failure (Újrapróbálkozások számára sikertelenség esetén): Meghatározza, hogy az eszköz hány alkalommal kísérelje meg ismét az Azure Cosmos DB-hez való csatlakozást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
5. Retry Interval (Újrapróbálkozások időköze): Meghatározza, hogy az eszköz mennyi ideig várjon, mielőtt ismét megkísérli az Azure Cosmos DB-hez való kapcsolódást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
6. Connection Mode (Kapcsolatmód): Meghatározza az Azure Cosmos DB-hez használandó kapcsolatmódot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

![Képernyőkép az Azure Cosmos DB speciális szekvenciálisrekord-importálási beállításairól](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.
> 
> 

## <a id="IndexingPolicy"></a>Indexelési házirend megadása
Ha engedélyezi az áttelepítési eszköznek az Azure Cosmos DB SQL API-gyűjtemények létrehozását az importálás során, megadhatja a gyűjtemények indexelési házirendjét. Az Azure Cosmos DB tömeges importálójának és az Azure Cosmos DB szekvenciális rekordimportálójának speciális beállításokat tartalmazó területén keresse meg az Indexing Policy (Indexelési házirend) szakaszt.

![Képernyőkép az Azure Cosmos DB indexelési házirendjének speciális beállításairól](./media/import-data/indexingpolicy1.png)

Az Indexing Policy speciális beállításnál megadhatja az indexelési házirend fájlját, írhat be manuálisan egy indexelési házirendet, valamint az alapértelmezetten elérhető sablonok közül is választhat egyet (az indexelési házirend mezőjére jobb gombbal kattintva).

Az eszköz az alábbi házirendsablonokat tartalmazza beépítve:

* Default (Alapértelmezett): Ez a házirend sztringeken végzett egyenlőségalapú lekérdezésekhez, illetve számokon végzett ORDER BY-, tartomány- és egyenlőségalapú lekérdezésekhez ideális. Alacsonyabb indextárolási terhelést nyújt, mint a Range házirend.
* Range (Tartomány): Ez a házirend akkor ideális, ha mind számokon, mind pedig sztringeken végez ORDER BY-, tartomány- és egyenlőségalapú lekérdezéseket. Magasabb indextárolási terheléssel jár, mint a Default és a Hash házirend.

![Képernyőkép az Azure Cosmos DB indexelési házirendjének speciális beállításairól](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Ha nem ad meg indexelési házirendet, az eszköz az alapértelmezett házirendet alkalmazza. Az indexelési házirendekről bővebben [Az Azure Cosmos DB indexelési házirendjei](indexing-policies.md) című cikkben tájékozódhat.
> 
> 

## <a name="export-to-json-file"></a>Exportálás JSON-fájlba
Az Azure Cosmos DB JSON-exportálójával a választható forrástípusok bármelyikét exportálhatja egy JSON-dokumentumtömböt tartalmazó JSON-fájlba. Az eszköz végrehajthatja Ön helyett az exportálást, vagy ha szeretné, akkor megtekintheti a kapott áttelepítési parancsot, és futtathatja azt önállóan. A művelet során kapott JSON-fájlt tárolhatja helyben vagy Azure Blob Storage-fiókban.

![Képernyőkép az Azure Cosmos DB helyi JSON-fájlba való exportálási beállításáról](./media/import-data/jsontarget.png)

![Képernyőkép az Azure Cosmos DB Azure Blob Storage-beli JSON-fájlba való exportálási beállításáról](./media/import-data/jsontarget2.png)

Választhatja az eredményként kapott JSON-fájl emberi olvasásra való formázását is, de tartsa szem előtt, hogy ez megnöveli a dokumentumméretet.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Speciális konfiguráció
Adja meg a speciális konfigurációs képernyőn annak a naplófájlnak a helyét, amelybe az esetlegesen előforduló hibákat szeretné menteni. Az alábbi szabályok érvényesek erre a lapra:

1. Ha nem ad meg fájlnevet, akkor az összes hibát megjeleníti az eszköz az eredménylapon.
2. Ha könyvtárnév nélküli fájlnevet ad meg, akkor a fájlt az eszköz az aktuális környezeti könyvtárban hozza létre (vagy írja felül).
3. Ha meglévő fájlt választ, akkor a fájlt felülírja az eszköz, mivel a hozzáfűzést nem támogatja.
4. Ezután adja meg, hogy az eszköz mit naplózzon: az összes üzenetet, csak a kritikus üzeneteket vagy egyetlen hibaüzenetet sem. Végül döntse el, hogy milyen gyakorisággal frissüljön a képernyőn látható átviteli üzenet a művelet előrehaladása során.

   ![A Speciális konfiguráció képernyő képernyőképe](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Importálási beállítások megerősítése és a parancssor megtekintése
1. Miután megadta a forrásadatokat, a céladatokat és a speciális konfigurációt, tekintse át az áttelepítés összegzését, és igény esetén ellenőrizze vagy másolja a kapott áttelepítési parancsot (a parancs másolása hasznos lehet az importálási műveletek automatizálásához).
   
    ![Képernyőkép az összesítő képernyőről](./media/import-data/summary.png)
   
    ![Képernyőkép az összesítő képernyőről](./media/import-data/summarycommand.png)
2. Ha elégedett a forrás- és célbeállításokkal, válassza az **Import** (Importálás) gombot. Az importálás előrehaladása során figyelemmel kísérheti a képernyőn az eltelt időt, az átvitt elemek számát és a hibákkal kapcsolatos információkat (ha nem adott meg fájlnevet a speciális konfigurációs lapon). Ha a művelet kész, exportálhatja az eredményt (például a sikertelen importálású elemek feldolgozásához).
   
    ![Képernyőkép az Azure Cosmos DB JSON-fájlba való exportálási beállításáról](./media/import-data/viewresults.png)
3. Ezután indíthat egy új importálást, melynek során megtarthatja a meglévő beállításokat (például a kapcsolati sztringet, a választott forrást és célt, illetve más értékeket), vagy alaphelyzetbe állíthat mindent.
   
    ![Képernyőkép az Azure Cosmos DB JSON-fájlba való exportálási beállításáról](./media/import-data/newimport.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Az adatáttelepítési eszköz telepítése
> * Adatok importálása különböző adatforrásokból
> * Exportálás Azure Cosmos DB-ből JSON-ba

Továbbléphet a következő oktatóanyagra, melyben megismerheti, hogy hogyan kérdezhet le adatokat az Azure Cosmos DB segítségével. 

> [!div class="nextstepaction"]
>[Adatok lekérdezése](../cosmos-db/tutorial-query-sql-api.md)
