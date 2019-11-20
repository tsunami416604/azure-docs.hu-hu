---
title: Lekérdezési problémák diagnosztizálása és hibaelhárítása Azure Cosmos DB használatakor
description: Ismerje meg, hogyan azonosíthatja, diagnosztizálhatja és elháríthatja a Azure Cosmos DB SQL-lekérdezéssel kapcsolatos problémákat.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: fd8e80c7cd7cb71e4e0418d970cf2f328f1a3d79
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184711"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Azure Cosmos DB lekérdezési teljesítményének megoldása
Ez a cikk a Azure Cosmos DB SQL-lekérdezési problémák azonosítását, diagnosztizálását és hibaelhárítását ismerteti. Az Azure Cosmos DB lekérdezések optimális teljesítményének elérése érdekében kövesse az alábbi hibaelhárítási lépéseket. 

## <a name="collocate-clients-in-same-azure-region"></a>Rézvezetékes végezhet-ügyfelek ugyanabban az Azure-régióban 
A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure Regions](https://azure.microsoft.com/global-infrastructure/regions/#services) című cikkben találja.

## <a name="check-consistency-level"></a>Konzisztencia-ellenőrzés
A [konzisztencia szintje](consistency-levels.md) hatással lehet a teljesítményre és a költségekre. Győződjön meg arról, hogy a konzisztencia szintje megfelel az adott forgatókönyvnek. További részleteket a [konzisztencia szintjének kiválasztása](consistency-levels-choosing.md)című témakörben talál.

## <a name="log-the-executed-sql-query"></a>A végrehajtott SQL-lekérdezés naplózása 

Naplózhatja a végrehajtott SQL-lekérdezést egy Storage-fiókban vagy a diagnosztikai napló táblájában. A [diagnosztikai naplókon keresztüli SQL-lekérdezési naplók](monitor-cosmos-db.md#diagnostic-settings) lehetővé teszik, hogy az Ön által választott Storage-fiókba naplózza a megzavarodott lekérdezést. Így megtekintheti a naplókat, és megkeresheti a magasabb RUs-t használó lekérdezést. Később a tevékenység-AZONOSÍTÓval megegyezően használhatja a QueryRuntimeStatistics aktuális lekérdezését. A lekérdezés a biztonsági célra, a lekérdezési paraméterek neveivel és a WHERE záradékokban szereplő értékekkel nem egyezik meg a tényleges névvel és értékekkel. A naplózás a Storage-fiók használatával megtarthatja a végrehajtott lekérdezések hosszú távú megőrzését.  

## <a name="log-query-metrics"></a>Napló lekérdezési metrikái

A lassú vagy költséges lekérdezések hibakereséséhez használja a `QueryMetrics`. 

  * Állítsa be, hogy a válaszban `QueryMetrics` legyen a `FeedOptions.PopulateQueryMetrics = true`.
  * `QueryMetrics` az osztály túlterhelt `.ToString()` függvényt tartalmaz, amely meghívja a `QueryMetrics`karakterlánc-ábrázolását. 
  * A metrikák a következő, többek között az alábbi adatok kinyerésére használhatók: 
  
      * Azt határozza meg, hogy a lekérdezési folyamat adott összetevője rendellenesen hosszú ideig tartott-e (több száz ezredmásodperc vagy több). 

          * Tekintse meg `TotalExecutionTime`.
          * Ha a lekérdezés `TotalExecutionTime` a végpontok és a végpontok közötti végrehajtási idő alatt marad, akkor az idő az ügyfél oldalán vagy a hálózaton lesz elköltve. Ellenőrizze, hogy az ügyfél és az Azure-régió közös elhelyezésű-e.
      
      * Az elemzett dokumentumokban hamis pozitívak voltak-e (ha a kimeneti dokumentumok száma jóval kevesebb, mint a lekért dokumentumok száma).  

          * Tekintse meg `Index Utilization`.
          * `Index Utilization` = (visszaadott dokumentumok száma/betöltött dokumentumok száma)
          * Ha a visszaadott dokumentumok száma jóval kisebb a betöltött számnál, akkor a rendszer a hamis pozitív értékeket elemzi.
          * Korlátozza a lekérdezett dokumentumok számát a szűkebb szűrők használatával.  

      * Az egyes ciklikus utak díjszabása (lásd a `Partition Execution Timeline` a `QueryMetrics`karakterlánc-ábrázolásával). 
      * Azt jelzi, hogy a lekérdezés magas szintű kérelmeket használ-e. 

További részletekért lásd az [SQL-lekérdezések végrehajtási metrikáinak beszerzéséről](profile-sql-api-query.md) szóló cikket.
      
## <a name="tune-query-feed-options-parameters"></a>Lekérdezési csatorna beállítási paramétereinek hangolása 
A lekérdezési teljesítményt a kérés [Csatornabeállítások](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) paramétereivel tudja hangolni. Próbálja beállítani az alábbi paramétereket:

  * Először állítsa be az `MaxDegreeOfParallelism` az-1 értékre, majd hasonlítsa össze a teljesítményt a különböző értékek között. 
  * Először állítsa be az `MaxBufferedItemCount` az-1 értékre, majd hasonlítsa össze a teljesítményt a különböző értékek között. 
  * A `MaxItemCount` beállítása a-1 értékre.

Amikor a különböző értékek szerinti teljesítményt hasonlítja össze, olyan értékekkel próbálkozzon, mint a 2, 4, 8, 16 stb.
 
## <a name="read-all-results-from-continuations"></a>Olvassa le az összes eredményt a folytatásokból
Ha úgy gondolja, hogy nem minden eredményt kap meg, gondoskodjon róla, hogy a folytatást teljes mértékben kiürítse. Más szóval olvassa tovább az eredményeket, amíg a folytatási kód további dokumentumokat eredményez.

A teljes kiürítést a következő minták követésével lehet elérni:

  * Folytassa a feldolgozás eredményeit, miközben a folytatás nem üres.
  * A feldolgozás folytatása, amíg a lekérdezés több eredményt is tartalmaz. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Válasszon ki olyan rendszerfunkciókat, amelyek indexet használnak
Ha a kifejezés lefordítható különböző sztringértékekre, akkor használhat indexet. Ellenkező esetben nem. 

Íme az indexet használó sztringfüggvények listája: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    Íme néhány példa a lekérdezésekre: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Kerülje a System functions funkciót a szűrőben (vagy a WHERE záradékban), amelyet nem az index szolgáltat. Néhány példa az ilyen rendszerfunkciókra: tartalmazza a következőket: felső, alsó.
  * Ha lehetséges, a lekérdezések írása partíciókulcs-szűrő használatával történjen.
  * Az elvégezhető lekérdezések elérésének elkerülése érdekében a szűrőben ne legyenek meghívók a felső/alacsonyabb érték. Ehelyett a beszúráskor normalizálja az értékek házát. Minden értéknél illessze be az értéket a kívánt burkolattal, vagy szúrja be az eredeti értéket és az értéket a kívánt burkolattal. 

    Például:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Ebben az esetben a "Joe" kifejezést a "Joe" kifejezéssel vagy az eredeti "Joe" értékkel együtt tároljuk. 
    
    Ha a JSON-adattároló normalizálva van, a lekérdezés a következőket válik:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    A második lekérdezés nagyobb teljesítményt nyújt, mivel az értékek összehasonlításához nem szükséges az egyes értékek átalakításának elvégzése a "JOE" értékkel.

A rendszerfunkciók további részleteiért lásd: [rendszerfunkciók](sql-query-system-functions.md) című cikk.

## <a name="check-indexing-policy"></a>Indexelési szabályzat keresése
Ellenőrizze, hogy az aktuális [indexelési szabályzat](index-policy.md) optimálisan van-e beállítva:

  * Győződjön meg arról, hogy a lekérdezésekben használt összes JSON-útvonal szerepel az indexelési szabályzatban a gyorsabb olvasás érdekében.
  * A lekérdezésekben nem használt útvonalak kizárása a több elvégezhető íráshoz.

További részletekért lásd az [indexelési szabályzat kezelése](how-to-manage-indexing-policy.md) című cikket.

## <a name="spatial-data-check-ordering-of-points"></a>Térbeli információk: a pontok sorrendjét vizsgálja
Sokszög belül pontok óramutató járásával ellentétes irányban sorrendben kell adni. Egy megadott óramutató sorrendben sokszög benne a régió inverzét jelöli.

## <a name="optimize-join-expressions"></a>ILLESZTÉSi kifejezések optimalizálása
`JOIN` kifejezések nagy mennyiségű termékre bővíthetők. Ha lehetséges, szűkítse a lekérdezést egy kisebb keresési területtel egy szűkebb szűrő használatával.

A többértékű allekérdezések optimalizálhatja `JOIN` kifejezéseket úgy, hogy az összes Select-many kifejezés után lenyomja a predikátumokat, és nem a `WHERE` záradékban lévő összes kereszthivatkozást. A részletes példákat lásd: [illesztési kifejezések optimalizálása](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) cikk.

## <a name="optimize-order-by-expressions"></a>ORDER BY kifejezések optimalizálása 
`ORDER BY` lekérdezési teljesítmény akkor csökkenhet, ha a mezők ritkák vagy nem szerepelnek az index házirendben.

  * A ritka mezők (például az idő) esetében a szűréshez a lehető legnagyobb mértékben csökkentse a keresési területet. 
  * Az egytulajdonságú `ORDER BY`esetében adja meg a tulajdonságot a tárgymutató-házirendben. 
  * Több tulajdonság `ORDER BY` kifejezés esetében Definiáljon egy [összetett indexet](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) a rendezett mezőkhöz.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Számos nagy méretű dokumentum betöltése és feldolgozása
A lekérdezésekhez szükséges idő és RUs nem csak a válasz méretétől függ, hanem attól is függ, hogy milyen munkát végez a lekérdezés feldolgozási folyamata. Az idő és az RUs arányosan növekszik a teljes lekérdezés-feldolgozási folyamat által elvégzett munka mennyiségével. A nagyméretű dokumentumok nagyobb mennyiségű munkát végeznek, így a nagyméretű dokumentumok betöltéséhez és feldolgozásához több idő és RUs szükséges.

## <a name="low-provisioned-throughput"></a>Alacsony kiosztott átviteli sebesség
Gondoskodjon arról, hogy a kiépített átviteli sebesség képes legyen a munkaterhelés kezelésére. Növelje az érintett gyűjtemények RU-költségvetését.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Próbálja meg frissíteni a legújabb SDK-verziót
A legújabb SDK-val kapcsolatban lásd az [SDK letöltési és kibocsátási megjegyzéseit](sql-api-sdk-dotnet.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések
Az alábbi dokumentumokban megtekintheti, hogyan mérjük le a lekérdezéseket, és hogyan végezhet végrehajtási statisztikát a lekérdezések finomhangolásához, és így tovább:

* [SQL-lekérdezés végrehajtási metrikáinak lekérése a .NET SDK használatával](profile-sql-api-query.md)
* [A lekérdezési teljesítmény finomhangolása az Azure Cosmos DB-vel](sql-api-sql-query-metrics.md)
* [Teljesítménnyel kapcsolatos tippek .NET SDK-hoz](performance-tips.md)
