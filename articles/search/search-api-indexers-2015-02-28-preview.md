---
title: "Az indexelési műveletek (az Azure Search szolgáltatás REST API-ja: 2015-02-28 – előzetes verzió) |} Microsoft Docs"
description: "Az indexelési műveletek (az Azure Search szolgáltatás REST API-ja: 2015-02-28 – előzetes verzió)"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 42b5f85c-8304-4bc7-8e1e-a9c76b8ca25a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: eugenesh
ms.openlocfilehash: 356ceb98106d080d8c24dedc3547bee33750156e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Az indexelési műveletek (az Azure Search szolgáltatás REST API-ja: 2015-02-28 – előzetes verzió)
> [!NOTE]
> Ez a cikk ismerteti az indexelők a [2015-02-28-Preview REST API](search-api-2015-02-28-preview.md). Az API verziója ad hozzá a dokumentum kibontási az Azure Blob Storage indexelő és az Azure Table Storage indexelő, valamint az egyéb fejlesztések előzetes verziói. Az API-t (GA) általánosan elérhető indexelő, beleértve az indexelők az Azure SQL Database, SQL Server Azure virtuális gépeken, és Azure Cosmos DB is támogatja.
> 
> 

## <a name="overview"></a>Áttekintés
Az Azure Search integrálható közvetlenül néhány általános adatforrás, az adatok indexelését kód írása szükség. A működési beállításához létrehozása és kezelése az Azure Search API hívása **indexelők** és **adatforrások**. 

Egy **indexelő** kapcsoló cél keresési indexek, az adatforrások erőforrás. Az indexelő a következő módon használható: 

* Hajtsa végre az adatokat tölthet fel indexet egyszeri másolatát.
* Egy index ugyanezzel az adatforrás ütemezés szerint módosítások szinkronizálása. Az ütemezés az indexelő definíció részét képezi.
* Hívja meg az index frissítése, igény szerint igény. 

