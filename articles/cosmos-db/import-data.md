---
title: "Adatbázis-áttelepítési eszköz Azure Cosmos DB |} Microsoft Docs"
description: "Útmutató: adatok importálása az Azure Cosmos Adatbázishoz különböző forrásokból, beleértve a MongoDB, SQL Server, Table storage, Amazon DynamoDB, CSV és JSON fájlokat a nyílt forráskódú Azure Cosmos DB adatok áttelepítési eszközök segítségével. A fürt megosztott kötetei szolgáltatás JSON alakításához."
keywords: "fürt megosztott kötetei szolgáltatás JSON, adatbázis-áttelepítési eszközök, csv konvertálása json"
services: cosmos-db
author: andrewhoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: a60c47814da2660f17456f5e662f420adbb9158e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-import-data-into-azure-cosmos-db-with-the-documentdb-api"></a>Adatok importálása az Azure Cosmos DB DocumentDB API-val

Ez az oktatóanyag az Azure Cosmos DB használatával kapcsolatos utasításokat tartalmazza: a DocumentDB API adatáttelepítés eszközt, amely adatokat importálhat különböző móddal, többek között a JSON-fájlokat, CSV-fájlok, SQL, MongoDB, az Azure Table storage, Amazon DynamoDB és Azure Cosmos DB DocumentDB API gyűjtemények gyűjteményekbe Azure Cosmos adatbázis és a DocumentDB API-val való használatra. Az adatok áttelepítési eszköz is használható, áttelepítésekor az egypartíciós gyűjtemény több partíció gyűjteménybe a DocumentDB az API-hoz.

Az adatok áttelepítési eszköz csak akkor működik, amikor Azure Cosmos DB importáló adatokat a DocumentDB API-t használja. A tábla API és a Graph API adatok importálása jelenleg nem támogatott. 

Adatimportálás a MongoDB API-val való használatra, lásd: [Azure Cosmos DB: adatok áttelepítése a MongoDB API?](mongodb-migrate.md).

Ez az oktatóanyag ismerteti a következő feladatokat:

> [!div class="checklist"]
> * Az adatáttelepítési eszköz telepítése
> * Különböző forrásokból származó adatok importálása
> * A JSON Azure Cosmos DB exportálása

## <a id="Prerequisites"></a>Előfeltételek
Mielőtt Ez a cikk utasításait követve ellenőrizze, hogy a következőkkel:

