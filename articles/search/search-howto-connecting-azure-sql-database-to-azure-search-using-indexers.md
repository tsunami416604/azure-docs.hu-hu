---
title: Csatlakozás és tartalmának indexelőket – Azure Search használatával az Azure SQL Database indexelése
description: Ismerje meg, hogyan feltérképezi az adatok teljes szöveges kereséshez, az Azure Search indexelők használatával Azure SQL Database-ben. Ez a cikk ismerteti, az indexelő konfigurációjának, valamint az adatok betöltése céljából.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 66712b97807135b1e9e8321e441ac21368f86fc5
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633027"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-azure-search-indexers"></a>Csatlakozás és a tartalom az Azure Search-indexelők használatával Azure SQL Database indexelése

Mielőtt lekérdezheti, ha egy [Azure Search-index](search-what-is-an-index.md), meg kell az adatokkal való feltöltéséhez. Ha az adattárolás helyén, egy Azure SQL database-ben egy **az Azure SQL Database az Azure Search-indexelőt** (vagy **Azure SQL-indexer** röviden) az indexelési folyamat, amely azt jelenti, hogy kevesebb kódot írni, és kevésbé automatizálható infrastruktúrája számára.

Ez a cikk ismerteti a mechanics használatának [indexelők](search-indexer-overview.md), azonban csak akkor áll rendelkezésre az Azure SQL Database-adatbázisok (például integrált változáskövetés) funkciókat is ismerteti. 

