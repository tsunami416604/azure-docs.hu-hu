---
title: Az Azure SQL adatbázis csatlakoztatása az Azure Search használatával az indexelők |} Microsoft Docs
description: Megtudhatja, hogyan olvasnak be adatokat az Azure SQL Database az Azure Search-index használatával indexelők.
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: eugenesh
ms.openlocfilehash: 02b4e8cb4963a5c12b528630e8e7906d6c5307fe
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Csatlakozás Azure SQL adatbázis Azure Search használatával az indexelők

Mielőtt kérdezheti le egy [Azure Search-index](search-what-is-an-index.md), fel kell tölteni azt az adatait. Ha él, az adatok Azure SQL-adatbázis, egy **az Azure SQL Database az Azure Search-indexelőt** (vagy **Azure SQL indexelő** röviden) automatizálhatja az indexelési folyamata, ami azt jelenti, hogy kevesebb kód írása, és kevesebb infrastruktúrát a fontos adatokhoz.

Ez a cikk ismerteti a beállítás használata esetén [indexelők](search-indexer-overview.md), de csak Azure SQL-adatbázisok (például az integrált változáskövetés) elérhető szolgáltatások is ismerteti. 

Azure SQL-adatbázisok, továbbá Azure Search nyújt az indexelők [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob Storage tárolóban](search-howto-indexing-azure-blob-storage.md), és [Azure table storage](search-howto-indexing-azure-tables.md). Kérjen támogatást más adatforrások, a visszajelzést adhat a [Azure Search-visszajelzési fórumon](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexelők és adatforrások

A **adatforrás** mely adatok index, adatelérési és a házirendeket, amelyek hatékonyan azonosítják az adatoknak (új, módosított vagy törölt sorai) hitelesítő adatok megadásához. Az definiálva van egy független erőforrásként, hogy több indexelők használható.

Egy **indexelő** , amely csatlakozik egy adatforráshoz a célzott keresési indexszel rendelkező erőforrás. Az indexelő a következő módon használható:

* Hajtsa végre az adatokat tölthet fel indexet egyszeri másolatát.
* Az adatforrás ütemezés szerint módosításokkal index frissítése.
* Igény szerinti index frissítése, igény szerint futtassa.

Egyetlen indexelő csak felhasználhat egy táblát vagy nézetet, de több indexelők hozhat létre, ha azt szeretné, hogy több keresési index feltöltése. A fogalmakat további információkért lásd: [az indexelési műveletek: tipikus munkafolyamat](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Állítsa be, és konfigurálhatja az Azure SQL indexelő segítségével:

* Az adatok importálása varázslót a [Azure-portálon](https://portal.azure.com)
* Azure Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Az Azure Search [REST API-n](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

Ebben a cikkben használjuk a REST API létrehozása **indexelők** és **adatforrások**.

## <a name="when-to-use-azure-sql-indexer"></a>Mikor érdemes használni az Azure SQL indexelő
Attól függően, hogy az adatok vonatkozó számos tényező az Azure SQL indexelő használatát feltétlenül nem megfelelő. Az adatok a következő követelményeinek megfelelő, ha az Azure SQL indexelő is használhatja.

| Feltételek | Részletek |
|----------|---------|
| Egyetlen tábla vagy nézet származik adatok | Ha az adatok több tábla van tárolja, az adatok egyetlen nézetben is létrehozhat. Azonban ha egy nézetben sem lesz integrált SQL Server-címváltozásának felderítését segítségével frissítse egy index ugyanezzel a növekményes változásokat. További információkért lásd: [rögzítése módosított és törölt sorok](#CaptureChangedRows) alatt. |
| Adattípusok kompatibilisek. | Nem minden a SQL típusok támogatottak az Azure Search-index. Az útmutató, [adattípusok leképezési](#TypeMapping). |
| Nincs szükség a valós idejű adatok szinkronizálása | Az indexelő indexelheti újra a tábla legfeljebb 5 perc. Ha az adatok gyakran változnak, és a változások figyelembe kell venni az indexben vagy egyetlen perceken belül, azt javasoljuk, a [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) vagy [.NET SDK](search-import-data-dotnet.md) frissített sorok leküldéses közvetlenül. |
| Növekményes indexelő lehetőség | Ha egy nagy adatkészlet és a terv az indexelő ütemezés szerint futtatni, Azure Search lehet hatékonyan tudja azonosítani az új, módosított vagy törölt sor kell lennie. Nem növekményes indexelő csak engedélyezett, ha az igény szerinti (nem a ütemezés) indexelő, vagy kevesebb, mint 100 000 sor indexelése. További információkért lásd: [rögzítése módosított és törölt sorok](#CaptureChangedRows) alatt. |

> [!NOTE] 
> Az Azure Search csak az SQL Server-hitelesítést támogatja. Ha a támogatási Azure Active Directory jelszavas hitelesítés szükséges, adjon szavazzon ez [UserVoice javaslat](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Hozzon létre egy Azure SQL indexelőt

1. Az adatforrás létrehozása:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Kaphat a kapcsolati karakterláncot, a [Azure-portálon](https://portal.azure.com); használja a `ADO.NET connection string` lehetőséget.

2. A cél Azure Search-index létrehozása, ha Ön nem rendelkezik ilyennel. Az index használatával hozhat létre a [portal](https://portal.azure.com) vagy a [Index API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Győződjön meg arról, hogy a cél index sémája kompatibilis a forrástábla a séma – Lásd [SQL és az Azure search közötti megfeleltetés adattípusok](#TypeMapping).

3. Hozzon létre az indexelő adjon neki egy nevet, és az adatok forrása és célja index hivatkozik:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Ily módon létrehozta az indexelő nem tartozhat olyan ütemezés. Ha már létrehozott automatikusan futtatja. Futtatható újra az összes idő a **indexelő futtatása** kérelem:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

Testre szabhatja a indexelő viselkedését, például a kötegméretet és hány dokumentumokat is kimarad, mielőtt egy indexelő végrehajtása meghiúsul számos tulajdonságát. További információkért lásd: [indexelő API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Szükség lehet az adatbázishoz való kapcsolódáshoz Azure-szolgáltatások engedélyezése. Lásd: [csatlakozás az Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ehhez útmutatást.

Az indexelő állapotát és végrehajtási előzményei (indexelt elemek, hiba stb. száma) figyelése egy **indexelő állapot** kérelem:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

A válasz a következőhöz hasonlóan kell kinéznie:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
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

Futtatási előzményei a legutóbb befejeződött, a rendszer rendezi a fordított időrendben (úgy, hogy a legújabb végrehajtási származik a válaszban szereplő első) végrehajtások legfeljebb 50 tartalmazza.
További információt a válasz megtalálhatók [indexelő állapotának beolvasása](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Indexelő fussanak ütemezés szerint
Az indexelő rendszeres ütemezés szerint futtatni is rendezheti. Ehhez adja hozzá a **ütemezés** létrehozásakor vagy frissítésekor az indexelő tulajdonság. Az alábbi példában látható a PUT kérés az indexelő frissítése:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

A **időköz** paraméter megadása kötelező. Az időköz két egymást követő indexelő végrehajtások kezdete közötti idő hivatkozik. A legkisebb megengedett intervallum értéke 5 perc; a leghosszabb egy nap. Az XSD "daytimeduration típusú" értéknek kell formázni (korlátozott részhalmaza egy [ISO 8601 időtartama](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) érték). A minta: `P(nD)(T(nH)(nM))`. Példák: `PT15M` a 15 percenként `PT2H` minden 2 órán át.

A választható **startTime** azt jelzi, ha az ütemezett végrehajtások kell megkezdéséhez. Ha ki van hagyva, a jelenlegi UTC időt használja. Most a múltban –, amelyben eset az első végrehajtása van ütemezve, mintha az indexelő futása során a kezdő időpont óta folyamatosan lehet.  

Az indexelő csak egy végrehajtásának futtatható egyszerre. Az indexelő futása közben a végrehajtása van ütemezve, ha a végrehajtás kerül sor, amíg a következő ütemezett időpontban.

Mérlegeljük konkrétabb legyen ez a példa. Tegyük fel, hogy azt a következő óránkénti ütemezés konfigurálva:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Ez történik:

1. Az első indexelő végrehajtása kezdődik, vagy az akörüli 2015. március 1 12:00-kor UTC.
2. Tegyük fel, a végrehajtási időt vesz igénybe, 20 perc (vagy bármikor kisebb, mint 1 óra).
3. A második végrehajtása kezdődik, vagy az akörüli 2015. március 1 1:00-kor
4. Most tegyük fel, hogy a végrehajtása több mint egy óra – például 70 perc – időt vesz igénybe, így ő maga befejeződne körülbelül 2:10 óra
5. Most 2:00 óra, a harmadik végrehajtásra elindításához idő van. Azonban mivel a második végrehajtási 1 órától továbbra is fut, a harmadik végrehajtását a rendszer kihagyja. A harmadik indítása 3 órakor

Hozzáadása, módosítása, vagy törölje a meglévő indexelőt ütemezésének használatával egy **PUT indexelő** kérelmet.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Új, módosított és törölt sorok rögzítése

Használja az Azure Search **növekményes indexelő** kívánja kerülni indexelik újra a teljes táblázathoz, vagy minden alkalommal, amikor az indexelő futása megtekintéséhez. Az Azure Search biztosít két észlelési szabályzatok támogatja a növekményes indexelő módosítása. 

### <a name="sql-integrated-change-tracking-policy"></a>Az SQL integrált változáskövetési házirend
Ha az SQL-adatbázis támogatja [a változáskövetés](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), azt javasoljuk, **SQL integrált módosítása követési házirend**. Ez egy, a leghatékonyabb házirend. Emellett lehetővé teszi a Azure Search törölt soraihoz azonosításához anélkül, hogy egy explicit "helyreállítható törlésre" oszlop hozzáadása a táblához.

#### <a name="requirements"></a>Követelmények 

+ Adatbázis-verzióra vonatkozó követelmények:
  * SQL Server 2012 SP3 és újabb verziók, használata az SQL Server Azure virtuális gépeken.
  * Az Azure SQL Database 12-es, az Azure SQL Database használata.
+ Táblák csak (Nézet). 
+ Az adatbázis [engedélyezése változáskövetési](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) a táblához. 
+ Nem összetett elsődleges kulcs (egy elsődleges kulcs tartalmazó több mint egy oszlopban) a táblában.  

#### <a name="usage"></a>Használat

Ezzel a házirend, hozzon létre, vagy frissíti az adatforrást ehhez hasonló:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Ha használja az SQL integrált változáskövetés házirend, ne adjon meg egy külön adatok törlési szabályzat – a ezzel a házirend-azonosító beépített támogatása törölt sorok. Azonban a törlés észlelt "automagically" kell, a dokumentum kulcsot, a search-index a kell ugyanaz, mint az SQL-tábla elsődleges kulcsa. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Magas vízjel alapján észlelésének házirend

A módosítás szabályzat rögzítése a verzió vagy idő, amikor egy sor utolsó frissítés "magas vízjelének" oszlop támaszkodik. Egy nézet használata, a magas vízjel alapján házirendet kell használnia. A magas vízjel alapján oszlop az alábbi követelményeknek kell megfelelnie.

#### <a name="requirements"></a>Követelmények 

* Minden Beszúrások adja meg az oszlop értékét.
* Egy elem összes frissítését is módosíthatja az oszlop értéke.
* Ez az oszlop értékének növeli az egyes insert vagy update mutatóval.
* A következő lekérdezések ahol és ORDER BY záradékok hatékonyan hajtható végre: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Határozottan javasoljuk a [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) a magas vízjel alapján oszlop adattípusa esetében. Ha bármilyen más típusú adatokat használ, változások követése nem biztos, hogy rögzítése tranzakciók egyidejűleg az indexelő lekérdezések végrehajtása mellett minden változást. Használata esetén **rowversion** írásvédett replikával konfiguráció esetén az elsődleges másodpéldány, az indexelő kell mutatnia. Csak egy elsődleges másodpéldány adatok szinkronizálása forgatókönyvek esetén használható.

#### <a name="usage"></a>Használat

A magas vízjel alapján házirend használatához hozzon létre, vagy frissíti az adatforrást ehhez hasonló:

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
> Ha a forrástábla nem rendelkezik az index a magas vízjel alapján oszlopra, használja az SQL indexelő lekérdezések előfordulhat, hogy túllépi az időkorlátot. Különösen a `ORDER BY [High Water Mark Column]` záradékhoz szükség van az index hatékonyan és méretezhetően fussanak, ha a táblázat több sort tartalmaz.
>
>

Ha időtúllépési hibákat tapasztal, használhatja a `queryTimeout` indexelő konfigurációs beállítás értékre való beállítására a lekérdezés időkorlátja egy magasabb, mint az alapértelmezett 5 perces időkorlát. Például 10 percre időkorlát beállításához hozzon létre, vagy az indexelő frissítése a következő beállításokkal:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Le is tilthatja a `ORDER BY [High Water Mark Column]` záradékban. Azonban ez nem ajánlott, mert az indexelő végrehajtása hiba miatt megszakadt, ha rendelkezik-e az indexelő újra feldolgozni összes sorát, ha később - fog futni akkor is, ha az indexelő feldolgozása már majdnem összes sort az időkorlát miatt megszakítva. Letiltja a `ORDER BY` záradék, használja a `disableOrderByHighWaterMarkColumn` beállítása az indexelő meghatározása:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Véglegesen törli oszlop törlési szabályzat
Ha a sorok törlődnek a forrástábla, érdemes azokat a sorokat törölni, valamint a search-index. Ha az SQL integrált változáskövetés házirend használja, ez van végrehajtott kezeli az Ön. Azonban a magas vízjel alapján változáskövetési házirend nem oldották meg a törölt soraihoz. Mi a teendő ilyenkor?

A sorok fizikailag el lesznek távolítva a táblázatban, ha az Azure Search rendelkezik semmilyen módon nem következtethető ki, jelenléte azt jelzi, hogy a továbbiakban nem létezik.  A "soft-törlés" módszer használatával azonban logikailag sorok törlése nélkül távolít el őket a táblából. Oszlop hozzáadása a tábla vagy nézet, és be van jelölve sorok törlése az adott oszlop használatával.

A soft-törlés módszer használata esetén adja meg a helyreállítható törlési házirendet az alábbiak szerint létrehozásakor vagy frissítésekor az adatforráshoz:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

A **softDeleteMarkerValue** kell egy karakterlánc – használja a tényleges érték karakterláncos ábrázolása. Például, ha még szerepel egészszám-oszloppal, ahol törölt soraihoz megjelölve az 1 értékkel, használja `"1"`. Ha rendelkezik ahol különbözteti meg a logikai true értéket törölt soraihoz BIT oszlophoz, `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Az SQL és az Azure Search adattípusok közötti megfeleltetés.
| SQL-adattípus | Cél index mezőtípusok engedélyezett | Megjegyzések |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| valódi, lebegőpontos |Edm.Double, Edm.String | |
| kis pénz típusú értékké, pénzt decimális numerikus |Edm.String |Az Azure Search nem támogatja a decimális típusok konvertálásakor Edm.Double, mert ez elveszítik pontosság |
| CHAR, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Egy SQL-karakterlánc Collection(Edm.String) mező feltöltéséhez, ha a karakterlánc egy JSON-tömb karakterláncok használhatók: `["red", "white", "blue"]` |
| smalldatetime, dátum és idő, datetime2, dátum, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| földrajzi hely |Edm.GeographyPoint |Csak az srid-Azonosítónak 4326 (Ez az alapértelmezett) pontra típusú geográfiai példányban támogatottak. |
| ROWVERSION |– |Sor verziójú oszlopok nem lehet tárolni a search-index, de a változások követése is használhatók |
| idő, timespan, binary, varbinary, kép, xml, geometriai, CLR-típus |– |Nem támogatott |

## <a name="configuration-settings"></a>Konfigurációs beállítások
SQL indexelő számos konfigurációs beállítás közzétesz:

| Beállítás | Adattípus | Cél | Alapértelmezett érték |
| --- | --- | --- | --- |
| queryTimeout |karakterlánc |Beállítja az SQL-lekérdezés végrehajtása időtúllépés |5 perc ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |logikai érték |Az SQL-lekérdezés ORDER BY záradékban hagyja el a magas vízjel alapján házirend segítségével okoz. Lásd: [magas vízjel alapján házirend](#HighWaterMarkPolicy) |false |

Ezek a beállítások szerepelnek a `parameters.configuration` objektum indexelő definíciójában. Például szeretné beállítani a lekérdezés időkorlátja 10 percig, hozzon létre, vagy az indexelő frissítése a következő beállításokkal:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>GYIK

**K: használhatok Azure SQL indexelő Azure IaaS virtuális gépeken futó SQL-adatbázisok?**

Igen. Azonban szüksége ahhoz, hogy a Keresés az adatbázishoz való kapcsolódáshoz. További információkért lásd: [egy kapcsolatot egy Azure Search-indexelőt, és az SQL Server konfigurálása az Azure virtuális gép](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**K: használhatok Azure SQL indexelő helyileg futó SQL-adatbázisok?**

Közvetlenül nem. Nem javasoljuk vagy támogatják a közvetlen kapcsolat létrehozásakor, ezzel volna megkövetelik, hogy nyissa meg az internetes forgalmat az adatbázisokat. Az ügyfelek a jelen forgatókönyvben híd technológiák használatával például az Azure Data Factory sikeres volt. További információkért lásd: [adatokat küldeni az Azure Data Factory használatához Azure Search-index](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**K: használhatok Azure SQL indexelő nem Azure IaaS futó SQL Server adatbázisok?**

Nem. Ebben az esetben nem támogatjuk, mert a nem SQL Server adatbázisok indexelő nem ellenőriztük.  

**K: hozható létre több indexelők ütemezés szerint fut?**

Igen. Azonban csak egy indexelő futtathat egy csomóponton egy időben. Ha egyidejűleg több indexelők van szüksége, fontolja meg a keresési szolgáltatás több keresési egység vertikális felskálázásával.

**K: Indexelő hatással a lekérdezés munkaterhelések futtatásával?**

Igen. Az indexelő egy, a keresőszolgáltatása csomópontja fut, és adott csomóponton erőforrások indexelést és a lekérdezés forgalom és az egyéb API-kérelmek kiszolgáló között vannak megosztva. Ha intenzív indexelő és a lekérdezés alkalmazásokat és szolgáltatásokat futtathatnak, és 503-as hibáinak vagy növekszik a válaszhoz szükséges idő nagy mértékű tapasztal, érdemes lehet [a keresőszolgáltatása vertikális felskálázásával](search-capacity-planning.md).

**K: Használhatom az egy másodlagos másodpéldány egy [feladatátvevő fürt](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) adatforrásként?**

Ez a konkrét licenctől függ. A teljes indexelő egy tábla vagy nézet, egy másodlagos másodpéldány is használhatja. 

Növekményes indexelés, két Azure Search támogatja észlelési szabályzatok módosítása: az SQL integrált módosítása nyomkövetési és magas vízjel alapján.

A csak olvasható replika SQL-adatbázis nem támogatja az integrált változáskövetés. Ezért a magas vízjel alapján házirendet kell használnia. 

A szabványos javasoljuk, hogy a magas vízjel alapján oszlop rowversion adattípus használatához. Azonban az SQL-adatbázis használatával rowversion támaszkodik `MIN_ACTIVE_ROWVERSION` függvénynek, amely csak olvasható replika nem támogatott. Ezért kell mutatnia az indexelő elsődleges replika rowversion használata.

Ha egy csak olvasható replika rowversion használatát kísérli meg, a következő hiba jelenik meg: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**K: használhatok egy alternatív, nem rowversion oszlop magas vízjel alapján változáskövetési?**

Nem ajánlott. Csak **rowversion** lehetővé teszi, hogy a megbízható adatok szinkronizálásához. Azonban, attól függően, hogy az alkalmazáslogikát, hogy nem ha:

+ Biztosíthatja, hogy az indexelő futása, amikor nincsenek függőben lévő tranzakciók a tábla (például frissítések ütemezése hiba akkor fordulhat elő egy ütemezést, és az Azure Search-indexelőt ütemezés kötegelt értéke átfedésben vannak a táblázat frissítés elkerülése érdekében minden tábla) indexelt.  

+ Rendszeres időközönként tegye a teljes ismételt indexelése átvételéhez a hiányzó sorokat. 