* [A Microsoft .NET-keretrendszer 4.51](https://www.microsoft.com/download/developer-tools.aspx) vagy újabb verzióját.

## <a id="Overviewl"></a>Az adatok áttelepítési eszköz áttekintése
Az adatok áttelepítési eszköz egy nyílt forráskódú megoldás, amellyel különféle adatok Azure Cosmos DB móddal, többek között számos különböző:

* JSON-fájlok
* MongoDB
* SQL Server
* CSV-fájlok
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Az Azure Cosmos DB gyűjtemények

Amíg az importálási eszköz tartalmazza a grafikus felhasználói felületen (dtui.exe), azt is is vezeti a parancssorból (dt.exe). Valójában nincs lehetősége van arra beállítása a felhasználói felületen az importálás után a hozzárendelt parancs kimenetét. Táblázatos forrásadatok (pl. az SQL Server vagy CSV-fájlokban) is kell alakítani, úgy, hogy az importálás során hozható létre a is a hierarchikus kapcsolat (aldokumentumok). Adatforrás-beállításokkal kapcsolatos további, a minta parancssor minden forrás, a cél lehetőségekkel, valamint a Megtekintés importálási eredmények importálása olvasási megtartása.

## <a id="Install"></a>Az adatáttelepítési eszköz telepítése
Az áttelepítési eszköz a forráskód nem elérhető a Githubon található [ebben a tárházban](https://github.com/azure/azure-documentdb-datamigrationtool) és egy lefordított elérhető a [Microsoft Download Center](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Akkor lehet, hogy vagy a megoldás összeállításához vagy egyszerűen letöltéséhez és kibontásához a lefordított az Ön által választott könyvtárra. Ezután futtassa vagy:

* **Dtui.exe**: az eszköz grafikus felület verziója
* **DT.exe**: az eszköz parancssori verziója

## <a name="import-data"></a>Adatok importálása

Az eszköz telepítését követően a rendszer az idő, importálja az adatokat. Milyen típusú adatokat kívánja importálni?

* [JSON-fájlok](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB exportfájlok](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-fájlok](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [A BLOB](#BlobImport)
* [Az Azure Cosmos DB gyűjtemények](#DocumentDBSource)
* [HBase](#HBaseSource)
* [Az Azure Cosmos DB tömeges importálással](#DocumentDBBulkImport)
* [Az Azure Cosmos DB szekvenciális rekord importálása](#DocumentDSeqTarget)


## <a id="JSON"></a>JSON-fájlok importálása
A JSON fájl forrás importáló beállítással importálása egy vagy több egyetlen dokumentum JSON-fájlokat vagy JSON-fájlokat, hogy minden egyes tartalmazza JSON-dokumentumok tömbjét. Importálása JSON-fájlokat tartalmazó mappák hozzáadásakor lehetősége van a rekurzív módon almappákban lévő fájlok keresése.

![Képernyőfelvétel a JSON fájl forrására vonatkozó beállítások – adatbázis-áttelepítési eszközök](./media/import-data/jsonsource.png)

Az alábbiakban néhány parancssor minták JSON-fájlok importálása:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>MongoDB importálása

> [!IMPORTANT]
> Ha egy, a mongodb-Protokolltámogatással rendelkező Azure Cosmos DB fiókot importál, kövesse az alábbi [utasításokat](mongodb-migrate.md).
> 
> 

A MongoDB forrás importáló beállítás lehetővé teszi az egyes MongoDB-gyűjteményt importálása és opcionálisan szűrése lekérdezéssel dokumentumok és/vagy módosítani a dokumentumot szerkezetét leképezés használatával.  

![Képernyőfelvétel a MongoDB forrására vonatkozó beállítások](./media/import-data/mongodbsource.png)

A kapcsolati karakterláncot a szabványos MongoDB formátumban van:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Az ellenőrzés parancs segítségével győződjön meg arról, hogy elérhető-e a MongoDB-példány a kapcsolati karakterlánc mezőben megadott.
> 
> 

Adja meg, amelyből adatok Importálja a gyűjtemény nevét. Szükség lehet, hogy adja meg, vagy adjon meg egy fájlt egy lekérdezés (pl. {pop: {$gt: 5000}}) és/vagy a leképezése (például {loc:0}) szűrő és az adatokat, importálandók alakul.

Az alábbiakban néhány parancssor minták MongoDB importálása:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>MongoDB exportálási fájlok importálása

> [!IMPORTANT]
> Ha egy, a mongodb-protokolltámogatással rendelkező Azure Cosmos DB fiókot importál, kövesse az alábbi [utasításokat](mongodb-migrate.md).
> 
> 

A MongoDB exportálási JSON fájl forrás importáló beállítás lehetővé teszi egy vagy több JSON-fájlokat a mongoexport segédprogram előállított importálását.  

![Képernyőfelvétel a MongoDB exportálási forrására vonatkozó beállítások](./media/import-data/mongodbexportsource.png)

MongoDB exportálási JSON-fájlok importálása tartalmazó mappák hozzáadásakor lehetősége van a rekurzív módon almappákban lévő fájlok keresése.

Egy parancssorban minta MongoDB exportálási JSON-fájlok importálása a következő:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>SQL Server importálása
Az SQL-adatforrás-importáló beállítást lehetővé teszi egy egyéni SQL Server-adatbázis importálása, és igény szerint szűrheti a rekordokat, importálandók lekérdezés segítségével. Emellett módosíthatja a dokumentumstruktúrával (további sablonokat, amelyek később) beágyazási elválasztó megadásával.  

![Képernyőkép az SQL - adatbázis áttelepítési eszközök forrása](./media/import-data/sqlexportsource.png)

A kapcsolati karakterlánc formátuma a szokásos SQL kapcsolati karakterlánc-formátum.

> [!NOTE]
> A ellenőrizze parancs segítségével győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott SQL Server-példány is elérhetők.
> 
> 

A beágyazási elválasztó tulajdonság importálása során (alárendelt dokumentumok) hierarchikus kapcsolat létrehozásához használt. Vegye figyelembe a következő SQL-lekérdezést:

*Válassza ki a TÍPUSKONVERZIÓ (BusinessEntityID AS varchar) azonosítója, neve, mint [Address.AddressType] AddressType, AddressLine1 [Address.AddressLine1], [Address.Location.City] városát, StateProvinceName [Address.Location.StateProvinceName], irányítószám szerint [Address.PostalCode], CountryRegionName [Address.CountryRegionName], a Sales.vStoreWithAddresses ahol AddressType = "Main Office"*

Amely a következő (részleges) eredményeket ad vissza:

![Képernyőkép az SQL-lekérdezés eredményei](./media/import-data/sqlqueryresults.png)

Vegye figyelembe például Address.AddressType és Address.Location.StateProvinceName aliasok. A beágyazási elválasztó megadásával ".", az importálási eszköz cím és Address.Location aldokumentumok létrehozza az importálás során. Íme egy példa az Azure Cosmos Adatbázisba az eredményül kapott dokumentum:

*{"id": "956", "Name": "Egyeztetését értékesítés és a szolgáltatás", "Cím": {"AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor utca", "Hely": {"Város": "Ottawai", "StateProvinceName": "Ontario"}, "Irányítószám": "K4B 1S2", "CountryRegionName": "Kanada"}}*

Az alábbiakban néhány importálása az SQL Server parancssori minták:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Importálása CSV-fájlok és a fürt megosztott kötetei szolgáltatás átalakítása JSON
A fürt megosztott kötetei szolgáltatás fájl forrás importáló beállítás lehetővé teszi egy vagy több CSV-fájl importálása. Az Importálás CSV-fájlokat tartalmazó mappák hozzáadásakor lehetősége van a rekurzív módon almappákban lévő fájlok keresése.

![Képernyőfelvétel a CSV-forrására vonatkozó beállítások - JSON CSV](media/import-data/csvsource.png)

Az SQL-forrás hasonló, a beágyazási elválasztó tulajdonság lehetséges, hogy használható létrehozásához hierarchikus kapcsolat (alárendelt dokumentumok) importálása során. Vegye figyelembe a következő CSV-fejléc sor és az adatok sorok:

![Képernyőfelvétel a fürt megosztott kötetei szolgáltatás minta rekordok - JSON CSV](./media/import-data/csvsample.png)

Vegye figyelembe például DomainInfo.Domain_Name és RedirectInfo.Redirecting aliasok. A beágyazási elválasztó megadásával ".", az importálási eszköz DomainInfo és RedirectInfo aldokumentumok hoz létre az importálás során. Íme egy példa az Azure Cosmos Adatbázisba az eredményül kapott dokumentum:

*{"DomainInfo": {"Tartománynév": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV"}, "Szövetségi ügynökség": "felügyeleti konferencia az Amerikai Egyesült államokbeli", "RedirectInfo": {"Átirányítása": "0", "Redirect_Destination": ""}, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d"}*

Az import eszközt, amelyből megállapítható a CSV-fájlok nem jegyzett értékekre típusinformációt (idézőjelek közé zárt értékek mindig tekintendők karakterláncok) kísérli meg.  Típusok azonosítja a következő sorrendben: szám, dátum és idő, logikai érték.  

Két dolgot más kapcsolatos CSV import figyelembe venni:

1. Alapértelmezés szerint nem jegyzett értékek mindig elrejti a program lapok és szóközt tartalmaz, idézőjelek közé zárt értékek őrződnek meg amíg-van. Ez a viselkedés felülbírálható a vágás idézőjelek közé zárt értékek jelölőnégyzet vagy /s.TrimQuoted parancssori kapcsolót.
2. Alapértelmezés szerint egy nem jegyzett null értéke null értékű. Ez a viselkedés felülbírálható (azaz tekinti egy nem jegyzett null "null" karakterlánc) és a Treat utasítás nem jegyzett NULL karakterlánc jelölőnégyzet vagy /s.NoUnquotedNulls parancssori kapcsolót.

A CSV-importálási parancssori minta a következő:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Az Azure Table storage importálható
Az Azure Table storage importáló forrásbeállítás lehetővé teszi az egyes Azure Table storage tábla importálása, és igény szerint szűrheti a táblaentitásokat, importálandók. Vegye figyelembe, hogy az adatáttelepítési eszköz nem használható az Azure Table storage adatok importálása az Azure Cosmos DB a tábla API-val való használatra. Most támogatja a DocumentDB API-val való használatra Azure Cosmos DB csak importálását.

![Képernyőfelvétel az Azure Table storage forrására vonatkozó beállítások](./media/import-data/azuretablesource.png)

Az Azure Table storage kapcsolati karakterlánc formátuma:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Az ellenőrzés parancs segítségével győződjön meg arról, hogy az Azure Table storage-példány a kapcsolati karakterlánc mezőben megadott elérhető.
> 
> 

Adja meg a nevét, amelyről adatokat importálja az Azure tábla. Opcionálisan megadhat egy [szűrő](https://msdn.microsoft.com/library/azure/ff683669.aspx).

Az Azure Table storage importáló beállítást az alábbi lehetőségekkel rendelkezik:

1. Belső mezők szerepelhetnek
   1. Az összes - mezők szerepelhetnek, az összes belső (PartitionKey, RowKey, vagy időbélyeg)
   2. Nincs – összes belső mező kizárása
   3. RowKey - csak közé tartozik a RowKey mező
2. Oszlopok kiválasztása
   1. Az Azure Table storage szűrők nem támogatják a leképezések. Ha csak az adott Azure Table Entitástulajdonságok importálni kívánt, vegye fel a Select Columns listára. Minden más entitás tulajdonságait a rendszer figyelmen kívül hagyja.

Az Azure Table storage importálható parancssori minta a következő:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Amazon DynamoDB importálása
Az Amazon DynamoDB forrás importáló beállítás lehetővé teszi az egyes Amazon DynamoDB tábla importálása, és opcionálisan szűrheti az entitásokat, importálandók. Több sablonok találhatók, így a lehető legkönnyebben állítja be az importálás nem.

![Képernyőkép az Amazon DynamoDB forrására vonatkozó beállítások – adatbázis-áttelepítési eszközök](./media/import-data/dynamodbsource1.png)

![Képernyőkép az Amazon DynamoDB forrására vonatkozó beállítások – adatbázis-áttelepítési eszközök](./media/import-data/dynamodbsource2.png)

Az Amazon DynamoDB kapcsolati karakterlánc formátuma:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> A ellenőrizze parancs segítségével győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott Amazon DynamoDB példány is elérhetők.
> 
> 

Íme egy parancssor minta Amazon DynamoDB importálása:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Az Azure Blob storage fájlok importálása
A JSON-fájl, a MongoDB exportfájl és a fürt megosztott kötetei szolgáltatás fájl forrás importáló beállítások lehetővé teszik egy vagy több fájlt importálja az Azure Blob-tárolóból. Adja meg a Blob-tároló URL-cím és a Fiókkulcsot, adjon meg egy reguláris kifejezést az importálandó fájl kiválasztása.

![Képernyőfelvétel a Blob fájl forrására vonatkozó beállítások](./media/import-data/blobsource.png)

JSON-fájlok importálása az Azure Blob storage parancssori minta a következő:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="DocumentDBSource"></a>Egy Azure Cosmos DB DocumentDB API gyűjtemény importálása
Az Azure Cosmos DB adatforrás-importáló beállítást adatokat importálhat egy vagy több Azure Cosmos DB gyűjteményt, és opcionálisan szűréséhez a dokumentumok lekérdezés segítségével teszi lehetővé.  

![Képernyőfelvétel az Azure Cosmos DB adatforrás-beállítások](./media/import-data/documentdbsource.png)

Az Azure Cosmos DB kapcsolati karakterlánc formátuma:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A Azure Cosmos DB fiók kapcsolati karakterláncot az Azure-portálon (kulcsok) panelén lekérhetők, a [Azure Cosmos DB fiók kezelése](manage-account.md), azonban annak az adatbázisnak a nevét meg kell hozzáfűzi a kapcsolati karakterlánc a következő formátumban:

    Database=<CosmosDB Database>;

> [!NOTE]
> A ellenőrizze parancs segítségével győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott Azure Cosmos DB-példány is elérhetők.
> 
> 

Egyetlen Azure Cosmos DB gyűjtemény importálása, adja meg, amelyből adatok Importálja a gyűjtemény nevét. Több Azure Cosmos DB gyűjtemények importálása, adja meg egy vagy több gyűjtemény neve kereséséhez reguláris kifejezés (pl. collection01 |} collection02 |} collection03). Meg lehet, hogy igény szerint adja meg, vagy adjon meg egy fájlt, szűrő és az adatokat, importálandók shape lekérdezés.

> [!NOTE]
> Mivel a gyűjtemény mezőben reguláris kifejezések fogad el, ha importál egy egyetlen gyűjteményből, amelynek a neve reguláris kifejezés karaktereket tartalmaz, majd ezeket a karaktereket kell megjelölni ennek megfelelően.
> 
> 

Az Azure Cosmos DB adatforrás-importáló beállítást tartalmaz a következő speciális beállítások:

1. Belső mezők szerepelhetnek: Megadja, hogy Azure Cosmos DB rendszer dokumentumtulajdonságok szerepeljenek az Exportálás (pl. _rid, _ts).
2. Hiba újbóli próbálkozások számát: a kapcsolat az Azure Cosmos Adatbázishoz (pl.: hálózati kapcsolat megszakadása) átmeneti hibák esetén próbálkozások számát adja meg.
3. Újrapróbálkozási időköz: Azt határozza meg mennyi ideig várjon, amíg a kapcsolat az Azure Cosmos Adatbázishoz (pl.: hálózati kapcsolat megszakadása) átmeneti hibák esetén újrapróbálkozása között.
4. Csatlakozási mód: A csatlakozási mód használata Azure Cosmos DB határozza meg. A lehetséges értékek DirectTcp, DirectHttps és az átjáró. A közvetlen kapcsolat módok a következők: gyorsabb, amíg az átjáró mód rövid több tűzfal, mert csak 443-as portot.

![Képernyőfelvétel az Azure Cosmos DB adatforrás speciális beállítások](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> Az import eszközt az alapértelmezett csatlakozási mód DirectTcp. Ha tűzfal problémák, kapcsolat módba vált-átjárón csak 443-as portra van szüksége.
> 
> 

Az alábbiakban néhány parancssor minták Azure Cosmos DB importálása:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Az Azure Cosmos DB importálása eszközzel is támogatja az adatok importálása a [Azure Cosmos DB emulátor](local-emulator.md). Adatok importálása egy helyi emulátor, állítja a végpont `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>A HBase importálása
A HBase forrás importáló beállítás lehetővé teszi adatokat importálhat egy HBase tábla, és opcionálisan szűrje az adatokat. Több sablonok találhatók, így a lehető legkönnyebben állítja be az importálás nem.

![Képernyőfelvétel a HBase forrására vonatkozó beállítások](./media/import-data/hbasesource1.png)

![Képernyőfelvétel a HBase forrására vonatkozó beállítások](./media/import-data/hbasesource2.png)

A HBase Stargate kapcsolati karakterlánc formátuma:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> A ellenőrizze parancs segítségével győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott HBase-példány is elérhetők.
> 
> 

Íme egy parancssor minta HBase importálása:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="DocumentDBBulkTarget"></a>A DocumentDB-API (tömeges importálásához) importálása
Az Azure Cosmos DB tömeges importáló lehetővé teszi az elérhető forráskiszolgálók beállításokat, egy Azure Cosmos DB tárolt eljárás használatával a hatékonyság importálása. Az eszköz támogatja az importálás egy egyetlen particionált Azure Cosmos DB gyűjteménybe, valamint a szilánkos importálása, amelynek során az adatok több egy particionált Azure Cosmos DB gyűjtemények között particionált van. Adatok partícionálásra vonatkozó további információkért lásd: [particionálás és az Azure Cosmos Adatbázisba skálázás](partition-data.md). Az eszköz létrehozása, hajtható végre, és törölje a cél a következő gyűjtemény(ek) készleteit szinkronizálja a tárolt eljárást.  

![Képernyőfelvétel az Azure Cosmos DB tömeges beállítások](./media/import-data/documentdbbulk.png)

Az Azure Cosmos DB kapcsolati karakterlánc formátuma:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A Azure Cosmos DB fiók kapcsolati karakterláncot az Azure-portálon (kulcsok) panelén lekérhetők, a [Azure Cosmos DB fiók kezelése](manage-account.md), azonban annak az adatbázisnak a nevét meg kell hozzáfűzi a kapcsolati karakterlánc a következő formátumban:

    Database=<CosmosDB Database>;

> [!NOTE]
> A ellenőrizze parancs segítségével győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott Azure Cosmos DB-példány is elérhetők.
> 
> 

Egyetlen gyűjtemény importálásához adja meg, amelyhez adatok Importálja, és kattintson a Hozzáadás gombra a gyűjtemény nevét. Több gyűjteményre importálásához külön-külön adja meg az egyes kollekció nevét vagy az alábbi szintaxissal adhatja meg több gyűjteményt: *collection_prefix*[kezdő index - end index]. A fent említett szintaxis használatával több gyűjtemény meghatározásakor vegye figyelembe a következő:

1. Csak az egész tartomány neve minták támogatottak. Például adja meg a gyűjtemény [0 – 3] eredményez a következő gyűjteményeket: collection0, collection1, collection2, collection3.
2. Használhat egy rövidített Szintaxis: gyűjtemény [3] fog kibocsátás ugyanazokat a gyűjtemények az 1. lépésben említett.
3. Egynél több helyettesítési megadható. Például [0-1] [0-9] gyűjteményt hoz létre a 20 gyűjteménynevek vezető nullák (collection01,... 02... (03).

A gyűjtemény neve megadása után válassza ki a kívánt átviteli sebességgel (10 000 RUs a 400 RUs) a következő gyűjtemény(ek) készleteit szinkronizálja. Importálás legjobb teljesítmény érdekében válasszon egy nagyobb átviteli sebesség. Teljesítmény szintekkel kapcsolatos további információkért lásd: [teljesítményszintek az Azure Cosmos Adatbázisba](performance-levels.md).

> [!NOTE]
> A teljesítmény beállítását csak a webhelycsoport létrehozása vonatkozik. A megadott gyűjtemény már létezik, az átviteli sebesség nem módosítható.
> 
> 

Több gyűjteményre importálásakor a importálási eszköz által támogatott kivonatoló alapú horizontális. Ebben az esetben adja meg a partíciókulcsnak használni kívánt tulajdonság (Ha üresen marad partíciós kulcs, dokumentumok lesz szilánkos véletlenszerűen a célként megadott gyűjtemények között).

Opcionálisan megadhat melyik mezőt a importálási forrásból (vegye figyelembe, hogy ha dokumentumok nem tartalmazzák ezt a tulajdonságot, majd az importálási eszköz létrehoz egy GUID azonosító tulajdonság értékeként) importálásakor kell használható az Azure Cosmos adatbázis-azonosító tulajdonsággal.

Nincsenek speciális beállítások számos elérhető importálása során. Először amíg az eszköz tartalmaz alapértelmezett tömeges importálásához tárolt eljárás (BulkInsert.js), választhatja a saját importálási tárolt eljárás megadása:

 ![Képernyőfelvétel az Azure Cosmos DB tömeges beszúrási sproc beállítást](./media/import-data/bulkinsertsp.png)

Emellett dátum típusú (pl. az SQL Server vagy a MongoDB) importálásakor választhat három importálási beállításokat:

 ![Képernyőfelvétel az Azure Cosmos DB dátumbeállításainak idő importálása](./media/import-data/datetimeoptions.png)

* Karakterlánc: Egy karakterláncértéket áll fenn
* Epoch: Egy érték szám Epoch áll fenn
* Mindkét: Továbbra is fennáll, karakterlánc és a Epoch számértékeit. Ez a beállítás létrehoz aldokumentum, például: "date_joined": {"Érték": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245}

Az Azure Cosmos DB tömeges importáló a következő további speciális beállítások rendelkezik:

1. Köteg mérete: Az eszköz az alapértelmezett érték a Köteg mérete 50.  Ha a dokumentumokat, importálandók nagy, fontolja meg a köteg méretének csökkentésével. Ezzel szemben ha a dokumentumokat, importálandók kicsi, fontolja meg a köteg méretének emelés.
2. Max. parancsfájl mérete (bájt): az eszköz az alapértelmezett 512KB parancsfájl maximális mérete
3. Tiltsa le automatikus azonosító létrehozása: Ha minden dokumentumot, importálandók azonosító mezőjéhez tartalmaz, majd ezzel a beállítással növelheti a teljesítményt. Hiányzik egy egyedi azonosító mező dokumentumok lesz importálva.
4. Frissítés meglévő dokumentumok: Az eszköz az alapértelmezett érték nem azonosító ütközik a meglévő dokumentumok cseréje. Ezzel a beállítással lehetővé teszi az egyező azonosítók felülírja a meglévő dokumentumokat. A szolgáltatás akkor hasznos, amelyek frissítik a dokumentumok meglévő ütemezett áttelepítésre.
5. Hiba újbóli próbálkozások számát: a kapcsolat az Azure Cosmos Adatbázishoz (pl.: hálózati kapcsolat megszakadása) átmeneti hibák esetén próbálkozások számát adja meg.
6. Újrapróbálkozási időköz: Azt határozza meg mennyi ideig várjon, amíg a kapcsolat az Azure Cosmos Adatbázishoz (pl.: hálózati kapcsolat megszakadása) átmeneti hibák esetén újrapróbálkozása között.
7. Csatlakozási mód: A csatlakozási mód használata Azure Cosmos DB határozza meg. A lehetséges értékek DirectTcp, DirectHttps és az átjáró. A közvetlen kapcsolat módok a következők: gyorsabb, amíg az átjáró mód rövid több tűzfal, mert csak 443-as portot.

![Képernyőfelvétel az Azure Cosmos DB tömeges importálással speciális beállítások](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> Az import eszközt az alapértelmezett csatlakozási mód DirectTcp. Ha tűzfal problémák, kapcsolat módba vált-átjárón csak 443-as portra van szüksége.
> 
> 

## <a id="DocumentDBSeqTarget"></a>A DocumentDB-API (egymást követő rekord importálása) importálása
Az Azure Cosmos DB szekvenciális rekord importáló lehetővé teszi, hogy a rekord által rekord alapon az elérhető forráskiszolgálók beállításokat importálhat. Akkor célszerű használni ezt a beállítást, ha importál egy meglevő gyűjteményhez éri el a tárolt eljárásokra vonatkozó kvótáját. Az eszköz támogatja az importálás (egypartíciós és több partíció) egyetlen Azure Cosmos DB gyűjteménybe, valamint a szilánkos importálása, amelynek során az adatok több egypartíciós és/vagy több partíció Azure Cosmos DB gyűjtemények között particionált van. Adatok partícionálásra vonatkozó további információkért lásd: [particionálás és az Azure Cosmos Adatbázisba skálázás](partition-data.md).

![Képernyőfelvétel az Azure Cosmos DB szekvenciális rekord importálási beállítások](./media/import-data/documentdbsequential.png)

Az Azure Cosmos DB kapcsolati karakterlánc formátuma:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A Azure Cosmos DB fiók kapcsolati karakterláncot az Azure-portálon (kulcsok) panelén lekérhetők, a [Azure Cosmos DB fiók kezelése](manage-account.md), azonban annak az adatbázisnak a nevét meg kell hozzáfűzi a kapcsolati karakterlánc a következő formátumban:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> A ellenőrizze parancs segítségével győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott Azure Cosmos DB-példány is elérhetők.
> 
> 

Egyetlen gyűjtemény importálásához adja meg, amelyhez adatok Importálja, és kattintson a Hozzáadás gombra a gyűjtemény nevét. Több gyűjteményre importálásához külön-külön adja meg az egyes kollekció nevét vagy az alábbi szintaxissal adhatja meg több gyűjteményt: *collection_prefix*[kezdő index - end index]. A fent említett szintaxis használatával több gyűjtemény meghatározásakor vegye figyelembe a következő:

1. Csak az egész tartomány neve minták támogatottak. Például adja meg a gyűjtemény [0 – 3] eredményez a következő gyűjteményeket: collection0, collection1, collection2, collection3.
2. Használhat egy rövidített Szintaxis: gyűjtemény [3] fog kibocsátás ugyanazokat a gyűjtemények az 1. lépésben említett.
3. Egynél több helyettesítési megadható. Például [0-1] [0-9] gyűjteményt hoz létre a 20 gyűjteménynevek vezető nullák (collection01,... 02... (03).

A gyűjtemény neve megadása után válassza ki a kívánt átviteli sebességgel (a 250 000 RUs 400 RUs) a következő gyűjtemény(ek) készleteit szinkronizálja. Importálás legjobb teljesítmény érdekében válasszon egy nagyobb átviteli sebesség. Teljesítmény szintekkel kapcsolatos további információkért lásd: [teljesítményszintek az Azure Cosmos Adatbázisba](performance-levels.md). Bármely importálás átviteli gyűjtemények > 10 000 RUs partíciós kulcs szükséges. Ha több mint 250 000 RUs választ, akkor küldje el a kérést a portálon növelni a fiókját.

> [!NOTE]
> Az átviteli sebesség beállítás csak a webhelycsoport létrehozása vonatkozik. A megadott gyűjtemény már létezik, az átviteli sebesség nem módosítható.
> 
> 

Több gyűjteményre importálásakor a importálási eszköz által támogatott kivonatoló alapú horizontális. Ebben az esetben adja meg a partíciókulcsnak használni kívánt tulajdonság (Ha üresen marad partíciós kulcs, dokumentumok lesz szilánkos véletlenszerűen a célként megadott gyűjtemények között).

Opcionálisan megadhat melyik mezőt a importálási forrásból (vegye figyelembe, hogy ha dokumentumok nem tartalmazzák ezt a tulajdonságot, majd az importálási eszköz létrehoz egy GUID azonosító tulajdonság értékeként) importálásakor kell használható az Azure Cosmos adatbázis-azonosító tulajdonsággal.

Nincsenek speciális beállítások számos elérhető importálása során. Első lépésként dátum típusú (pl. az SQL Server vagy a MongoDB) importálásakor választhat három importálási beállításokat:

 ![Képernyőfelvétel az Azure Cosmos DB dátumbeállításainak idő importálása](./media/import-data/datetimeoptions.png)

* Karakterlánc: Egy karakterláncértéket áll fenn
* Epoch: Egy érték szám Epoch áll fenn
* Mindkét: Továbbra is fennáll, karakterlánc és a Epoch számértékeit. Ez a beállítás létrehoz aldokumentum, például: "date_joined": {"Érték": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245}

Az Azure Cosmos DB - szekvenciális rekord importáló a következő további speciális beállításokat tartalmaz:

1. Párhuzamos kérelem: az eszköz az alapértelmezett 2 párhuzamos kérelmeket. Ha a dokumentumokat, importálandók kicsi, fontolja meg előléptetése párhuzamos kérelmek számát jelenti. Vegye figyelembe, hogy ez a szám túl sok következik be, ha az importálás problémákat tapasztalhat a sávszélesség-szabályozás.
2. Tiltsa le automatikus azonosító létrehozása: Ha minden dokumentumot, importálandók azonosító mezőjéhez tartalmaz, majd ezzel a beállítással növelheti a teljesítményt. Hiányzik egy egyedi azonosító mező dokumentumok lesz importálva.
3. Frissítés meglévő dokumentumok: Az eszköz az alapértelmezett érték nem azonosító ütközik a meglévő dokumentumok cseréje. Ezzel a beállítással lehetővé teszi az egyező azonosítók felülírja a meglévő dokumentumokat. A szolgáltatás akkor hasznos, amelyek frissítik a dokumentumok meglévő ütemezett áttelepítésre.
4. Hiba újbóli próbálkozások számát: a kapcsolat az Azure Cosmos Adatbázishoz (pl.: hálózati kapcsolat megszakadása) átmeneti hibák esetén próbálkozások számát adja meg.
5. Újrapróbálkozási időköz: Azt határozza meg mennyi ideig várjon, amíg a kapcsolat az Azure Cosmos Adatbázishoz (pl.: hálózati kapcsolat megszakadása) átmeneti hibák esetén újrapróbálkozása között.
6. Csatlakozási mód: A csatlakozási mód használata Azure Cosmos DB határozza meg. A lehetséges értékek DirectTcp, DirectHttps és az átjáró. A közvetlen kapcsolat módok a következők: gyorsabb, amíg az átjáró mód rövid több tűzfal, mert csak 443-as portot.

![Képernyőfelvétel az Azure Cosmos DB szekvenciális rekord importálása speciális beállítások](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> Az import eszközt az alapértelmezett csatlakozási mód DirectTcp. Ha tűzfal problémák, kapcsolat módba vált-átjárón csak 443-as portra van szüksége.
> 
> 

## <a id="IndexingPolicy"></a>Adjon meg egy indexelési házirendet, amikor Azure Cosmos DB gyűjtemények létrehozása
Ha engedélyezi az áttelepítési eszköz importálása során gyűjtemények létrehozásához, a gyűjtemények az indexelési házirendet is megadhat. A Speciális beállítások szakaszban Azure Cosmos DB tömeges importálással és Azure Cosmos DB szekvenciális rekord beállítások keresse meg az indexelési házirendet szakaszban.

![Képernyőfelvétel az Azure Cosmos DB indexelő házirend Speciális beállítások](./media/import-data/indexingpolicy1.png)

A speciális beállítás indexelési házirendet használja, akkor is indexelési házirend fájlt válasszon ki, manuálisan adja meg az indexelési házirendet vagy kattintva válassza ki az alapértelmezett sablonok közül (jobb gombbal az indexelési házirendet szövegmezőjének).

A sablonok az eszközt biztosít a következők:

* Alapértelmezés szerint. Ez a házirend akkor ajánlott, ha Ön karakterláncok egyenlőséglekérdezéséhez végrehajtása és a számok ORDER BY, tartomány és egyenlőség lekérdezések használatával. Ez a házirend rendelkezik egy alacsonyabb indextárolási terheléssel jár mint tartomány.
* Tartomány. Ezzel a házirend-érdemes a számok és karakterláncok ORDER BY, tartomány-és egyenlőséglekérdezéséhez használ. Ez a házirend egy magasabb indextárolási terheléssel jár, mint az alapértelmezett vagy az ujjlenyomat-rendelkezik.

![Képernyőfelvétel az Azure Cosmos DB indexelő házirend Speciális beállítások](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Ha nem adja meg az indexelési házirendet, majd az alapértelmezett házirend lépnek érvénybe. Az indexelő házirendekkel kapcsolatos további információkért lásd: [Azure Cosmos DB házirendek indexelő](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>JSON-fájl exportálása
Az Azure Cosmos DB JSON-exportáló lehetővé teszi a JSON-dokumentumok bájttömb tartalmazó JSON-fájlba exportálja az elérhető forráskiszolgálók beállításokat. Az eszköz az Exportálás meg fogja kezelni, vagy ha szeretné megtekinteni az eredményül kapott áttelepítési parancsot, és futtassa a parancsot. Az eredményül kapott JSON-fájl helyben vagy az Azure Blob storage tárolhatók.

![Az Azure Cosmos DB JSON képernyőkép helyi fájl exportálási beállítás](./media/import-data/jsontarget.png)

![Képernyőkép Azure Cosmos DB JSON Azure Blob storage exportálási beállítás](./media/import-data/jsontarget2.png)

Opcionálisan választhatja az eredményül kapott JSON, ami növeli az eredményül kapott dokumentum méretét a tartalom több tétele közben prettify emberi olvasható.

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
A speciális konfigurálására szolgáló képernyőn adja meg a naplófájlt, amelyet szeretne írt hibák helyét. Ezen a lapon az alábbi szabályok vonatkoznak:

1. Ha egy fájl neve nem áll rendelkezésre, majd a program az összes hiba visszatér az eredmények lapon.
2. Ha a fájl nevét a címtár nélkül áll rendelkezésre, majd a fájl létrejön (vagy felül) az aktuális környezet könyvtárban található.
3. Ha egy meglévő fájlt, majd a fájl felül lesznek írva, nincs append beállítás.

Ezután válassza naplózását, kritikus, vagy a hibaüzeneteket. Végül döntse el, hogy milyen gyakran az a képernyő átviteli üzenet frissíti a rendszer a telepítés előrehaladását.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Erősítse meg a beállításokat importálhat és parancs sor megtekintése
1. Adja meg az adatforrásra vonatkozó információ, a cél adatokat és a speciális konfigurációs, áttekintheti összefoglaló az áttelepítés, és szükség esetén megtekintése és másolása az eredményül kapott áttelepítési parancsot (a parancs másolás akkor hasznos, importálási műveletek automatizálásához):
   
    ![Képernyőkép a összegző képernyő](./media/import-data/summary.png)
   
    ![Képernyőkép a összegző képernyő](./media/import-data/summarycommand.png)
2. Ha elégedett a forrás- és beállításokat, kattintson **importálási**. Az eltelt idő, átvitt száma és hibaadatokat (Ha nem adott meg egy fájlnevet a Speciális konfiguráció) frissíti az importálás folyamatban van. Művelet befejeződése után exportálhatja az eredményeket (pl. importálási hibák kezelése érdekében).
   
    ![Az Azure Cosmos DB JSON képernyőkép exportálási beállítás](./media/import-data/viewresults.png)
3. Egy új importálhat indulhat tartani a meglévő beállítások (például kapcsolati karakterlánc adatokat, a forrás és cél kiválasztása, stb.), vagy alaphelyzetbe állítása az összes értéket.
   
    ![Az Azure Cosmos DB JSON képernyőkép exportálási beállítás](./media/import-data/newimport.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Az adatáttelepítési eszköz telepítése
> * Különböző adatforrásokból importált adatok
> * Exportált Azure Cosmos DB JSON

Most már továbbléphet a következő oktatóanyagot, és megtudhatja, hogyan kérdezhet le adatokat Azure Cosmos DB használatával. 

> [!div class="nextstepaction"]
>[Hogyan kérdezhet le adatokat?](../cosmos-db/tutorial-query-documentdb.md)
