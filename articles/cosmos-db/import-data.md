---
title: Az Azure Cosmos DB adatbázis-migrálási eszköz
description: Ez a dokumentum ismerteti, hogy hogyan telepíthet át adatokat az Azure Cosmos DB nyílt forráskódú adatáttelepítési eszközeivel az Azure Cosmos DB-be különféle forrásokból, például MongoDB-, SQL Server-, Table Storage- és Amazon DynamoDB-adatbázisokból, illetve CSV- és JSON-fájlokból. A CSV-fájlokat JSON formátumba konvertálhatja.
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: dech
ms.openlocfilehash: 0981a0810ee64f78443512d794d172a69fb54494
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617008"
---
# <a name="use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Használja az adatok migrálása eszközt az adatok Azure Cosmos DB-be történő migrálásához

Ez az oktatóanyag útmutatást nyújt a Azure Cosmos DB adatáttelepítési eszköz használatáról, amely különböző forrásokból származó adatok importálását teszi lehetővé az Azure Cosmos-tárolókban és-táblákban. JSON fájlok, CSV fájlok, SQL, MongoDB, az Azure Table storage, Amazon DynamoDB és még akkor is, az Azure Cosmos DB SQL API-gyűjteményeket is importálhat. Gyűjtemények és a táblákhoz használható az Azure Cosmos DB az adatokat telepít át. Az adatáttelepítési eszközzel emellett SQL API-beli egypartíciós gyűjteményt is telepíthet át többpartíciós gyűjteménybe.

Melyik API-t szeretné használni az Azure Cosmos DB-vel?

* **[SQL API](documentdb-introduction.md)** – Az adatáttelepítési eszközben elérhető bármelyik forráslehetőséggel importálhat adatokat.
* **[Table API](table-introduction.md)** – Az adatok importálásához használhatja az adatáttelepítési eszközt vagy az AzCopy segédprogramot. További információért olvassa el az [Adatok importálása az Azure Cosmos DB Table API-val való használathoz](table-import.md) című témakört.
* **[Azure Cosmos db API-MongoDB](mongodb-introduction.md)** – az adatáttelepítési eszköz jelenleg nem támogatja Azure Cosmos db API-ját MongoDB vagy forrásként, illetve célként. Ha a Azure Cosmos DB gyűjteményekben lévő vagy azokon kívüli gyűjtemények adatait szeretné áttelepíteni, tekintse [meg a MongoDB-adatbázisok áttelepítését a Cosmos Database-ben Azure Cosmos db API](mongodb-migrate.md) -jával a MongoDB című témakört. Az adatáttelepítési eszközzel azonban exportálhat adatokat MongoDB-ből Azure Cosmos DB SQL API-gyűjteményekbe az SQL API-val való használathoz.
* **[Gremlin API](graph-introduction.md)**  – az adatok áttelepítési eszköze nem egy támogatott importálási eszköz a Gremlin API-fiókok jelenleg.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az adatáttelepítési eszköz telepítése
> * Adatok importálása különböző adatforrásokból
> * Exportálás Azure Cosmos DB-ből JSON-ba

## <a id="Prerequisites"></a>Előfeltételek

Ez a cikk utasításait követve előtt győződjön meg arról, hogy, tegye a következőket:

