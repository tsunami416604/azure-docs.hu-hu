---
title: Keresés az Azure SQL-adatokon keresztül
titleSuffix: Azure Cognitive Search
description: Adatok importálása az Azure SQL Database-ből indexelők használatával, teljes szöveges keresés az Azure Cognitive Search. Ez a cikk a kapcsolatok, az indexelő konfigurációját és az adatok betöltését ismerteti.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113310"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Csatlakozás Azure SQL Database-tartalomhoz azure Cognitive Search indexelővel

Az [Azure Cognitive Search index](search-what-is-an-index.md)lekérdezése előtt fel kell nagyítania az adatokat. Ha az adatok egy Azure SQL-adatbázisban élnek, az **Azure Cognitive Search indexelő az Azure SQL Database** (vagy az Azure SQL **indexelő)** automatizálhatja az indexelési folyamatot, ami kevesebb kódot jelent az íráshoz, és kevesebb infrastruktúrát kell gondozni.

Ez a cikk ismerteti az [indexelők](search-indexer-overview.md)használatának mechanikáját, de azt is ismerteti, hogy a csak Az Azure SQL-adatbázisokban érhető el funkciók (például integrált változáskövetés). 

Az Azure SQL-adatbázisok mellett az Azure Cognitive Search indexelőket is biztosít az [Azure Cosmos DB,](search-howto-index-cosmosdb.md) [az Azure Blob storage](search-howto-indexing-azure-blob-storage.md)és az Azure table storage [számára.](search-howto-indexing-azure-tables.md) Ha más adatforrások támogatását szeretné kérni, adja meg visszajelzését az [Azure Cognitive Search visszajelzési fórumán.](https://feedback.azure.com/forums/263029-azure-search/)

## <a name="indexers-and-data-sources"></a>Indexelők és adatforrások

Az **adatforrás** határozza meg, hogy mely adatokat kell indexelni, az adatelérés hitelesítő adatait és azokat a házirendeket, amelyek hatékonyan azonosítják az adatok változásait (új, módosított vagy törölt sorok). Ez egy független erőforrás, így több indexelők által használható.

Az **indexelő** olyan erőforrás, amely egyetlen adatforrást kapcsol össze egy célzott keresési indexszel. Az indexelő t a következő módokon használjuk:

* Az index feltöltéséhez végezze el az adatok egyszeri másolatát.
* Index frissítése az adatforrás változásaival ütemezés szerint.
* Igény szerint futtassa az index szükség szerinti frissítéséhez.

Egyetlen indexelő csak egy táblát vagy nézetet használhat fel, de több indexelőt is létrehozhat, ha több keresési indexet szeretne feltöltődni. A fogalmakról további információt az [Indexelő műveletek: Tipikus munkafolyamat](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow)című témakörben talál.

Az Azure SQL indexelőt a következő használatával állíthatja be és konfigurálhatja:

* Adatok importálása varázsló az [Azure Portalon](https://portal.azure.com)
* Azure Cognitive Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

Ebben a cikkben a REST API-t **használjuk indexelők** és **adatforrások**létrehozásához.

## <a name="when-to-use-azure-sql-indexer"></a>Mikor kell használni az Azure SQL Indexert?
Az adatokkal kapcsolatos számos tényezőtől függően előfordulhat, hogy az Azure SQL indexelő használata megfelelő vagy nem megfelelő. Ha az adatok megfelelnek az alábbi követelményeknek, használhatja az Azure SQL indexelő.

| Feltételek | Részletek |
|----------|---------|
| Az adatok egyetlen táblából vagy nézetből származnak | Ha az adatok több táblában vannak szétszórva, az adatokegyetlen nézetet hozhat létre. Ha azonban nézetet használ, az SQL Server integrált változásészlelésével nem frissítheti az indexet növekményes módosításokkal. További információt a [Módosított és törölt sorok rögzítése](#CaptureChangedRows) című témakörben talál. |
| Az adattípusok kompatibilisek | A legtöbb, de nem az összes SQL-típusok az Azure Cognitive Search index támogatja. A listát az [Adattípusok leképezése (Térképezés) (Adattípusok leképezése) (Adattípusok leképezése](#TypeMapping) |
| Nem szükséges valós idejű adatszinkronizálást végezni | Az indexelő legfeljebb öt percenként indexelheti a táblát. Ha az adatok gyakran változnak, és a módosításokat másodperceken vagy perceken belül meg kell jelenni az indexben, javasoljuk, hogy a [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) vagy a [.NET SDK](search-import-data-dotnet.md) használatával közvetlenül leküldéses frissített sorokat. |
| Növekményes indexelés lehetséges | Ha egy nagy adatkészletet, és azt tervezi, hogy az indexelő ütemezés szerint futtassa, az Azure Cognitive Search képesnek kell lennie az új, módosított vagy törölt sorok hatékony azonosításához. A nem növekményes indexelés csak akkor engedélyezett, ha igény szerint indexel (nem ütemezett), vagy 100 000-nél kevesebb sort indexel. További információt a [Módosított és törölt sorok rögzítése](#CaptureChangedRows) című témakörben talál. |

> [!NOTE] 
> Az Azure Cognitive Search csak az SQL Server-hitelesítést támogatja. Ha az Azure Active Directory jelszó-hitelesítéséhez támogatásra van szüksége, kérjük, szavazzon erre a [UserVoice javaslatra.](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)

## <a name="create-an-azure-sql-indexer"></a>Azure SQL Indexelő létrehozása

1. Az adatforrás létrehozása:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   A kapcsolati karakterláncot az [Azure Portalon](https://portal.azure.com)szerezheti be; használja `ADO.NET connection string` a lehetőséget.

2. Hozza létre a cél Azure Cognitive Search index, ha még nem rendelkezik. Indexet a [portál](https://portal.azure.com) vagy az [Index létrehozása API](https://docs.microsoft.com/rest/api/searchservice/Create-Index)használatával hozhat létre. Győződjön meg arról, hogy a célindex sémája kompatibilis a forrástábla sémájával – lásd az SQL és az [Azure Cognitive keresési adattípusok közötti leképezést.](#TypeMapping)

3. Hozza létre az indexelőt egy név megadásával, és hivatkozva az adatforrásra és a célindexre:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Az ily módon létrehozott indexelőnem rendelkezik ütemezéssel. Automatikusan egyszer fut, amikor létrejön. A **futtatási indexelő-kérelem** használatával bármikor újra futtathatja:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Az indexelő viselkedésének számos aspektusa testreszabható, például a kötegméret és a dokumentumok kihagyása az indexelő végrehajtása sikertelensvégrehajtása előtt. További információ: [Indexer API létrehozása.](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)

Előfordulhat, hogy engedélyeznie kell, hogy az Azure-szolgáltatások csatlakozzanak az adatbázishoz. Ennek módjáról az [Azure-ból való csatlakozás](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ról további információt talál.

Az indexelő állapotának és végrehajtási előzményeinek (indexelt elemek száma, hibák stb.) figyeléséhez használjon **indexelő** állapotkérelmet:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

A válasznak a következőhöz hasonlóan kell kinéznie:

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

A végrehajtási előzmények legfeljebb 50 legutóbb befejezett végrehajtást tartalmaznak, amelyek fordított időrendi sorrendben vannak rendezve (így a legutóbbi végrehajtás az első a válaszban).
A válaszról további információ az [Indexelő állapotának beírása című](https://go.microsoft.com/fwlink/p/?LinkId=528198) részben található.

## <a name="run-indexers-on-a-schedule"></a>Indexelők futtatása ütemezés szerint
Az indexelőt úgy is elrendezheti, hogy rendszeres időközönként, ütemezés szerint fusson. Ehhez adja hozzá az **ütemezési** tulajdonságot az indexelő létrehozásakor vagy frissítésekor. Az alábbi példa egy PUT-kérést mutat be az indexelő frissítésére:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Az **intervallum** paraméter megadása kötelező. Az intervallum két egymást követő indexelő végrehajtás kezdete közötti időre utal. A legkisebb megengedett időköz 5 perc; a leghosszabb egy nap. XSD "dayTimeDuration" értékként kell formázni (az [ISO 8601 időtartam](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték korlátozott részhalmaza). A minta ez: `P(nD)(T(nH)(nM))`. Példák: `PT15M` 15 percenként, `PT2H` 2 óránként.

Az indexelő-ütemezések meghatározásáról az [Azure Cognitive Search indexelőinek ütemezése](search-howto-schedule-indexers.md)című témakörben olvashat bővebben.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Új, módosított és törölt sorok rögzítése

Az Azure Cognitive Search **növekményes indexelést** használ, hogy ne kelljen újraindexelni a teljes táblát vagy nézetet minden alkalommal, amikor egy indexelő fut. Az Azure Cognitive Search két változásészlelési szabályzatot biztosít a növekményes indexelés támogatásához. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL integrált változáskövetési házirend
Ha az SQL-adatbázis támogatja a [változások követését,](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)javasoljuk az **SQL integrált változáskövetési házirend**használatát. Ez a leghatékonyabb politika. Emellett lehetővé teszi, hogy az Azure Cognitive Search azonosítsa a törölt sorokat anélkül, hogy explicit "helyreállítható törlés" oszlopot kellene hozzáadnia a táblához.

#### <a name="requirements"></a>Követelmények 

+ Adatbázis verziókövetelményei:
  * SQL Server 2012 SP3 és újabb, ha az SQL Server azure-beli virtuális gépeken.
  * Azure SQL Database V12, ha az Azure SQL Database használatával.
+ Csak táblázatok (nincs nézet). 
+ Az adatbázisban engedélyezze a [tábla változáskövetését.](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) 
+ Nincs összetett elsődleges kulcs (egynél több oszlopot tartalmazó elsődleges kulcs) a táblában.  

#### <a name="usage"></a>Használat

A házirend használatához hozza létre vagy frissítse az adatforrást a következőkkel:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

SQL integrált változáskövetési házirend használatakor ne adjon meg külön adattörlés-észlelési házirendet – ez a házirend beépített támogatást nyújt a törölt sorok azonosításához. Ahhoz azonban, hogy a törléseket "automatikusan" észlelje, a keresési indexben lévő dokumentumkulcsnak meg kell egyeznie az SQL-tábla elsődleges kulcsának. 

> [!NOTE]  
> Ha [a TRUNCATE TABLE segítségével](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) nagy számú sort távolít el egy SQL-táblából, az indexelőt [alaphelyzetbe](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) kell állítani a változáskövetési állapot visszaállításához a sorok törlésének felvételéhez.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Magas vízjel változásészlelési irányelvei

Ez a változásészlelési házirend egy "magas vízjel" oszlopon alapul, amely rögzíti a sor utolsó frissítésének verzióját vagy idejét. Ha nézetet használ, magas vízjel-politikát kell használnia. A magas vízjeloszlopnak meg kell felelnie az alábbi követelményeknek.

#### <a name="requirements"></a>Követelmények 

* Minden beszúrás értéket ad meg az oszlophoz.
* Az elem minden frissítése az oszlop értékét is módosítja.
* Az oszlop értéke minden beszúrással vagy frissítéssel növekszik.
* A következő WHERE és ORDER BY záradékokkal rendelkező lekérdezések hatékonyan hajthatók végre:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Javasoljuk, hogy a magas vízjel oszlop [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) adattípusát használja. Ha bármilyen más adattípust használ, a változáskövetés nem garantált, hogy rögzítse az összes változást az indexelő lekérdezéssel egyidejűleg végrehajtó tranzakciók jelenlétében. Rowversion **rowversion** használatakor a konfiguráció csak olvasható replikák, meg kell mutatni az indexelő az elsődleges replika. Adatszinkronizálási forgatókönyvekhez csak elsődleges kópia használható.

#### <a name="usage"></a>Használat

A magas vízjel-házirend használatához hozza létre vagy frissítse az adatforrást a következőkkel:

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
> Ha a forrástábla nem rendelkezik indexszel a magas vízjel oszlopban, az SQL indexelő által használt lekérdezések időtúllépésre lehetnek. A záradék `ORDER BY [High Water Mark Column]` megköveteli különösen az index hatékony futtatását, ha a tábla sok sort tartalmaz.
>
>

Ha időtúllépési hibákat észlel, az `queryTimeout` indexelő konfigurációs beállításával beállíthatja a lekérdezés időtúlértékét az alapértelmezett 5 perces időtúllépésnél magasabb értékre. Ha például az időhosszabbítást 10 percre szeretné beállítani, hozza létre vagy frissítse az indexelőt a következő konfigurációval:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

A `ORDER BY [High Water Mark Column]` záradékot is letilthatja. Ez azonban nem ajánlott, mert ha az indexelő végrehajtását egy hiba szakítja meg, az indexelőnek újra fel kell dolgoznia az összes sort, ha később fut - még akkor is, ha az indexelő már szinte az összes sort feldolgozta, mire megszakadt. A `ORDER BY` záradék letiltásához `disableOrderByHighWaterMarkColumn` használja az indexelő definíciójának beállítását:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Oszloptörlés-észlelési házirend ideiglenes törlése
Ha a sorokat törli a forrástáblából, valószínűleg ezeket a sorokat is törölni szeretné a keresési indexből. Ha az SQL integrált változáskövetési házirendet használja, ezt elintézi az Ön számára. A magas vízjel változáskövetési házirendje azonban nem segít a törölt sorokban. Mi a teendő?

Ha a sorok fizikailag eltávolítják a táblából, az Azure Cognitive Search nem tud következtetni a már nem létező rekordok jelenlétét.  A "soft-delete" technikával azonban logikusan törölheti a sorokat anélkül, hogy eltávolítaná őket a táblából. Adjon hozzá egy oszlopot a táblázathoz, vagy tekintse meg és jelölje meg a sorokat töröltként az adott oszlop használatával.

A soft-delete módszer használatakor a következőképpen adhatja meg a helyreállítható törlési házirendet az adatforrás létrehozásakor vagy frissítésekor:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

A **softDeleteMarkerValue értéknek** karakterláncnak kell lennie – a tényleges érték karakterlánc-ábrázolását kell használnia. Ha például van egy egész oszlop, ahol a törölt sorok `"1"`at 1 értékkel jelölik meg, használja a parancsot. Ha van olyan BIT oszlopa, ahol a törölt sorok logikai `True` `true`igaz értékkel vannak megjelölve, használja a karakterlánc-konstanst, vagy a kis- és nagybetűnem számít.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Sql és Azure Cognitive Search adattípusok leképezése
| SQL-adattípus | Engedélyezett célindexmezőtípusok | Megjegyzések |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| valódi, lebeg |Edm.Double, Edm.String | |
| smallmoney, pénz tizedes numerikus |Edm.String |Az Azure Cognitive Search nem támogatja a decimális típusok Edm.Double-re történő konvertálását, mert ez elveszítené a pontosságot |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Egy SQL-karakterlánc használható a Collection(Edm.String) mező feltöltésére, ha a karakterlánc karakterláncok JSON-tömbjét jelöli:`["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Földrajz |Edm.GeographyPoint |Csak a POINT típusú, SRID 4326 típusú (alapértelmezett) földrajzi példányok támogatottak |
| rowversion (sorverzió) |N/A |A sorverziós oszlopok nem tárolhatók a keresési indexben, de felhasználhatók a változások nyomon követésére. |
| idő, timespan, bináris, varbinary, kép, xml, geometria, CLR típusok |N/A |Nem támogatott |

## <a name="configuration-settings"></a>Konfigurációs beállítások
Az SQL indexelő több konfigurációs beállítást tesz elérhetővé:

| Beállítás | Adattípus | Cél | Alapértelmezett érték |
| --- | --- | --- | --- |
| queryTimeout |sztring |Az SQL-lekérdezés végrehajtásának időhatárolójának beállítja |5 perc ("00:05:00") |
| disableOrderByHighWaterMarkOszlop |Bool |Hatására a magas vízjel-házirend által használt SQL-lekérdezés kihagyja az ORDER BY záradékot. Lásd: [Magas vízjel-irányelv](#HighWaterMarkPolicy) |hamis |

Ezek a beállítások `parameters.configuration` az objektumban használatosak az indexelő definíciójában. Ha például a lekérdezés időhamását 10 percre szeretné beállítani, hozza létre vagy frissítse az indexelőt a következő konfigurációval:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>GYIK

**K: Használhatom az Azure SQL indexelőt az Azure-beli IaaS virtuális gépeken futó SQL-adatbázisokkal?**

Igen. Azonban engedélyeznie kell, hogy a keresési szolgáltatás csatlakozzon az adatbázishoz. További információ: [Kapcsolat konfigurálása egy Azure Cognitive Search indexelő és az SQL Server között egy Azure virtuális gépen.](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

**K: Használhatom az Azure SQL indexelőt a helyszíni futó SQL-adatbázisokhoz?**

Nem közvetlenül. Nem javasoljuk vagy támogatja a közvetlen kapcsolatot, mivel ehhez meg kell nyitnia az adatbázisokat az internetes forgalom számára. Az ügyfelek sikerrel ezt a forgatókönyvet híd technológiák, például az Azure Data Factory használatával. További információ: [Adatok leküldése egy Azure Cognitive Search index az Azure Data Factory használatával.](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector)

**K: Használhatom az Azure SQL indexelőt az Azure-beli IaaS-ben futó SQL Server-en kívüli adatbázisokkal?**

Nem. Nem támogatjuk ezt a forgatókönyvet, mert az indexelőt nem teszteltük az SQL Serveren kívül más adatbázisokkal.  

**K: Létrehozhatok több indexelőt ütemezés szerint?**

Igen. Azonban csak egy indexelő futtatható egy csomóponton egyszerre. Ha egyszerre több indexelőre van szüksége, fontolja meg a keresési szolgáltatás több keresési egységre való felskálázását.

**K: Az indexelő futtatása hatással van a lekérdezési számítási feladatra?**

Igen. Indexelő fut az egyik csomópont a keresési szolgáltatásban, és hogy a csomópont erőforrásai meg vannak osztva az indexelésés és a lekérdezési forgalom kiszolgálása és más API-kérelmek. Ha intenzív indexelési és lekérdezési számítási feladatokat futtat, és magas 503-as hibaarányt vagy növekvő válaszidőt tapasztal, fontolja meg [a keresési szolgáltatás bővítését.](search-capacity-planning.md)

**K: Használhatok másodlagos replika egy [feladatátvevő fürtben](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) adatforrásként?**

Attól függ. Egy tábla vagy nézet teljes indexelése, használhat egy másodlagos replika. 

A növekményes indexeléshez az Azure Cognitive Search két változásészlelési szabályzatot támogat: az SQL integrált változáskövetést és a magas vízjeljelet.

Írásvédett kópiák esetén az SQL-adatbázis nem támogatja az integrált változáskövetést. Ezért a Magas vízjel házirendet kell használnia. 

A standard javaslat az, hogy a rowversion adattípus a magas vízjel oszlop. A rowversion használata azonban az SQL `MIN_ACTIVE_ROWVERSION` Database függvényére támaszkodik, amelyet írásvédett replikák nem támogatnak. Ezért rowversion használata esetén az indexelőt egy elsődleges replikára kell mutatnia.

Ha írásvédett replika sorverzióhasználatát próbálja használni, a következő hibaüzenet jelenik meg: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**K: Használhatok alternatív, nem rowversion oszlopot a magas vízjel-változáskövetéshez?**

Nem ajánlott. Csak **rowversion** lehetővé teszi a megbízható adatszinkronizálás. Az alkalmazás logikájától függően azonban biztonságos lehet, ha:

+ Biztosíthatja, hogy az indexelő futtatásakor nincsenek indexelt nyitott tranzakciók a táblában (például az összes táblafrissítés kötegként történik ütemezés szerint, és az Azure Cognitive Search indexelő ütemezése úgy van beállítva, hogy elkerülje a táblával való átfedést ütemezés frissítése).  

+ Rendszeresidőközönként teljes újraindexet végez a kihagyott sorok felvételéhez. 