Egy **indexelő** akkor hasznos, ha azt szeretné, hogy indexek rendszeres frissítéseket. Az indexelő definíciójának részeként egy beágyazott ütemezés beállítása, vagy futtassa az igény szerinti használatával [indexelő futtatása](#RunIndexer). 

A **adatforrás** határozza meg, milyen adatokat kell indexelése, hozzáférhet az adatokhoz és a házirendek ahhoz, hogy az adatok (például a módosított vagy törölt sorok egy adatbázis tábláinak) változásai hatékonyan azonosításához az Azure Search-felhasználó hitelesítő adatait. Az definiálva van egy független erőforrásként, hogy több indexelők használható.

A következő adatforrások jelenleg támogatottak:

* **Az Azure SQL adatbázis** és **az Azure virtuális gépeken futó SQL Server**. A célként megadott segédlet, lásd: [Ez a cikk](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 
* **Az Azure Cosmos DB**. A célként megadott segédlet, lásd: [Ez a cikk](search-howto-index-documentdb.md). 
* **Az Azure Blob Storage**, beleértve a következő dokumentum formátumok: PDF, Microsoft Office (DOCX/DOC, XSLX/XLS, PPTX/PPT, Adatköltségek), HTML, XML, ZIP és egyszerű szövegként fájlok (beleértve a JSON). A célként megadott segédlet, lásd: [Ez a cikk](search-howto-indexing-azure-blob-storage.md).
* **Az Azure Table Storage**. A célként megadott segédlet, lásd: [Ez a cikk](search-howto-indexing-azure-tables.md).

A Microsoft fontolóra vette, a jövőben hozzáadása a további adatforrások támogatása. Ezek a döntések sorrendjének érdekében adja meg a visszajelzést a a [Azure Search-visszajelzési fórumon](http://feedback.azure.com/forums/263029-azure-search).

Lásd: [szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md) az indexelőt és az adatok forrása erőforrásokkal kapcsolatos határok.

## <a name="typical-usage-flow"></a>Tipikus használati folyamata
Létrehozhat és egyszerű HTTP-kérelmek (POST, GET, PUT, DELETE) keresztül indexelők és adatforrások kezelése szemben egy adott `data source` vagy `indexer` erőforrás.

Automatikus indexelő beállítása az általában egy négy lépésben folyamat:

1. Azonosítsa az adatforrás, amely tartalmazza a indexelése szükséges adatokat. Ne feledje, hogy Azure Search előfordulhat, hogy nem támogatja az összes adatforrás található adatok típusát. Lásd: [a támogatott adattípusokat](https://msdn.microsoft.com/library/azure/dn798938.aspx) listája.
2. Hozzon létre egy Azure Search-index, amelynek séma összeegyeztethető az adatokat az adatforrásból.
3. Hozzon létre egy Azure Search adatforrás leírtak [adatforrás létrehozása](#CreateDataSource).
4. Hozzon létre egy Azure Search-indexelőt, leírtak [létrehozása indexelő](#CreateIndexer).

Kell egy indexelő minden célként megadott index és az adatforrás kombinációjához létrehozását tervezi. Az azonos indexbe írása több indexelők is rendelkezik, és ugyanarra az adatforrásra vonatkozó több indexelők is felhasználhatja. Az indexelő azonban csak vehet igénybe, egyszerre több adatforrást, és csak egyetlen index írhat. 

Miután létrehozta az indexelő, kérheti le a végrehajtási állapot a [indexelő állapotának beolvasása](#GetIndexerStatus) műveletet. Az indexelő minden alkalommal (helyett vagy annak kiegészítéseként rendszeres időközönként futó ütemezett) az is futtathatja a [indexelő futtatása](#RunIndexer) műveletet.

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Adatforrás létrehozása
Az Azure Search használt adatforrás indexelők, adja meg a célként megadott index az alkalmi vagy ütemezett Adatfrissítés kapcsolati adatokat. Létrehozhat egy új adatforrás belül az Azure Search szolgáltatást használja a HTTP POST-kérelmet.

    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Azt is megteheti PUT használja, és adja meg az adatforrás neve URI-n. Ha az adatforrás nem létezik, a rendszer létrehozza.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

> [!NOTE]
> Engedélyezett adatforrások maximális számának függ a tarifacsomagra vált. Az ingyenes szolgáltatás lehetővé teszi, hogy legfeljebb 3 adatforrások. Standard szolgáltatás lehetővé teszi, hogy 50 adatforrások. Lásd: [szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md) részleteiről.
> 
> 

**Kérés**

HTTPS-kapcsolat szükséges az összes szolgáltatási kérelmek. A **adatforrás létrehozása** kérelem egy POST vagy a PUT metódust használó lehet létrehozni. POST használatakor meg kell adnia egy adatforrás neve mellett az adatforrása definíciója a kérés törzsében. A PUT a név része az URL-címet. Ha az adatforrás nem létezik, akkor jön létre. Ha már létezik, az új definition frissül. 

Az adatforrás nevét kell kell kisbetű, betűvel vagy számmal kezdődhet, nincs perjeleket vagy pontokból és 128 karakternél rövidebb lehet. Után az adatforrás nevének betűvel vagy számmal verziótól kezdődően a nevét a többi tartalmazhatnak bármely betű, szám és kötőjeleket, mindaddig, amíg a kötőjelek nincsenek egymást követő. Lásd: [elnevezési szabályait](https://msdn.microsoft.com/library/azure/dn857353.aspx) részleteiről.

A `api-version` szükséges. A jelenlegi verzió: `2015-02-28`.

**Kérelem fejlécei**

Az alábbi lista ismerteti a szükséges és választható kérelemfejlécekben. 

* `Content-Type`: Kötelező. Állítsa ezt a beállítást`application/json`
* `api-key`: Kötelező. A `api-key` hitelesíteni a kérelmet a keresőszolgáltatása szolgál. Egy karakterláncértéket, a szolgáltatás egyedi. A **adatforrás létrehozása** kérelem tartalmaznia kell egy `api-key` fejlécben az adminisztrációs kulcsot (nem egy lekérdezési kulcsot). 

Konfigurálnia kell a szolgáltatás nevét, a kérelem URL-címe összeállításához. A szolgáltatás nevet is kaphat és `api-key` a szolgáltatás irányítópultján a a [Azure Portal](https://portal.azure.com/). Lásd: [Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) navigációs segítségét.

<a name="CreateDataSourceRequestSyntax"></a>
**Törzs szintaxis**

A kérelem törzsében egy adatforrás-definíciót, amely tartalmazza az adatforrás típusú hitelesítő adatokat, olvassa el az adatokat, valamint egy nem kötelező az észlelés módosításához, és az adatok törlését szabályzatok, amely hatékonyan azonosítják módosítható vagy törölhető az adatforrás egy rendszeresen ütemezett indexelő használata esetén az adatokat. 

A szerkezetének kialakítása a kérelem hasznos szintaxisa a következő. Egy minta kérelem biztosítja az ebben a témakörben további.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Kérelem tartalmazza a következő tulajdonságokkal: 

* `name`: Kötelező. Az adatforrás neve. Adatforrás nevét kell csak kisbetűk, számjegyek és kötőjelek szerepelhetnek, nem kezdődhet vagy végződhet kötőjelekből és legfeljebb 128 karakter hosszú lehet.
* `description`: Egy leírást. 
* `type`: Kötelező. A támogatott adatforrásokat valamelyike lehet:
  * `azuresql`-Azure SQL Database vagy az SQL Server Azure virtuális gépeken
  * `documentdb`-Azure Cosmos DB
  * `azureblob`-Azure Blobtárolóba
  * `azuretable`-Azure Táblatárolói
* `credentials`:
  * A szükséges `connectionString` tulajdonság határozza meg az adatforrás kapcsolati karakterláncában. A kapcsolati karakterlánc formátuma attól függ, hogy az adatforrás típusa: 
    * Azure SQL ez pedig a szokásos SQL Server kapcsolati karakterláncát. Ha az Azure portál segítségével kéri le a kapcsolati karakterláncot, használja a `ADO.NET connection string` lehetőséget.
    * Az Azure Cosmos DB, a kapcsolati karakterláncnak kell lennie, a következő formátumban: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Összes értéket szükség. Megtalálhatja azokat a [Azure-portálon](https://portal.azure.com/).  
    * Azure-Blob és Table Storage ez pedig a tárolási fiók kapcsolati karakterlánc. A formátum leírt [Itt](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). HTTPS-végpont protokoll megadása kötelező.  
* `container`, szükséges: az adatok indexelését használatával a `name` és `query` tulajdonságok: 
  * `name`, szükséges:
    * Az Azure SQL: Megadja a tábla vagy nézet. Használhatja például a séma minősített nevek, `[dbo].[mytable]`.
    * A DocumentDB: azt a gyűjteményt. 
    * Az Azure Blob Storage: Adja meg a tároló.
    * Az Azure Table Storage: Megadja a tábla neve. 
  * `query`, nem kötelező:
    * A DocumentDB: Adjon meg egy lekérdezést, amely egy tetszőleges JSON-dokumentum elrendezés simítja Azure Search indexelheti strukturálatlan sémába teszi lehetővé.  
    * Az Azure Blob Storage: lehetővé teszi a blob-tároló virtuális mappában megadását. Például blob elérési út `mycontainer/documents/blob.pdf`, `documents` virtuális mappa használható.
    * Az Azure Table Storage: lehetővé teszi egy lekérdezést, amely a partíciókezelőt, importálandók szűrők megadását.
    * Az Azure SQL: a lekérdezés nem támogatott. Ha ez a funkció van szüksége, adjon szavazzon [Ez a javaslat](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
* A választható `dataChangeDetectionPolicy` és `dataDeletionDetectionPolicy` tulajdonságok az alábbiakban található.

<a name="DataChangeDetectionPolicies"></a>
**Adatok módosítása szabályzatok**

A szabályzat adatok módosítása célja megváltozott adatelemek hatékonyan azonosításához. Támogatott házirendek adatforrás típusának függően változhat. Az alábbi szakaszok ismertetik a minden egyes házirend. 

***Magas vízjel módosítása szabályzat*** 

Ez a házirend felhasználhatja az adatforrást tartalmaz egy oszlop vagy egy tulajdonság, amely megfelel a következő feltételeknek:

* Minden Beszúrások adja meg az oszlop értékét. 
* Egy elem összes frissítését is módosíthatja az oszlop értéke. 
* Ez az oszlop értékének fokozódik minden módosításakor.
* Egy szűrési záradékot a következőhöz hasonló használó lekérdezések `WHERE [High Water Mark Column] > [Current High Water Mark Value]` hatékonyan hajtható végre.

Például, ha az Azure SQL adatforrásokat, egy indexelt használó `rowversion` oszlop a ideális jelölt a magas vízjel alapján házirenddel való használatra. 

Ez a házirend az alábbiak szerint adható meg:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

Ha Azure Cosmos DB-adatforrásokhoz, használnia kell a `_ts` Azure Cosmos DB által biztosított tulajdonság. 

Azure Blob adatforrásokat, Azure Search automatikusan használatakor használ magas vízjel módosítása egy blob last-modified időbélyeg; alapján szabályzat nem kell saját kezűleg ilyen egy házirend megadása.   

***Az SQL integrált szabályzat módosítása***

Ha az SQL-adatbázis támogatja [a változáskövetés](https://msdn.microsoft.com/library/bb933875.aspx), javasoljuk, hogy az SQL integrált módosítása követési házirenddel. Ez a házirend lehetővé teszi, hogy a leghatékonyabb változáskövetési, és lehetővé teszi az Azure Search törölt soraihoz anélkül, hogy a séma egy explicit "helyreállítható törlésre" oszlophoz való azonosításához.

Az integrált változáskövetés a következő SQL Server adatbázis-verziók kezdve támogatott: 

* SQL Server 2008 R2, SQL Server Azure virtuális gépeken használatakor.
* Az Azure SQL Database 12-es, az Azure SQL Database használata.  

Ha a házirend az SQL integrált változáskövetés ne adjon meg egy külön adatok törlési szabályzat – a ezzel a házirend-azonosító beépített támogatása törölt sorok. 

Ez a házirend csak akkor használható táblákkal; a nézetek nem használható. Ez a házirend használata előtt használja a következő táblázatban változáskövetés engedélyezhető kell. Lásd: [engedélyezése és letiltása a változáskövetés](https://msdn.microsoft.com/library/bb964713.aspx) utasításokat.    

Felépítésekor a **adatforrás létrehozása** kérelem, az SQL integrált változáskövetés házirend adható meg az alábbiak szerint:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Adatok törlése szabályzatok**

A szabályzat adatok törlését célja törölt adatelemek hatékonyan azonosításához. Az egyetlen támogatott házirend jelenleg a `Soft Delete` házirendet, amely lehetővé teszi, hogy a törölt elemek értékének alapján azonosítja a `soft delete` oszlop vagy az adatforrás tulajdonság. Ez a házirend az alábbiak szerint adható meg:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

> [!NOTE]
> Karakterlánc, egész szám vagy logikai értékek csak oszlopok támogatottak. Az értéket, mint a `softDeleteMarkerValue` olyan karakterláncot kell megadni, akkor is, ha a szóban forgó oszlop egész számnak vagy logikai rendelkezik. Például, ha az adatforrás megjelenő értéke 1, használja `"1"` , a `softDeleteMarkerValue`.    
> 
> 

<a name="CreateDataSourceRequestExamples"></a>
**Példák a szervezet**

Ha szeretne használni az adatforrás az indexelő, amelyek ütemezés szerint fut, ez a példa bemutatja módosítása és törlése szabályzatok megadása: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Ha csak szeretne használni az adatforrás az adatok egyszeri példányát, a házirendek elhagyható:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Válasz**

A kérelem sikeres: 201 létrehozva. 

<a name="UpdateDataSource"></a>

## <a name="update-data-source"></a>Adatforrás frissítése.
Egy meglévő adatforráson egy HTTP PUT-kérelmet használatával frissítheti. A kérelem URI-azonosítója frissítéséhez az adatforrás nevét kell megadni:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

A `api-version` szükséges. A jelenlegi verzió: `2015-02-28`. [Az Azure Search API-verziók](https://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióival kapcsolatos további információk.

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Kérés**

A kérelem törzsében szintaxisa megegyezik a [adatforrás létrehozása kérelmek](#CreateDataSourceRequestSyntax).

Néhány tulajdonság nem lehet frissíteni, egy meglévő adatforráson. Például egy meglévő adatforráson típusa nem módosíthatja.  

Ha nem szeretné módosítani egy meglévő adatforrás kapcsolati karakterláncában, megadhatja a szövegkonstans `<unchanged>` a kapcsolódási karakterláncban. Ez akkor hasznos, ha szükség egy adatainak frissítése forrás-, de nincs kényelmes hozzáférése a kapcsolati karakterlánc, mivel ez biztonsági szempontból kényes adatokat.

**Válasz**

A kérelem sikeres: 201 létrehozva, ha új adatforrás nem létezik, és 204 nem tartalom Ha egy meglévő adatforráson frissítették.

<a name="ListDataSource"></a>

## <a name="list-data-sources"></a>Az adatforrások listája
A **lista adatforrások** művelet az Azure Search szolgáltatás adatforrások listáját adja vissza. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

A `api-version` szükséges. A jelenlegi verzió: `2015-02-28`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

A kérelem sikeres: 200 OK gombra.

Íme egy példa adott válasz törzse:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Vegye figyelembe, hogy a válasz le kíváncsiak vagyunk tulajdonságainak szűrheti. Például, ha azt szeretné, hogy csak az adatforrások nevének listája, használja az OData `$select` lekérdezési lehetőség:

    GET /datasources?api-version=205-02-28&$select=name

Ebben az esetben a fenti példa válaszát távolságban jelenjen meg az alábbiak szerint: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Ez a sávszélesség menteni, ha szerepel a keresőszolgáltatása adatforrások számos hasznos technika.

<a name="GetDataSource"></a>

## <a name="get-data-source"></a>Az adatforrás beolvasása
A **beolvasni az adatforrás** művelet lekérdezi az adatforrása definíciója az Azure Search.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

A `api-version` szükséges. A jelenlegi verzió: `2015-02-28`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

A válaszban szereplő példák hasonlít [adatforrás létrehozása példa kérelmek](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [!NOTE]
> Ne adja meg a `Accept` a kérelem fejlécében `application/json;odata.metadata=none` amikor hívja az API-t, így hatására `@odata.type` attribútumot kell nincs megadva, a válasz, és nem fog tudni különböztetheti meg az adatok változás- és adatok törlését a szabályzatok különböző típusok. 
> 
> 

<a name="DeleteDataSource"></a>

## <a name="delete-data-source"></a>Az adatforrás törlése
A **adatforrás törlése** művelet adatforrást eltávolítja az Azure Search szolgáltatás.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> Ha bármely indexelők a törölni kívánt adatforráshoz hivatkozik, a delete művelet továbbra is folytatódik. Azonban ezen indexelők ismerhetik hibaállapotba után a következő futtatáskor.  
> 
> 

A `api-version` szükséges. A jelenlegi verzió: `2015-02-28`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

Állapotkód: 204 nem tartalom visszaküldött a sikeres válasz.

<a name="CreateIndexer"></a>

## <a name="create-indexer"></a>Az indexelő létrehozása
Létrehozhat egy új indexelő belül az Azure Search szolgáltatást használja a HTTP POST-kérelmet.

    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Azt is megteheti PUT használja, és adja meg az adatforrás neve URI-n. Ha az adatforrás nem létezik, a rendszer létrehozza.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [!NOTE]
> IP-címek függ az indexelők engedélyezett maximális számát. Az ingyenes szolgáltatás lehetővé teszi, hogy a 3 indexelők. Standard szolgáltatás lehetővé teszi, hogy 50 indexelők. Lásd: [szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md) részleteiről.
> 
> 

A `api-version` szükséges. A jelenlegi verzió: `2015-02-28`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

<a name="CreateIndexerRequestSyntax"></a>
**Törzs szintaxis**

A kérelem törzsében egy indexelő definíciót, amely meghatározza az adatforrás és a cél indexe indexelő, valamint a nem kötelező indexelési ütemezés és a paraméterek. 

A szerkezetének kialakítása a kérelem hasznos szintaxisa a következő. Egy minta kérelem biztosítja az ebben a témakörben további.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Az indexelő ütemezése**

Az indexelő opcionálisan megadható ütemezés szerint. Ha az ütemezés szerint meg adva, az indexelő ütemezés szerint rendszeresen fog futni. Ütemezés a következő attribútumokkal rendelkezik:

* `interval`: Kötelező. Egy időtartamértéket, amely megadja az időközt vagy az indexelő időszak fut. A legkisebb megengedett intervallum értéke 5 perc; a leghosszabb egy nap. Az XSD "daytimeduration típusú" értéknek kell formázni (korlátozott részhalmaza egy [ISO 8601 időtartama](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték). A minta: `"P[nD][T[nH][nM]]"`. Példák: `PT15M` a 15 percenként `PT2H` minden 2 órán át. 
* `startTime`: Kötelező. Az indexelő kell elindultak, amikor az UTC datetime. 

**Az indexelő paraméterek**

Az indexelő Megadja annak viselkedését befolyásoló több paramétert. A paraméterek mindegyike nem kötelező.  

* `maxFailedItems`: Az elemek száma, amelyek egy indexelő futtatása tekinthető hiba indexelése sikertelen lehet. Alapértelmezett érték 0. Sikertelen elemek információt ad vissza a [indexelő állapotának beolvasása](#GetIndexerStatus) műveletet. 
* `maxFailedItemsPerBatch`: Az elemek száma, amelyek az egyes kötegekben, az indexelő futtatása előtt indexelése meghiúsulhat van tekintve. Alapértelmezett érték 0.
* `base64EncodeKeys`: Adja meg, hogy dokumentum kulcsok base-64 kódolású legyen-e. Az Azure Search dokumentumkulcsban használható dokumentum kulcsban. Azonban az értékeket a forrásadatokban tartalmazhat érvénytelen karaktereket. Ha ilyen értékek dokumentum kulcsként indexelésre, ezzel a jelzővel állítható be igaz értékre. Alapértelmezett érték a `false`.
* `batchSize`: Adja meg az elemek száma, amelyek az adatforrásból beolvasása és indexelt a kötegek teljesítményének javítása érdekében. Az alapértelmezett függ az adatforrás típusa: Azure SQL és az Azure Cosmos DB 1000, és az Azure Blob Storage 10.

**A mező hozzárendelések**

Mező leképezéseit segítségével egy mező neve az adatforrásban hozzárendelése a cél index másik mezőnevet. Vegyük példaként a forrástábla mezőt `_id`. Az Azure Search nem engedélyezi a mező kell átnevezni, aláhúzásjelet, kezdve mező nevét. Ehhez használja a `fieldMappings` tulajdonsága az indexelő az alábbiak szerint: 

    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Több mező leképezéseket adhat meg: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

A forrás- és cél mezőnevek nem különböztetik meg.

<a name="FieldMappingFunctions"></a>
***A mező leképezése***

Mező hozzárendelések is használható forrás mező értékét átalakítására *funkciók leképezési*.

Csak egy ilyen funkció jelenleg támogatott: `jsonArrayToStringCollection`. A cél index Collection(Edm.String) mezőbe egy JSON-tömb formátumú karakterláncot tartalmazó mező értelmezi. Célja való használathoz az Azure SQL indexelő különösen fontos, mivel az SQL natív adatok gyűjteménytípus nem rendelkezik. Az alábbiak szerint használható: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Például, ha a forrás mezőben a karakterlánc `["red", "white", "blue"]`, majd a cél mezőben típusú `Collection(Edm.String)` tölti fel a következő három érték `"red"`, `"white"` és `"blue"`.

Vegye figyelembe, hogy a `targetFieldName` tulajdonság nem kötelező megadni; Ha ezt nem fejeződött be, a `sourceFieldName` értéket használja. 

<a name="CreateIndexerRequestExamples"></a>
**Példák a szervezet**

Az alábbi példakód létrehozza indexelőt, amely által hivatkozott tábla adatait átmásolja a `ordersds` a forrás a `orders` index ütemezés szerint 2015 jan. 1 UTC elindul, és óránként futtat. Minden indexelő meghívás sikeres, ha 5-nél több elemek indexelése meghiúsulhat, ha az egyes kötegekben, és legfeljebb 10 elemet indexelése meghiúsulhat, ha összesen. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Válasz**

A kérelem sikeres 201 létrehozva.

<a name="UpdateIndexer"></a>

## <a name="update-indexer"></a>Indexelő frissítése
Egy meglévő indexelő egy HTTP PUT-kérelmet használatával frissítheti. A kérelem URI-azonosítója frissítéséhez az indexelő nevét kell megadni:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

A `api-version` szükséges. A jelenlegi verzió: `2015-02-28`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Kérés**

A kérelem törzsében szintaxisa megegyezik a [létrehozása indexelő kérelmek](#CreateIndexerRequestSyntax).

**Válasz**

A kérelem sikeres: 201 létrehozva, ha egy új indexelő nem létezik, és 204 nem tartalom Ha egy meglévő indexelő frissítették.

<a name="ListIndexers"></a>

## <a name="list-indexers"></a>Lista indexelő
A **lista indexelők** művelet az indexelők listájának az Azure Search szolgáltatás adja vissza. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


A `api-version` szükséges. Az előzetes verzió `2015-02-28-Preview`. [Az Azure Search versioning](https://msdn.microsoft.com/library/azure/dn864560.aspx) részletek és alternatív verzióival kapcsolatos további információk.

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

A kérelem sikeres: 200 OK gombra.

Íme egy példa adott válasz törzse:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Vegye figyelembe, hogy a válasz le kíváncsiak vagyunk tulajdonságainak szűrheti. Például, ha azt szeretné, hogy csak indexelőt neveinek listáját, használja az OData `$select` lekérdezési lehetőség:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Ebben az esetben a fenti példa válaszát távolságban jelenjen meg az alábbiak szerint: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Ez a sávszélesség menteni, ha szerepel a keresőszolgáltatása indexelő sok hasznos technika.

<a name="GetIndexer"></a>

## <a name="get-indexer"></a>Indexelt tartalom lekérése
A **beolvasása indexelő** művelet lekérdezi az indexelő meghatározása az Azure Search.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

A `api-version` szükséges. Az előzetes verzió `2015-02-28-Preview`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

Állapotkód: 200 OK visszaküldött a sikeres válasz.

A válaszban szereplő példák hasonlít [indexelő hozzon létre például kérelmek](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>

## <a name="delete-indexer"></a>Indexelő törlése
A **indexelő törlése** művelet eltávolítja az indexelő az Azure Search szolgáltatás.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Az indexelő törlésekor jelenleg folyamatban van az indexelő végrehajtások végrehajtása, de nincs további végrehajtások ütemezi. Kísérlet egy nem létező indexelő eredményez HTTP-állapotkód: 404 nem található. 

A `api-version` szükséges. Az előzetes verzió `2015-02-28-Preview`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

Állapotkód: 204 nem tartalom visszaküldött a sikeres válasz.

<a name="RunIndexer"></a>

## <a name="run-indexer"></a>Az indexelő futtatása
Rendszeres időközönként futó ütemezett, mellett az indexelő is el az igény szerinti keresztül a **indexelő futtatása** műveletet: 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

A `api-version` szükséges. Az előzetes verzió `2015-02-28-Preview`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

Állapotkód: 202 elfogadott visszaküldött a sikeres válasz.

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Az indexelő állapotának beolvasása
A **indexelő állapotának beolvasása** művelet lekérdezi az indexelő aktuális állapot és végrehajtási előzményeinek: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


A `api-version` szükséges. Az előzetes verzió `2015-02-28-Preview`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

Állapotkódja: az OK gombra a sikeres válasz 200-as.

Az adott válasz törzsének indexelő általános állapotát, az utolsó indexelő meghívása, valamint az indexelő legutóbbi indítások előzményeit információkat tartalmaz, (ha van ilyen). 

Egy minta adott válasz törzsének így néz ki: 

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

**Az indexelő állapota**

Az indexelő állapota a következő értékek egyike lehet:

* `running`azt jelzi, hogy az indexelő megfelelően fut-e. Megjegyzendő, hogy az indexelő végrehajtások némelyike még sikertelenségét, ezért célszerű ellenőrizni a `lastResult` tulajdonságot is. 
* `error`azt jelzi, hogy az indexelő emberi beavatkozás nélkül nem javítható hibába ütközött. Például előfordulhat, hogy az adatforrás hitelesítő adatainak lejárt, vagy az adatforrás vagy a célként megadott index a séma megváltozott a legfrissebb módon. 

**Az indexelő végrehajtás eredménye**

Az indexelő végrehajtás eredménye egy egyetlen indexelő végrehajtása adatait tartalmazza. A legújabb eredmény van illesztett, mint a `lastResult` tulajdonság indexelő állapotát. Más legutóbbi eredményeket, ha van ilyen, vissza, a `executionHistory` tulajdonság indexelő állapotát. 

Az indexelő végrehajtás eredménye tartalmazza a következő tulajdonságokkal: 

* `status`: egy végrehajtási állapotát. Lásd: [indexelő végrehajtásának állapota](#IndexerExecutionStatus) alábbi részleteket. 
* `errorMessage`: sikertelen végrehajtása hibaüzenetet. 
* `startTime`: a időpontja UTC a futtatását indításakor.
* `endTime`: a ideje UTC Formátumban, amikor a végrehajtása befejeződött. Ez az érték nem be, ha a végrehajtás folyamatban van.
* `errors`: elemszintű hibákat, ha van ilyen tömbjét. A leírásokban a dokumentum kulcsot tartalmaz (`key` tulajdonság) és egy hibaüzenetet (`errorMessage` tulajdonság). 
* `itemsProcessed`: az adatforrás, amely a végrehajtás során index megpróbálta az indexelő elemeket (például a táblázat sorait) a száma. 
* `itemsFailed`: az elemek száma, amelyek a végrehajtása során nem sikerült.  
* `initialTrackingState`: mindig `null` első indexelő végrehajtásának, vagy ha az adatok követési szabályzatának módosítása nem engedélyezett a használt adatforrás. Ilyen házirend engedélyezve van, ha a későbbi végrehajtások során ez az érték azt jelzi, az első (legalacsonyabb) változáskövetési érték dolgozza fel a végrehajtását. 
* `finalTrackingState`: mindig `null` Ha az adatok követési házirend módosítása nem engedélyezett a használt adatforrás. Ellenkező esetben azt jelzi, hogy a legújabb (legmagasabb) változáskövetési a végrehajtását sikeresen feldolgozott érték. 

<a name="IndexerExecutionStatus"></a>
**Az indexelő végrehajtásának állapota**

Az indexelő végrehajtásának állapota egy egyetlen indexelő végrehajtási állapotát rögzíti. Ez a következő értékeket veheti fel:

* `success`azt jelzi, hogy az indexelő végrehajtása sikeresen befejeződött.
* `inProgress`azt jelzi, hogy az indexelő végrehajtása folyamatban van. 
* `transientFailure`azt jelzi, hogy az indexelő végrehajtása sikertelen volt. Lásd: `errorMessage` részletek tulajdonság. A hiba lehetséges, hogy, illetve nem igényel megoldásának emberi beavatkozás - például a sémák nem kompatibilisek az adatforrás és a cél index közötti kijavítása szükség felhasználói beavatkozásra, míg egy ideiglenes adatok forrás üzemszünet nem. Indexelő indítások / ütemezés szerint folytatódik, ha ilyen. 
* `persistentFailure`azt jelzi, hogy az indexelő nem úgy, hogy az emberi beavatkozást igényelnek. Ütemezett indexelő végrehajtások leállítása. Miután elhárította a problémát, alaphelyzetbe állítja az indexelő API használatával indítsa újra az ütemezett végrehajtások. 
* `reset`azt jelzi, hogy az indexelő alaphelyzetbe állítása az indexelő API (lásd alább) hívásával alaphelyzetbe lett állítva. 

<a name="ResetIndexer"></a>

## <a name="reset-indexer"></a>Az indexelő alaphelyzetbe állítása
A **indexelő alaphelyzetbe állítása** művelet visszaállítja a változáskövetési állapotot az indexelőben társított. Ez lehetővé teszi elindítani a teljesen új műveletek (például, ha megváltozott a adatforrásséma), vagy módosíthatja az adatok módosítási szabályzat az indexelő társított egyes adatforrások esetében.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

A `api-version` szükséges. Az előzetes verzió `2015-02-28-Preview`. 

A `api-key` egy adminisztrációs kulcsot (szemben a lekérdezési kulcs) kell lennie. A hitelesítési részében [Search szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) kulcsok tájékozódhat. [A Search szolgáltatás létrehozása a portál](search-create-service-portal.md) a szolgáltatás URL-címe és a kérésben használt tulajdonságait ismerteti.

**Válasz**

Állapotkód: 204 a sikeres válasz tartalmát nem.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL-adattípusok és az Azure Search adattípusok közötti leképezést
<table style="font-size:12">
<tr>
<td>SQL-adattípus</td>    
<td>Cél index mezőtípusok engedélyezett</td>
<td>Megjegyzések</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>valódi, lebegőpontos</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>kis pénz típusú értékké, pénzt<br>Decimális<br>Numerikus
</td>
<td>Edm.String</td>
<td>Az Azure Search nem támogatja a decimális típusok konvertálásakor Edm.Double, mert ez elveszítik pontosság
</td>
</tr>
<tr>
<td>CHAR, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>Lásd: [mező leképezési funkciók](#FieldMappingFunctions) ebben a dokumentumban talál részletes karakterlánc oszlop átalakítása egy Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, dátum és idő, datetime2, dátum, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>földrajzi hely</td>
<td>Edm.GeographyPoint</td>
<td>Csak az srid-Azonosítónak 4326 (Ez az alapértelmezett) pontra típusú geográfiai példányban támogatottak.</td>
</tr>
<tr>
<td>ROWVERSION</td>
<td>N/A</td>
<td>Sor verziójú oszlopok nem lehet tárolni a search-index, de a változások követése is használhatók</td>
</tr>
<tr>
<td>a timespan idő<br>binary, varbinary, kép,<br>XML-geometriai, CLR-típus</td>
<td>N/A</td>
<td>Nem támogatott</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON az adattípusokat és az Azure Search adattípusok közötti leképezést
<table style="font-size:12">
<tr>
<td>JSON-adattípus</td>    
<td>Cél index mezőtípusok engedélyezett</td>
<td>Megjegyzések</td>
</tr>
<tr>
<td>logikai érték</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Egész szám</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Lebegőpontos szám</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Karakterlánc</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>tömbök primitív típusok, pl. ["a", "b", "c"]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Karakterláncok, dátumok hasonló</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON-pont objektumok</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON pontok JSON-objektumok a következő formátumban: {"type": "Point", "coordinates": [hosszú, lat]} </td>
</tr>
<tr>
<td>Más JSON-objektumok</td>
<td>N/A</td>
<td>Nem támogatott. Az Azure Search jelenleg csak a primitív típusok és a karakterlánc-gyűjtemények</td>
</tr>
</table>