* **Telepítés** [Microsoft .NET-keretrendszer 4.51](https://www.microsoft.com/download/developer-tools.aspx) vagy újabb verziója.

* **Átviteli sebesség növelése:** Az adatok áttelepítésének időtartama az egyes gyűjteményekhez vagy gyűjtemények készletéhez beállított átviteli sebességtől függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Az Azure Portalon növelve kapcsolatos további információkért lásd: [teljesítményszintek](performance-levels.md) és [tarifacsomagok](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB-ben.

* **Azure Cosmos DB erőforrások létrehozása:** Az áttelepítési adatok elkezdése előtt hozza létre az összes gyűjteményt a Azure Portal. Az adatbázis-szintű átviteli sebességű Azure Cosmos DB-fiókra való áttelepítéshez adjon meg egy partíciós kulcsot az Azure Cosmos-tárolók létrehozásakor.

## <a id="Overviewl"></a>Áttekintés

Az adatáttelepítési eszköz egy nyílt forráskódú megoldás, mellyel adatokat importálhat az Azure Cosmos DB-be különféle forrásokból, ideértve az alábbiakat:

* JSON-fájlok
* MongoDB
* SQL Server
* CSV-fájlok
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure Cosmos-tárolók

Az importálási eszköz tartalmaz egy grafikus felhasználói felületet (dtui.exe), de használhatja a parancssori verziót is (dt.exe). Sőt lehetőség van a kimenetben a kapcsolódó parancs beállítása a felhasználói felületen az importálás után. A forrás táblázatos adatok, például az SQL Server- vagy CSV-fájlok hierarchikus kapcsolatok (aldokumentumok) létrehozni az importálás során alakíthatja át. Az alábbiakban többet is megtudhat a választható forrásokról, mintaparancsokat találhat az egyes forrástípusokból való importáláshoz, és áttekintheti az importálás eredményét.

## <a id="Install"></a>Telepítés

Az áttelepítési eszköz forráskódját elérheti [ebben a GitHub-adattárban](https://github.com/azure/azure-documentdb-datamigrationtool). Letöltheti a forráskódot, és lefordíthatja helyben a megoldást, vagy [letöltheti az előre lefordított bináris fájlokat is](https://aka.ms/csdmtool). Ezután futtathatja az alábbiak egyikét:

* **Dtui. exe**: Az eszköz grafikus felületének verziója
* **DT. exe**: Az eszköz parancssori verziója

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
* [Azure Cosmos-tárolók](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB – tömeges importálás](#SQLBulkTarget)
* [Azure Cosmos DB – szekvenciális rekordimportálás](#SQLSeqTarget)

## <a id="JSON"></a>JSON-fájlok importálása

A JSON fájl forrás programu Pro import beállítás lehetővé teszi egy importálás vagy további egyetlen dokumentum JSON-fájlokat vagy JSON-fájlokat tartalmazó JSON-dokumentumok tömbjét. Mappák rendelkező JSON-fájlok importálása való hozzáadásakor, lehetősége van a fájlok almappákban lévő keresése rekurzív módon.

![Képernyőkép a JSON-fájlok importálási beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/jsonsource.png)

A következő formátumú a kapcsolatok karakterlánca:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* A `<CosmosDB Endpoint>` a végpont URI-ja. Ez az érték a Azure Portalból szerezhető be. Navigáljon az Azure Cosmos-fiókjához. Nyissa meg az **Áttekintés** ablaktáblát, és másolja az **URI** értékét.
* A `<AccountKey>` a "password" vagy az **elsődleges kulcs**. Ez az érték a Azure Portalból szerezhető be. Navigáljon az Azure Cosmos-fiókjához. Nyissa meg a **kapcsolatok karakterláncait** vagy a **kulcsok** ablaktáblát, és másolja a "jelszó" vagy az **elsődleges kulcs** értékét.
* A `<CosmosDB Database>` a CosmosDB-adatbázis neve.

Például: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Az ellenőrzés parancs használatával győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott Cosmos DB fiók elérhető.

Néhány parancssori példa JSON-fájlok importálásához:

```console
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
```

## <a id="MongoDB"></a>Importálás MongoDB-ből

> [!IMPORTANT]
> Ha a MongoDB-hez Azure Cosmos DB API-val konfigurált Cosmos-fiókba importálja, kövesse az alábbi [utasításokat](mongodb-migrate.md).

A MongoDB forrás programu Pro import lehetőséggel egy MongoDB-gyűjtemény importálása, opcionális szűrés a dokumentumok egy lekérdezést és módosítása a dokumentum szerkezete leképezés használatával.  

![Képernyőkép a MongoDB forrás beállításairól](./media/import-data/mongodbsource.png)

A kapcsolati sztring a szabványos MongoDB-formátumot követi:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott MongoDB-példány.

Adja meg annak a gyűjteménynek a nevét, amelyből adatokat kíván importálni. Szükség esetén előfordulhat, hogy adja meg, vagy adjon meg egy fájlt egy lekérdezés számára, például: `{pop: {$gt:5000}}`, vagy egy leképezési, mint például `{loc:0}`, a szűrés és az importált adatok formázása.

Néhány parancssori példa MongoDB-adatbázisból való importáláshoz:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a id="MongoDBExport"></a>MongoDB-exportfájlok importálása

> [!IMPORTANT]
> Ha az Azure Cosmos DB-fiók mongodb-protokolltámogatással rendelkező importál, kövesse az alábbi [utasításokat](mongodb-migrate.md).

Forrásként MongoDB-exportálási JSON-fájlokat választva importálhat egy vagy több olyan JSON-fájlt, amelyet a mongoexport segédprogrammal hozott létre.  

![Képernyőkép a MongoDB-exportfájlok importálási beállításairól](./media/import-data/mongodbexportsource.png)

MongoDB exportálási JSON-fájlok importálása rendelkező mappák való hozzáadásakor, lehetősége van a fájlok almappákban lévő keresése rekurzív módon.

Néhány parancssori példa MongoDB-exportálási JSON-fájlok importáláshoz:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a id="SQL"></a>Importálás SQL Server-adatbázisból

Forrásként SQL-t választva importálhat egy adott SQL Server-adatbázist, és opcionálisan szűrheti az importálandó rekordokat egy lekérdezéssel. Emellett módosíthatja a dokumentumstruktúrát egy beágyazási elválasztó megadásával (erre alább visszatérünk még).  

![Képernyőkép az SQL forrás beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/sqlexportsource.png)

A kapcsolati sztring a szabványos SQL-kapcsolati sztringek formátumát követi.

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott SQL Server-példány.

A beágyazási elválasztó tulajdonsággal hierarchikus kapcsolatokat (aldokumentumokat) hozhat létre az importálás során. Vizsgálja meg a következő SQL-lekérdezést:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Mely az alábbi (részleges) eredményt adja vissza:

![Képernyőkép az SQL-lekérdezés eredményéről](./media/import-data/sqlqueryresults.png)

Megfigyelheti az Address.AddressType és az Address.Location.StateProvinceName áljelet. A „.” beágyazási elválasztó megadása miatt az importáló eszköz létrehozza az Address és az Address.Location aldokumentumot az importálás során. Ez például az alábbi Azure Cosmos DB-beli végleges dokumentumot eredményezheti:

*{"azonosító": "956", "Name": "Finomabb értékesítés és szolgáltatás", "címe": {"AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor utca", "location": {"City": "Ottawa", "StateProvinceName": "Ontario"}, "Irányítószám": "K4B 1S2", "CountryRegionName": "Kanada"}}*

Néhány parancssori példa SQL Server-adatbázisból való importáláshoz:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a id="CSV"></a>CSV-fájlok importálása és CSV-fájlok konvertálása JSON formátumba

Forrásként CSV-fájlt választva importálhat egy vagy több CSV-fájlt. Az Importálás CSV-fájlok mappájához való hozzáadásakor, lehetősége van a fájlok almappákban lévő keresése rekurzív módon.

![Képernyőkép a CSV forrás beállításairól – CSV konvertálása JSON formátumba](media/import-data/csvsource.png)

Az SQL forráshoz hasonlóan itt is használhatja a beágyazási elválasztó tulajdonságot, ha hierarchikus kapcsolatokat (aldokumentumokat) kíván létrehozni az importálás során. Vizsgálja meg például az alábbi CSV-fejlécsort és -adatsorokat:

![Képernyőkép a CSV-mintarekordokról – CSV konvertálása JSON formátumba](./media/import-data/csvsample.png)

Megfigyelheti a DomainInfo.Domain_Name és a RedirectInfo.Redirecting áljelet. A „.” beágyazási elválasztó megadása miatt az importáló eszköz létrehozza a DomainInfo és a RedirectInfo aldokumentumot az importálás során. Ez például az alábbi Azure Cosmos DB-beli végleges dokumentumot eredményezheti:

*{"DomainInfo": {"tartománynév": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV" }, "Federal Agency": "A Egyesült Államok felügyeleti Konferenciája", "RedirectInfo": {"átirányítás": "0", "Redirect_Destination": ""}, "azonosító": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Az importálási eszköz próbál kikövetkeztetni informace o typu jegyzett értékek CSV-fájlok (határolójeles értékek mindig számít karakterláncok).  A típusokat a következő sorrendben azonosítja: szám, datetime, boolean.  

Két további dolgot is fontos tudnia a CSV-importálásról:

1. Az eszköz a nem idézőjeles értékekről alapértelmezés szerint mindig levágja a tabulátor- és szóközkaraktereket, míg az idézőjeles értékeket pontosan megőrzi. Ezt a viselkedést felülbírálhatja a „Trim quoted values” (Idézőjeles értékek csonkolása) jelölőnégyzettel vagy a /s.TrimQuoted parancssori kapcsolóval.
2. Alapértelmezés szerint a nem idézőjeles nullértéket null értékként kezeli az eszköz. Ezt a viselkedést felülbírálhatja (vagyis a nem idézőjeles nullértéket egy „null” sztringként kezelheti) a „Treat unquoted NULL as string” (Nem idézőjeles NULL kezelése sztringként) jelölőnégyzettel vagy a /s.NoUnquotedNulls parancssori kapcsolóval.

Parancssori példa CSV-fájl importáláshoz:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a id="AzureTableSource"></a>Importálás Azure Table Storage-ből

Forrás Azure Table Storage-et választva importálhat egy adott Azure Table Storage-táblát. Igény esetén szűrheti is az importálandó táblaentitásokat.

Az Azure Table Storage-ból az Azure Cosmos DB-táblákat és entitásokat a Table API-val használható importált adatok előfordulhat, hogy kimeneti. Importált adatok kimenet gyűjtemények és dokumentumok használható az SQL API-val is lehet. Table API azonban csak akkor használható, a parancssori segédprogram célként. Nem lehet exportálni a Table API az adatok áttelepítési eszköz felhasználói felület használatával. További információért olvassa el az [Adatok importálása az Azure Cosmos DB Table API-val való használathoz](table-import.md) című témakört.

![Képernyőkép az Azure Table Storage forrás beállításairól](./media/import-data/azuretablesource.png)

Az Azure Table Storage kapcsolati sztringjének formátuma a következő:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Table Storage-példány.

Adja meg annak az Azure-táblának a nevét, amelyből importálni kíván. Igény esetén adhat meg egy [szűrőt](../vs-azure-tools-table-designer-construct-filter-strings.md) is.

Az Azure Table Storage importálási forráshoz a következő további beállításokat adhatja meg:

1. Include Internal Fields (Belső mezők belefoglalása)
   1. All (Mind) – Az összes belső mező (PartitionKey, RowKey és Timestamp) belefoglalása
   2. None (Egyik sem) – Az összes belső mező kizárása
   3. RowKey – Csak a RowKey mező belefoglalása
2. Select Columns (Oszlopok kijelölése)
   1. Az Azure Table storage szűrők nem támogatja a leképezéseket. Ha csak bizonyos Azure Table Storage-entitástulajdonságokat szeretne importálni, adja őket hozzá a Select Columns listához. Az összes többi entitástulajdonságot figyelmen kívül hagyja az eszköz.

Parancssori példa Azure Table Storage-ből való importáláshoz:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a id="DynamoDBSource"></a>Importálás Amazon DynamoDB-ből

Az Amazon DynamoDB forrás programu Pro import beállítás lehetővé teszi, hogy egyetlen Amazon DynamoDB tábla importálása. Ezt igény szerint szűrheti az importálandó entitásokat. Számos beépített sablon a rendelkezésére áll, hogy a lehető legegyszerűbben beállíthassa az importálást.

![Képernyőkép az Amazon DynamoDB forrás beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/dynamodbsource1.png)

![Képernyőkép az Amazon DynamoDB forrás beállításairól – Adatbázis-áttelepítési eszközök](./media/import-data/dynamodbsource2.png)

Az Amazon DynamoDB kapcsolati sztringjének formátuma a következő:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Amazon DynamoDB-példány.

Parancssori példa Amazon DynamoDB-ből való importáláshoz:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a id="BlobImport"></a>Importálás Azure Blob Storage-ből

Forrásként JSON-fájlt, MongoDB-exportfájlt vagy CSV-fájlt választva importálhat egy vagy több Azure Blob Storage-beli fájlt. Miután megadta a blobtároló URL-címét és fiókkulcsát, adjon meg egy reguláris kifejezést is az importálandó fájlok kijelöléséhez.

![Képernyőkép a Blob forrás beállításairól](./media/import-data/blobsource.png)

Parancssori példa Azure Blob Storage-beli JSON-fájlok importáláshoz:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a id="SQLSource"></a>Importálás egy SQL API-gyűjteményből

A Azure Cosmos DB forrás-importáló lehetőséggel adatok importálhatók egy vagy több Azure Cosmos-tárolóból, és opcionálisan szűrheti a dokumentumokat egy lekérdezés használatával.  

![Képernyőkép az Azure Cosmos DB forrás beállításairól](./media/import-data/documentdbsource.png)

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Lekérheti az Azure Cosmos DB kapcsolati sztringje az Azure portal kulcsok lapjáról leírtak szerint [kezelése az Azure Cosmos DB-fiók](manage-account.md). Azonban az adatbázis neve a kapcsolati karakterláncot a következő formátumban kell csatolni kell:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.

Egyetlen Azure Cosmos-tárolóból való importáláshoz adja meg annak a gyűjteménynek a nevét, amelyből az adatok importálhatók. Ha egynél több Azure Cosmos-tárolóból szeretne importálni, adjon meg egy reguláris kifejezést, hogy egyezzen egy vagy több gyűjtemény nevével (például collection01 | collection02 | collection03). Előfordulhat, hogy szükség esetén adja meg, vagy adjon meg egy fájlt, egy lekérdezési szűrő és alakzat importál adatokat is.

> [!NOTE]
> Mivel a gyűjtemény mező elfogadja a reguláris kifejezések, ha egy gyűjteményt, amelynek a neve van reguláris kifejezések karakterei történő importálás, majd ezeket a karaktereket escape-karakterrel ennek megfelelően.

Az Azure Cosmos DB importálási forráshoz a következő speciális beállításokat adhatja meg:

1. Belső mezők belefoglalása: Megadja, hogy Azure Cosmos DB-e a dokumentumrendszer tulajdonságai az exportálásban (például _rid, _ts).
2. Sikertelen próbálkozások száma: Meghatározza, hogy a rendszer hányszor próbálkozzon újra a kapcsolattal Azure Cosmos DB az átmeneti hibák (például hálózati kapcsolat megszakítása) esetén.
3. Újrapróbálkozási időköz: Itt adhatja meg, hogy mennyi ideig kell várni a kapcsolat újrapróbálkozása Azure Cosmos DB esetén átmeneti hibák esetén (például hálózati kapcsolat megszakítása).
4. Csatlakoztatási mód: Megadja a Azure Cosmos DBhoz használandó kapcsolódási módot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

![Képernyőkép az Azure Cosmos DB forrás speciális beállításairól](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.

Néhány parancssori példa Azure Cosmos DB-ből való importáláshoz:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500
```

> [!TIP]
> Az Azure Cosmos DB adatimportálási eszköze támogatja az adatok [Azure Cosmos DB Emulatorból](local-emulator.md) való importálását is. Adatok helyi emulátorból való importálásakor állítsa a végpontot a `https://localhost:<port>` értékre.

## <a id="HBaseSource"></a>Importálás HBase-ből

Forrásként HBase-t választva importálhat adatokat egy HBase-táblából, és opcionálisan szűrheti az adatokat. Számos beépített sablon a rendelkezésére áll, hogy a lehető legegyszerűbben beállíthassa az importálást.

![Képernyőkép a HBase forrás beállításairól](./media/import-data/hbasesource1.png)

![Képernyőkép a HBase forrás beállításairól](./media/import-data/hbasesource2.png)

A HBase Stargate kapcsolati sztringjének formátuma a következő:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott HBase-példány.

Parancssori példa HBase-ből való importáláshoz:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a id="SQLBulkTarget"></a>Importálás SQL API-ba (tömeges importálás)

Az Azure Cosmos DB tömeges importálójával egy Azure Cosmos DB-beli tárolt eljárás segítségével hatékonyabban importálhat bármelyik választható forrásból. Az eszköz támogatja az importálást egyetlen particionált Azure Cosmos-tárolóba. Támogatja továbbá a többrétegű importálást is, amely során az adatparticionálás több, egypartíciós Azure Cosmos-tárolón keresztül történik. Az adatok particionálásával kapcsolatban további információkat az [Azure Cosmos DB particionálási és méretezési eljárásait](partition-data.md) ismertető cikkben talál. Az eszköz létrehozza, végrehajtja, majd törli a tárolt eljárást a célgyűjtemény(ek)ben.  

![Képernyőkép az Azure Cosmos DB tömeges importálási beállításairól](./media/import-data/documentdbbulk.png)

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Az Azure Cosmos DB-fiók kapcsolati sztringjét beszerezheti az Azure Portal Kulcsok lapjáról az [Azure Cosmos DB-fiók kezelése](manage-account.md) című cikkben ismertetett módon, az adatbázis nevét azonban hozzá kell fűznie a kapcsolati sztringhez a következő formátumot követve:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.

Egyetlen gyűjtemény importálásához írja be annak a gyűjteménynek a nevét, amelyből importálni kívánja az adatokat, majd kattintson az Add (Hozzáadás) gombra. Egynél több gyűjtemény importálásához külön-külön megadása minden gyűjtemény neve vagy adjon meg egynél több gyűjteményt a következő szintaxissal: *collection_prefix*[start index - end index]. A fent említett szintaxis használatával több gyűjtemény megadásakor tartsa szem előtt az alábbi irányelveket:

1. Csak az integer adattípuson alapuló minták támogatottak. A „gyűjtemény[0-3]” minta például a következő gyűjteményeket hozza létre: gyűjtemény0, gyűjtemény1, gyűjtemény2, gyűjtemény3.
2. Használhat rövidített szintaxist: A „gyűjtemény[3]” minta ugyanúgy az 1. lépésben említett gyűjteménykészletet hozza létre.
3. Adhat meg több behelyettesítést is. A „gyűjtemény[0-1] [0-9]” minta például 20 gyűjteménynevet generál kezdő nulla használatával (gyűjtemény01, ..02, ..03).

A gyűjteménynév (vagy nevek) megadása után válassza ki a gyűjtemény(ek)hez használni kívánt átviteli sebességet (400 – 10 000 kérelemegység). Nagyobb importálási teljesítményhez válasszon magasabb átviteli sebességet. A teljesítményszintekről bővebben [Az Azure Cosmos DB teljesítményszintjei](performance-levels.md) című témakörben tájékozódhat.

> [!NOTE]
> A beállított átviteli sebesség csak a gyűjtemény létrehozásakor érvényesül. Ha a megadott gyűjtemény már létezik, az átviteli sebesség nem lehet módosítani.

Ha egynél több gyűjteményt importál, az importálási eszköz támogatja a horizontális skálázás kivonat-alapú. Ebben a forgatókönyvben adja meg a partíciókulcsnak használni kívánt tulajdonság. (Ha Partíciókulcsot üresen marad, dokumentumok olyan horizontálisan skálázott véletlenszerűen a cél gyűjtemények között.)

Opcionálisan megadhatja azt az importálás forrás melyik mezőt kell használni az Azure Cosmos DB-dokumentum ID tulajdonság az importálás során. Ha dokumentumok nincs ezt a tulajdonságot, majd az importálási eszköz előállít egy GUID Azonosítót, az ID tulajdonság értéke.

Elérhető néhány speciális beállítás is importáláskor. Például az eszköz tartalmaz egy alapértelmezett tömeges importálási tárolt eljárást (BulkInsert.js), de adhat meg helyette saját importálási tárolt eljárást is:

 ![Képernyőkép az Azure Cosmos DB tömeges beszúrási tárolt eljárásának beállításáról](./media/import-data/bulkinsertsp.png)

Emellett dátum típusú értékek importálásakor (például SQL Serverből vagy MongoDB-ből) három különböző importálási lehetőség közül választhat:

 ![Képernyőkép az Azure Cosmos DB importálási dátum- és időbeállításáról](./media/import-data/datetimeoptions.png)

* Karakterlánc Megmaradás sztringként
* Alapidőszak Alapértékek száma
* Mind A karakterlánc-és EPOCH-számok értékét is megőrzi. Ez a beállítás létrehoz egy aldokumentumot, például: "date_joined": {"value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Az Azure Cosmos DB tömeges importálási eszköze a következő speciális beállításokat támogatja:

1. Köteg mérete: Az eszköz alapértelmezett értéke 50.  Nagyméretű dokumentumok importálásakor célszerű csökkenteni a kötegméretet, kisméretű dokumentumok importálásakor pedig növelni a kötegméretet.
2. Szkriptek maximális mérete (bájt): Az eszköz alapértelmezett értéke a 512 KB-os maximális parancsfájl mérete.
3. Automatikus azonosító generálásának letiltása: Ha minden importálandó dokumentum rendelkezik azonosító mezővel, akkor a beállítás kiválasztásával növelheti a teljesítményt. Hiányzik egy egyedi azonosító mező dokumentumok nem importálja.
4. Meglévő dokumentumok frissítése: Az eszköz alapértelmezés szerint nem cseréli le a meglévő dokumentumokat azonosító ütközésekkel. Ez a beállítás lehetővé teszi a felülírják a meglévő dokumentumok-azonosítók egyeztetésével. Ez a funkció a meglévő dokumentumokat frissítő ütemezett adatáttelepítésekhez hasznos.
5. Sikertelen próbálkozások száma: Meghatározza, hogy a rendszer milyen gyakran próbálkozzon újra a kapcsolattal Azure Cosmos DB átmeneti hibák (például hálózati kapcsolat megszakítása) során.
6. Újrapróbálkozási időköz: Itt adhatja meg, hogy mennyi ideig kell várni a kapcsolat újrapróbálkozása Azure Cosmos DB esetén átmeneti hibák esetén (például hálózati kapcsolat megszakítása).
7. Csatlakoztatási mód: Megadja a Azure Cosmos DBhoz használandó kapcsolódási módot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

![Képernyőkép az Azure Cosmos DB speciális tömeges importálási beállításairól](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.

## <a id="SQLSeqTarget"></a>Importálás az SQL API-ba (szekvenciális rekordimportálás)

Az Azure Cosmos DB szekvenciális rekord programu Pro import elszámolással, az rekord-rekord egy rendelkezésre álló forrás történő importálását teszi lehetővé. Ez a lehetőség akkor lehet hasznos, ha olyan meglévő gyűjteménybe importál, amelynek már kimerült a tárolt eljárási kvótája. Az eszköz támogatja az importálást egyetlen (egypartíciós és több partíciós) Azure Cosmos-tárolóban. Emellett támogatja a többrétegű importálást is, amely során az adatparticionálás több, mint egy partíciós vagy több partíciós Azure Cosmos-tárolón keresztül történik. Az adatok particionálásával kapcsolatban további információkat az [Azure Cosmos DB particionálási és méretezési eljárásait](partition-data.md) ismertető cikkben talál.

![Képernyőkép az Azure Cosmos DB szekvenciális rekordimportálási beállításairól](./media/import-data/documentdbsequential.png)

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Kérheti le a kapcsolati karakterláncot az Azure Cosmos DB-fiók az Azure portal kulcsok lapjáról leírtak szerint [kezelése az Azure Cosmos DB-fiók](manage-account.md). Azonban az adatbázis neve a kapcsolati karakterláncot a következő formátumban kell csatolni kell:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.

Egyetlen gyűjtemény importálásához adja meg az adatok importálása a gyűjtemény nevét, és kattintson a Hozzáadás gombra. Egynél több gyűjtemény importálásához külön-külön adja meg minden egyes gyűjtemény neve. Adjon meg egynél több gyűjteményt az is előfordulhat, hogy használja a következő szintaxist: *collection_prefix*[start index - end index]. A fent említett szintaxis használatával több gyűjtemény megadásakor tartsa szem előtt az alábbi irányelveket:

1. Csak az integer adattípuson alapuló minták támogatottak. A „gyűjtemény[0-3]” minta például a következő gyűjteményeket hozza létre: gyűjtemény0, gyűjtemény1, gyűjtemény2, gyűjtemény3.
2. Használhat rövidített szintaxist: A „gyűjtemény[3]” minta ugyanúgy az 1. lépésben említett gyűjteménykészletet hozza létre.
3. Adhat meg több behelyettesítést is. A „gyűjtemény[0-1] [0-9]” minta például 20 gyűjteménynevet hoz létre kezdő nulla használatával (gyűjtemény01, ..02, ..03).

A gyűjteménynév (vagy nevek) megadása után válassza ki a gyűjtemény(ek)hez használni kívánt átviteli sebességet (400 – 250 000 kérelemegység). Nagyobb importálási teljesítményhez válasszon magasabb átviteli sebességet. A teljesítményszintekről bővebben [Az Azure Cosmos DB teljesítményszintjei](performance-levels.md) című témakörben tájékozódhat. A 10 000 kérelemegységnél magasabb átviteli sebességű gyűjteményekbe való importáláshoz partíciókulcsot kell használnia. Ha 250 000 kérelemegységet meghaladó átviteli sebességet kíván használni, akkor külön kérelmeznie kell a portálon a növelés végrehajtását.

> [!NOTE]
> A beállított átviteli sebesség csak a gyűjtemény vagy adatbázis létrehozásakor érvényesül. Ha a megadott gyűjtemény már létezik, az átviteli sebesség nem lehet módosítani.

Ha egynél több gyűjteményt importál, az importálási eszköz támogatja a horizontális skálázás kivonat-alapú. Ebben a forgatókönyvben adja meg a partíciókulcsnak használni kívánt tulajdonság. (Ha Partíciókulcsot üresen marad, dokumentumok olyan horizontálisan skálázott véletlenszerűen a cél gyűjtemények között.)

Opcionálisan megadhatja azt az importálás forrás melyik mezőt kell használni az Azure Cosmos DB-dokumentum ID tulajdonság az importálás során. (Ha dokumentumok nincs ezt a tulajdonságot, majd az importálási eszköz előállít egy GUID Azonosítót, az ID tulajdonság értéke.)

Elérhető néhány speciális beállítás is importáláskor. Dátum típusú értékek importálásakor (például SQL Serverből vagy MongoDB-ből) három különböző importálási lehetőség közül választhat:

 ![Képernyőkép az Azure Cosmos DB importálási dátum- és időbeállításáról](./media/import-data/datetimeoptions.png)

* Karakterlánc Megmaradás sztringként
* Alapidőszak Alapértékek száma
* Mind A karakterlánc-és EPOCH-számok értékét is megőrzi. Ez a beállítás létrehoz egy aldokumentumot, például: "date_joined": {"value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Az Azure Cosmos DB szekvenciális rekordimportáló eszköze a következő speciális beállításokat támogatja:

1. Párhuzamos kérelmek száma: Az eszköz alapértelmezett értéke két párhuzamos kérelem. Kisméretű dokumentumok importálásakor célszerű növelni a párhuzamos kérelmek számát. Ha azonban túl nagy értéket ad meg, akkor a rendszer korlátozhatja az importálás sebességét.
2. Automatikus azonosító generálásának letiltása: Ha minden importálandó dokumentum rendelkezik azonosító mezővel, akkor a beállítás kiválasztásával növelheti a teljesítményt. Hiányzik egy egyedi azonosító mező dokumentumok nem importálja.
3. Meglévő dokumentumok frissítése: Az eszköz alapértelmezés szerint nem cseréli le a meglévő dokumentumokat azonosító ütközésekkel. Ez a beállítás lehetővé teszi a felülírják a meglévő dokumentumok-azonosítók egyeztetésével. Ez a funkció a meglévő dokumentumokat frissítő ütemezett adatáttelepítésekhez hasznos.
4. Sikertelen próbálkozások száma: Meghatározza, hogy a rendszer milyen gyakran próbálkozzon újra a kapcsolattal Azure Cosmos DB átmeneti hibák (például hálózati kapcsolat megszakítása) során.
5. Újrapróbálkozási időköz: Itt adhatja meg, hogy mennyi ideig kell várni a kapcsolat újrapróbálkozása Azure Cosmos DB során az átmeneti hibák (például a hálózati kapcsolat megszakítása) során.
6. Csatlakoztatási mód: Megadja a Azure Cosmos DBhoz használandó kapcsolódási módot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

![Képernyőkép az Azure Cosmos DB speciális szekvenciálisrekord-importálási beállításairól](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.

## <a id="IndexingPolicy"></a>Indexelési házirend megadása

Ha engedélyezi az áttelepítési eszköznek az Azure Cosmos DB SQL API-gyűjtemények létrehozását az importálás során, megadhatja a gyűjtemények indexelési házirendjét. Az Azure Cosmos DB tömeges importálójának és az Azure Cosmos DB szekvenciális rekordimportálójának speciális beállításokat tartalmazó területén keresse meg az Indexing Policy (Indexelési házirend) szakaszt.

![Képernyőkép az Azure Cosmos DB indexelési házirendjének speciális beállításairól](./media/import-data/indexingpolicy1.png)

Az Indexing Policy speciális beállításnál megadhatja az indexelési házirend fájlját, írhat be manuálisan egy indexelési házirendet, valamint az alapértelmezetten elérhető sablonok közül is választhat egyet (az indexelési házirend mezőjére jobb gombbal kattintva).

Az eszköz az alábbi házirendsablonokat tartalmazza beépítve:

* Default (Alapértelmezett): Ez a szabályzat akkor ajánlott, ha a karakterláncok egyenlőséglekérdezéséhez végezhet. A számok ORDER BY, tartomány és egyenlőség lekérdezések használatakor is működik. Alacsonyabb indextárolási terhelést nyújt, mint a Range házirend.
* Range (Tartomány): Ez a szabályzat a legcélszerűbb a számok és karakterláncok ORDER BY, tartomány és egyenlőség lekérdezések használatakor. Magasabb indextárolási terheléssel jár, mint a Default és a Hash házirend.

![Képernyőkép az Azure Cosmos DB indexelési házirendjének speciális beállításairól](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Ha nem adja meg az indexelési házirendet, az alapértelmezett házirend érvényesül. Az indexelési házirendekről bővebben [Az Azure Cosmos DB indexelési házirendjei](index-policy.md) című cikkben tájékozódhat.

## <a name="export-to-json-file"></a>Exportálás JSON-fájlba

Az Azure Cosmos DB JSON exportáló lehetővé teszi, hogy exportálja a rendelkezésre álló forrás beállításokat egy JSON-fájlt, amely egy tömböt a JSON-dokumentumokat. Az eszköz kezeli az exportálás, az Ön számára. Másik lehetőségként kiválaszthatja az eredményül kapott áttelepítési parancs megtekintéséhez, és futtassa a parancsot. A művelet során kapott JSON-fájlt tárolhatja helyben vagy Azure Blob Storage-fiókban.

![Képernyőkép az Azure Cosmos DB helyi JSON-fájlba való exportálási beállításáról](./media/import-data/jsontarget.png)

![Képernyőkép az Azure Cosmos DB Azure Blob Storage-beli JSON-fájlba való exportálási beállításáról](./media/import-data/jsontarget2.png)

Választhatja az eredményül kapott JSON prettify igény szerint. Ez a művelet az eredményül kapott dokumentum méretét megnöveli a közben a tartalom több emberi olvasásra alkalmas.

* Standard JSON-exportálás

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Prettified JSON exportálása

  ```JSON
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
  ```

Parancssori példa a JSON-fájl Azure Blob Storage-ba történő exportálásához:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Speciális konfiguráció

Adja meg a speciális konfigurációs képernyőn annak a naplófájlnak a helyét, amelybe az esetlegesen előforduló hibákat szeretné menteni. Az alábbi szabályok érvényesek erre a lapra:

1. Ha egy fájl neve nincs megadva, majd az összes hibát az eredmények lapon.
2. Ha könyvtárnév nélküli fájlnevet ad meg, akkor a fájlt az eszköz az aktuális környezeti könyvtárban hozza létre (vagy írja felül).
3. Ha kiválaszt egy meglévő fájlt, majd a fájlt a rendszer felülírja, nem Hozzáfűzés beállítani.
4. Ezután adja meg, hogy az eszköz mit naplózzon: az összes üzenetet, csak a kritikus üzeneteket vagy egyetlen hibaüzenetet sem. Végül döntse el, hogy milyen gyakorisággal frissüljön a képernyőn látható átviteli üzenet a művelet előrehaladása során.

   ![A Speciális konfiguráció képernyő képernyőképe](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Erősítse meg a beállítások importálása és megjelenítése parancssort

1. A forrás, cél és adatait, valamint speciális konfiguráció megadása után tekintse át a migrálás összegzése és a megtekintése, vagy másolja az eredményül kapott áttelepítési parancsot, ha azt szeretné. (A parancs másolása hasznos importálási műveletek automatizálását.)

    ![Képernyőkép az összesítő képernyőről](./media/import-data/summary.png)

    ![Képernyőkép az összesítő képernyőről](./media/import-data/summarycommand.png)

2. Ha elégedett a forrás- és célbeállításokkal, válassza az **Import** (Importálás) gombot. Az importálás előrehaladása során figyelemmel kísérheti a képernyőn az eltelt időt, az átvitt elemek számát és a hibákkal kapcsolatos információkat (ha nem adott meg fájlnevet a speciális konfigurációs lapon). Ha a művelet kész, exportálhatja az eredményt (például a sikertelen importálású elemek feldolgozásához).

    ![Képernyőkép az Azure Cosmos DB JSON-fájlba való exportálási beállításáról](./media/import-data/viewresults.png)

3. Egy új importálás alaphelyzetbe állítása az összes értéket, vagy a meglévő beállítások tartja indulhat. (Például előfordulhat, hogy választja, hogy a kapcsolati sztring adatait, a forrás és cél kiválasztása és egyéb.)

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