Mellett az Azure SQL Database, az Azure Search biztosít az indexelők [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob storage](search-howto-indexing-azure-blob-storage.md), és [az Azure table storage](search-howto-indexing-azure-tables.md). Más adatforrások támogatást kérhet, a visszajelzést a [Azure Search-visszajelzési fórumon](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexelők és adatforrások

A **adatforrás** adja meg az index, adatelérési és a szabályzatok, amelyek hatékonyan azonosítják az adatok (az új, módosított vagy törölt sor) módosítása hitelesítő adatok. Van definiálva egy független erőforrásként, hogy több indexelők használható.

Egy **indexelő** egy erőforrás, amely csatlakozik egy adatforráshoz célzott keresési indexet. Az indexelő szolgál a következő módon:

* Végezze el az adatokat tölthet fel indexeket egyszeri példányát.
* Index frissítése a változások az adatforrás egy ütemezés szerint.
* Igény szerinti frissítéséhez az index, igény szerint futtatni.

Egyetlen indexelő csak felhasználhat egy táblát vagy nézetet, de több indexelők hozhat létre, ha azt szeretné, több search-index feltöltéséhez. Fogalmakról további információkért lásd: [Indexelőműveletek: Jellemző munkafolyamat](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Állítsa be, és konfigurálni egy Azure SQL indexelő használatával:

* Az adatok importálása varázsló az [Azure Portalon](https://portal.azure.com)
* Azure Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Az Azure Search [REST API-val](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

Ez a cikk használjuk a REST API létrehozásához **indexelők** és **adatforrások**.

## <a name="when-to-use-azure-sql-indexer"></a>Mikor érdemes használni az Azure SQL-indexelő
Függően számos tényező vonatkozó az adatok az Azure SQL-indexer használatát feltétlenül nem megfelelő. Az adatok a következő követelményeknek legjobban, ha az Azure SQL-indexelő is használhatja.

| Feltételek | Részletek |
|----------|---------|
| Adatok származnak, egy egyetlen táblából vagy nézetből | Ha az adatok több különböző táblázat többi részén Elszórva, létrehozhat az adatok egyetlen nézetben. Azonban ha egy nézetet használja, nem tudja használni az integrált SQL Server-címváltozásának felderítését a növekményes változásokat tartalmazó index frissítése. További információkért lásd: [rögzítése módosulnak, és a törölt sorokat](#CaptureChangedRows) alatt. |
| Az adattípusok kompatibilisek. | A legtöbb, de nem minden az SQL típusok támogatottak az Azure Search-index. Egy listát lásd: [adattípusok leképezési](#TypeMapping). |
| Valós idejű adatok szinkronizálását, nem szükséges | Az indexelő legfeljebb öt percenként is újraindexelni a táblában. Ha az adatok gyakran változnak, és a változások figyelembe kell venni az indexben vagy egyetlen perceken belül, javasoljuk a [REST API-val](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) vagy [.NET SDK-val](search-import-data-dotnet.md) közvetlenül elküldi a frissített sorokat. |
| Lehetőség a növekményes indexelése | Ha nagyméretű és -csomag az indexelő futtatása ütemezés szerint, az Azure Search lehet hatékonyan azonosítani az új, módosított vagy törölt sorokat kell lennie. Nem növekményes indexelő csak engedélyezett, ha indexelés igény szerinti (nem pedig ütemezés), vagy kevesebb mint 100 000 sor indexelése. További információkért lásd: [rögzítése módosulnak, és a törölt sorokat](#CaptureChangedRows) alatt. |

> [!NOTE] 
> Az Azure Search csak az SQL Server-hitelesítést támogatja. Támogatás az Azure Active Directory-jelszó-hitelesítés van szükség, ha meg szavazzon ez [UserVoice javaslat](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Az Azure SQL-indexelő létrehozása

1. Az adatforrás létrehozása:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Megtekintheti a kapcsolati karakterláncot, a [az Azure portal](https://portal.azure.com); használja a `ADO.NET connection string` lehetőséget.

2. A cél Azure Search-index létrehozása, ha még nincs ilyen. Egy index használatával is létrehozhat a [portál](https://portal.azure.com) vagy a [Index API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Győződjön meg arról, hogy a forrás tábla sémája kompatibilis a célindex sémája – lásd: [SQL és az Azure search közötti megfeleltetés a adattípusok](#TypeMapping).

3. Hozzon létre az indexelő nevét adná, és a forrás- és az adatindexet hivatkozik:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Az ilyen módon létrehozott indexelő ütemezés nem rendelkezik. Ha a létrehozást követően automatikusan futtatja. Futtatható újra bármikor egy **indexelő futtatása** kérelem:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2017-11-11
    api-key: admin-key

Testre szabhatja a több aspektusait indexelő viselkedését, például a Köteg mérete és a dokumentumok számát is kimarad, mielőtt egy indexelő végrehajtása meghiúsul. További információkért lásd: [indexelő API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Szükség lehet kapcsolódni az adatbázishoz az Azure-szolgáltatások engedélyezése. Lásd: [csatlakoztatása az Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ehhez útmutatást.

Az indexelő állapotának és végrehajtási előzmények (indexelt elemek, hibák, stb. száma) monitorozásához használja egy **az indexelő állapotának** kérelem:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2017-11-11
    api-key: admin-key

A válasz a következőhöz hasonlóan kell kinéznie:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Végrehajtási előzmények legfeljebb 50, a nemrégiben befejezett végrehajtások, amelyeket a rendszer rendezi a fordított időrendben (úgy, hogy a legújabb végrehajtása a válaszban hamarabb elérik) tartalmazza.
További információt a válaszban található [indexelő állapotának beolvasása](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Az indexelők futtatása történhet ütemezés szerint
Az indexelő futtatása rendszeres ütemezés szerint is rendezheti. Ehhez adja hozzá a **ütemezés** létrehozásakor vagy frissítésekor az indexelő tulajdonság. Az alábbi példában látható egy PUT kérelmet az indexelő frissítése:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

A **időköz** paraméter megadása kötelező. Az intervallum hivatkozik a két egymást követő indexelő végrehajtásának kezdete közötti idő. A legkisebb megengedett intervallum értéke 5 perc; a leghosszabb érték egy nap. Egy XSD "nyelv szerinti dayTimeDuration" értékként kell formázni (korlátozott részhalmaza olyan [ISO 8601 időtartama](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték). A minta: `P(nD)(T(nH)(nM))`. Példák: `PT15M` 15 percenként, a `PT2H` 2 óránként számára.

A választható **startTime** azt jelzi, ha az ütemezett végrehajtásának kell megkezdődnie. Ha ezt kihagyja, az aktuális UTC időpontig szolgál. Ez az idő, amelyben az első végrehajtás eset van ütemezve, mintha az indexelő futása során folyamatosan óta a startTime múltbeli – is lehet.  

Az indexelő csak egy végrehajtásának futtatható egyszerre. Az indexelő futása közben a végrehajtása van ütemezve, ha a végrehajtás van elhalaszthatók, amíg a következő ütemezett időpontban.

Vegyünk egy példát, ez részletesebb legyen. Tegyük fel, hogy a következő óránkénti ütemezés konfigurálva:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Ez történik:

1. Az első indexelő végrehajtás elindul, vagy 2015. március 1-12:00-kor UTC.
2. Tegyük fel, a végrehajtási vesz igénybe, 20 perc (vagy bármely idő kevesebb mint 1 óra).
3. A második végrehajtása, vagy 2015. március 1-1:00-kor kezdődik
4. Most tegyük fel, hogy a végrehajtási vesz igénybe több, mint egy óra – például 70 perc –, hogy ez befejeződik, körülbelül 2:10:00
5. Van 2:00-kor, ideje harmadik végrehajtásának indítása most. Azonban mivel a második végrehajtása 1 reggel továbbra is fut, a harmadik végrehajtását a rendszer kihagyta. A harmadik végrehajtási hajnali 3 órakor kezdődik

Hozzáadása, módosítása vagy törlése az ütemezés egy meglévő indexelő használatával egy **PUT indexelő** kérelmet.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Új, módosított és törölt sorok rögzítése

Használja az Azure Search **növekményes indexelő** újraindexelni a teljes tábla, illetve megtekintheti a minden alkalommal, amikor egy indexelő futása ne kelljen. Az Azure Search biztosít két támogatja a növekményes indexelési szabályzatok módosításához. 

### <a name="sql-integrated-change-tracking-policy"></a>Az SQL integrált változáskövetési házirend
Ha az SQL-adatbázis támogatja a [változáskövetés](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), javasoljuk a **SQL integrált módosítása változáskövetési házirend**. Ez a leghatékonyabb szabályzat. Emellett lehetővé teszi az Azure Search azonosíthatja a törölt sorokat anélkül egy explicit "helyreállítható törlés" oszlop hozzáadása a táblához.

#### <a name="requirements"></a>Követelmények 

+ Adatbázis-verzióra vonatkozó követelmények:
  * Az SQL Server 2012 SP3 vagy újabb, ha használ az SQL Server Azure virtuális gépeken.
  * Az Azure SQL Database V12-es, Azure SQL Database használata.
+ Táblák csak (egy nézet sem). 
+ Az adatbázis [engedélyezése a change tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) táblához. 
+ Nincs összetett elsődleges kulcs (egy elsődleges kulcs tartalmazó több mint egy oszlop) a táblán.  

#### <a name="usage"></a>Használat

Szeretné használni ezt a házirendet, hozzon létre vagy frissíti az adatforrást, ehhez hasonló:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Ha használja az integrált SQL változáskövetési házirend, ne adjon meg egy külön törlési szabályzat – Ez a házirend azonosítására szolgáló beépített támogatással rendelkezik az törölt sorok. Azonban a törlések észlelt "automagically" kell, a keresési index a dokumentum kulcsaként kell ugyanaz, mint az elsődleges kulcsot az SQL-táblát. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Magas Vízjelbe beleszámított módosítása szabályzat

Ez a változásészlelési házirend "magas vízjelbe beleszámított" oszlop rögzítése az, illetve amikor egy sor utolsó frissítés időpontja támaszkodik. Egy nézetet használja, ha egy magas vízjelbe beleszámított házirendet kell használnia. A magas vízjelbe beleszámított oszlop az alábbi követelményeknek kell megfelelnie.

#### <a name="requirements"></a>Követelmények 

* Minden Beszúrások adja meg az oszlop értékét.
* Minden frissítés egy elemhez is módosíthatja az oszlop értékét.
* Ez az oszlop értékét minden egyes insert nebo update egyenes arányban növekszik.
* A következő lekérdezéseket, és ORDER BY záradékok hatékonyan hajthatók végre: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Kifejezetten javasoljuk a [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) a magas vízjelbe beleszámított oszlop adattípusát. Ha bármilyen más típusú adatokat használ, a change tracking nem garantált, hogy a tranzakció végrehajtása egy indexelő lekérdezés már létesítve lett zajok mellett minden változások rögzítésére. Használata esetén **rowversion** írásvédett replikával rendelkező konfiguráció esetén az elsődleges másodpéldány, az indexelő kell mutatnia. Csak egy elsődleges replika szinkronizálása forgatókönyvek is használható.

#### <a name="usage"></a>Használat

Magas vízjelbe beleszámított szabályzat használatához hozzon létre vagy frissíti az adatforrást, ehhez hasonló:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> A forrástábla nem tartalmaz index magas vízjelbe beleszámított oszlopában, ha az SQL-indexelő által használt lekérdezésekkel lehet, hogy időtúllépés. Ilyen például a `ORDER BY [High Water Mark Column]` záradék szükséges egy index, hatékony futtatását, amikor a tábla tartalmazza a sorok számát.
>
>

Ha időtúllépési hibákat észlel, akkor használhatja a `queryTimeout` indexelő konfigurációs beállítás, ha a lekérdezés időkorlátja nagyobb, mint az alapértelmezett 5 perces időtúllépési értéket szeretné. Például az időtúllépés értéke 10 perc, hozzon létre vagy frissítse az indexelő a következő beállításokkal:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Letilthatja a `ORDER BY [High Water Mark Column]` záradékban. Azonban ez nem ajánlott, mert az indexelő végrehajtási hibája miatt megszakad, ha az indexelő újra feldolgozni az összes sor Ha később - fut, akkor is, ha az indexelő feldolgozása már szinte az összes sor megszakítva idő szerint van-e. Letiltása a `ORDER BY` záradék, használja a `disableOrderByHighWaterMarkColumn` beállítását az indexelő definíciója:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Helyreállítható törlés oszlop törlési szabályzat
Sorok törlése a forrás táblából, esetén érdemes a search-index, valamint azokat a sorokat törölni. Az integrált SQL változáskövetési házirend használja, ha ezt elvégzi, az Ön számára. Azonban a magas vízjelbe beleszámított változáskövetési házirend nem segít, a törölt sorokat. Mi a teendő ilyenkor?

A sorok fizikailag törlődik a táblából, ha az Azure Search rendelkezik célszámítógéppel a rekordokat, amelyek már nem létezik jelenléte nem lehet.  A "helyreállítható törlés" módszer használatával azonban logikailag anélkül, hogy eltávolítja a hatóköröket a táblázatban a sorok törlése. Oszlop hozzáadása a tábla vagy nézet és be van jelölve sort, mert törölték az oszlop használatakor.

A helyreállítható törlés módszer használata esetén adja meg a helyreállítható törlési házirendet a következő létrehozásakor vagy frissítésekor az adatforrás:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

A **softDeleteMarkerValue** kell karakterláncot – a tényleges érték karakteres formáját használja. Például, ha egészszám-oszloppal, ahol az érték 1 törölt sorok lesznek megjelölve, használja `"1"`. Ha hol lesznek megjelölve törölt sorokat az IGAZ logikai értéket BIT oszlopot, használja a karakterlánc-literál `True` vagy `true`, így nem számít.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Az SQL és az Azure Search-adattípusok közötti leképezések
| SQL-adattípus | Engedélyezett célindex mezőtípusok | Megjegyzések |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| valódi, float |Edm.Double, Edm.String | |
| pénz, pénzt tizedes tört szám |Edm.String |Az Azure Search nem támogatja a decimális típusú Edm.Double átalakítás, mert ez elveszítik a pontosság |
| karakter, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Egy SQL-karakterlánc használható Collection(Edm.String) mező feltöltéséhez, ha a karakterlánc jelenti. a karakterláncok egy JSON-tömb: `["red", "white", "blue"]` |
| smalldatetime, dátum és idő, datetime2, dátum, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Földrajzi hely |Edm.GeographyPoint |Támogatott SRID 4326 (Ez az alapértelmezett beállítás) pontra típusa csak földrajzi példánya |
| ROWVERSION |– |Oszlopok sor-verzió nem lehet tárolni, a keresési indexben, de a change Tracking szolgáltatáshoz használható |
| idő, időtartam, binary, varbinary, kép, xml, geometriai, CLR-beli típusok |– |Nem támogatott |

## <a name="configuration-settings"></a>Konfigurációs beállítások
SQL-indexelő több konfigurációs beállítást tesz elérhetővé:

| Beállítás | Adattípus | Cél | Alapértelmezett érték |
| --- | --- | --- | --- |
| queryTimeout |sztring |Beállítja az SQL-lekérdezés-végrehajtás időkorlátja |5 perc ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |Logikai |Hatására az SQL-lekérdezést használja a magas vízjelbe beleszámított házirend hagyja az ORDER BY záradékban. Lásd: [magas Vízjelbe beleszámított házirend](#HighWaterMarkPolicy) |false |

Ezek a beállítások szerepelnek a `parameters.configuration` az indexelő definíciója az objektumot. Például állítsa be a lekérdezés időkorlátja 10 perc, hozzon létre vagy frissítse az indexelő a következő beállításokkal:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>GYIK

**KÉRDÉS: Használhatom az Azure SQL-indexelő az Azure IaaS virtuális gépeken futó SQL-adatbázisok?**

Igen. Azonban meg kell, hogy a keresési szolgáltatás kapcsolódni az adatbázishoz. További információkért lásd: [konfigurálása egy kapcsolatot az Azure Search indexelők és az SQL Server-beli virtuális gépen](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**KÉRDÉS: Használható a helyszínen futó SQL-adatbázisok Azure SQL-indexer?**

Közvetlenül nem. Nem javasolt és nem támogatják a közvetlen kapcsolat, ezzel lenne megkövetelik, hogy nyissa meg az adatbázisok, az internetes forgalmat. Ügyfeleink a jelen forgatókönyvben híd technológiák használatával például az Azure Data Factory sikeres volt. További információkért lásd: [adatok leküldése az Azure Search-index, az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**KÉRDÉS: Használhatom az Azure SQL-indexelő iaas Azure-on futó SQL Serveren kívül más adatbázisok?**

Nem. Ebben a forgatókönyvben nem támogatjuk, mert még nem teszteltük az indexelő az SQL Serveren kívül más adatbázisokhoz.  

**KÉRDÉS: Ütemezés szerint futó több indexelő is létrehozható?**

Igen. Azonban csak egy indexelő is fut egy csomóponton egyszerre. Ha párhuzamosan fut több indexelők van szüksége, fontolja meg, a keresési szolgáltatás több keresési egység vertikális felskálázása.

**KÉRDÉS: Az indexelők futtatása hatással a számítási feladatok?**

Igen. Indexelő az egyik csomópontot a search szolgáltatás fut, és a csomópont erőforrásokat indexelést és lekérdezést forgalom és az egyéb API-kérések kiszolgáló között megosztott. Ha nagy számításigényű indexelés és a lekérdezési számítási feladatok futtatásához, és 503-as hibák vagy egyre nagyobb válaszidők nagy mértékű észlel, fontolja meg [a keresési szolgáltatás vertikális felskálázásával](search-capacity-planning.md).

**KÉRDÉS: Használható a másodlagos replika egy [feladatátvevő fürt](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) adatforrásként?**

Ez a konkrét licenctől függ. Teljes indexelése, egy tábla vagy nézet, használhatja egy másodlagos replikára. 

Növekményes indexelést, az Azure Search két változás szabályzatok használatát támogatja: Az SQL integrált változáskövetés és a magas vízjel.

A csak olvasható replikák a SQL database nem támogatja az integrált változáskövetés. Ezért magas Vízjelbe beleszámított házirendet kell használnia. 

A standard szintű ajánljuk, hogy a rowversion adattípust használ a magas vízjelbe beleszámított oszlophoz. Azonban az SQL Database használatával rowversion támaszkodik `MIN_ACTIVE_ROWVERSION` függvény, amely nem támogatott a csak olvasható replika. Ezért kell mutatnia az indexelő, egy elsődleges replika rowversion használatakor.

Ha egy csak olvasható replika rowversion használja, a következő hiba jelenik meg: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**KÉRDÉS: Használhatom-e egy másik, nem rowversion oszlop magas vízjelbe beleszámított változáskövetési?**

Nem ajánlott. Csak **rowversion** lehetővé teszi, hogy megbízható az adatszinkronizálásra használható. Azonban az alkalmazáslogika függően előfordulhat, hogy lehet biztonságos ha:

+ Biztosíthatja, hogy az indexelő futása, amikor nem léteznek szálankénti függőben lévő tranzakciók, amelyek indexelése a táblán (például az összes tábla frissítések történhet meg egy kötegelt ütemezés szerint, és kerülje az átfedésben lévő, a tábla frissítéssel az Azure Search-indexelő ütemezés beállítása Schedule).  

+ Ezt megteheti egy teljes reindex kihagyott sorokat csomópontmetrikák rendszeres időközönként. 
