---
title: Problémák diagnosztizálása és hibaelhárítása lekérdezés Azure Cosmos DB használata esetén
description: Ismerje meg, hogyan azonosíthatja, diagnosztizálása és hibaelhárítása az Azure Cosmos DB SQL-lekérdezés problémák.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835831"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Az Azure Cosmos DB lekérdezési teljesítmény hibaelhárítása
Ez a cikk bemutatja, hogyan azonosíthatja, diagnosztizálása és hibaelhárítása az Azure Cosmos DB SQL-lekérdezés problémák. Azure Cosmos DB-lekérdezéseket az optimális teljesítmény elérése érdekében kövesse az alábbi hibaelhárítási lépéseket. 

## <a name="collocate-clients-in-same-azure-region"></a>Ugyanazon Azure-régióban lévő ügyfelek elhelyezésének engedélyezése 
A lehető legkisebb késleltetést úgy, hogy a hívó alkalmazás megtalálható az üzembe helyezett Azure Cosmos DB-végpontként az azonos Azure-régióban érhető el. Az elérhető régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/#services) cikk.

## <a name="check-consistency-level"></a>Ellenőrizze a konzisztenciaszint
[Konzisztenciaszint](consistency-levels.md) befolyásolhatja a teljesítményt és a költségek. Ellenőrizze, hogy a konzisztencia szintjét az adott esethez megfelelő. További információ: [Konzisztenciaszint kiválasztása](consistency-levels-choosing.md).

## <a name="log-query-metrics"></a>Napló lekérdezés metrikák
Használat `QueryMetrics` lassú vagy drága lekérdezések hibaelhárítása. 

  * Állítsa be `FeedOptions.PopulateQueryMetrics = true` rendelkeznie `QueryMetrics` a válaszban.
  * `QueryMetrics` osztály rendelkezik egy túlterhelt `.ToString()` -függvény, amely karakteres formáját is lekérhető `QueryMetrics`. 
  * A metrikák, amellyel hozzájut a következő, többek között: 
  
      * A lekérdezési folyamat bármely adott összetevőjét e rendellenes sokáig végrehajtásához (ezredmásodpercben, vagy több száz sorrendben). 

          * Tekintse meg `TotalExecutionTime`.
          * Ha a `TotalExecutionTime` a lekérdezés nem éri el a teljes körű végrehajtási idő, akkor az ügyfél oldalán vagy a hálózati van folyamatban töltött idő. Ellenőrizze, hogy az ügyfél és az Azure-régióban vannak közös elhelyezésű.
      
      * E vakriasztások voltak-e a dokumentumok elemzése (Ha kimeneti dokumentumok száma sokkal kisebb, mint a lekért dokumentumok száma).  

          * Tekintse meg `Index Utilization`.
          * `Index Utilization` = (Visszaadott dokumentumok száma / betöltött száma dokumentumok)
          * Ha a visszaadott dokumentumok száma sokkal kisebb, mint a betöltött, majd vakriasztások elemzése van folyamatban.
          * Szűkebb szűrőket lekérése folyamatban dokumentumok számának korlátozásához.  

      * Egyes üzenetváltások fared (lásd a `Partition Execution Timeline` a karakteres formáját `QueryMetrics`). 
      * A lekérdezés e nagy kérelem díj használja fel. 

További információ: [beszerzése az SQL-lekérdezés végrehajtási metrikák](profile-sql-api-query.md) cikk.
      
## <a name="tune-query-feed-options-parameters"></a>Hírcsatorna beállításai lekérdezésparamétereket hangolása 
A lekérdezési teljesítmény hangolásával a kérelem használatával [hírcsatorna beállításai](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) paramétereket. Próbálja meg a beállítás az alábbi beállításokat:

  * Állítsa be `MaxDegreeOfParallelism` -1 első, majd hasonlítsa össze teljesítmény különböző értékek között. 
  * Állítsa be `MaxBufferedItemCount` -1 első, majd hasonlítsa össze teljesítmény különböző értékek között. 
  * Állítsa be `MaxItemCount` 1 értéket ad.

Összehasonlítja a különböző értékek teljesítményét, próbálja meg értékek, mint 2, 4, 8, 16 és mások.
 
## <a name="read-all-results-from-continuations"></a>Minden eredmény olvasni continuations
Ha úgy véli, hogy nem jelennek meg az összes eredmény, ügyeljen arra, hogy teljes mértékben kiürítési fenntartása. Más szóval folyamatosan eredmények olvasása, míg a folytatási token több dokumentum eddig is számtalan előnyét.

Teljes mértékben a kiürítés érhető el a következő minták egyikét:

  * Továbbra is feldolgozási eredményeket, amíg a folytatási nem üres.
  * Továbbra is, míg a lekérdezési eredmények feldolgozása. 

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

## <a name="choose-system-functions-that-utilize-index"></a>Válassza ki az indexet használó rendszer funkciók
Ha a kifejezés karakterlánc-értékek számos lefordítható, majd azt képes használni az index; Ellenkező esetben nem érhető el. 

A következő karakterlánc-függvények, amelyek használhatják az indexet listája: 
    
  * STARTSWITH (str_expr, str_expr) 
  * LEFT (str_expr, num_expr) = str_expr 
  * KARAKTERLÁNCRÉSZLET (str_expr, num_expr, num_expr) = str_expr, de csak ha az első num_expr 0 
    
    Az alábbiakban néhány lekérdezést példát: 
    
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

  * Elkerülése érdekében a rendszer függvények a szűrő (vagy a WHERE záradékban), amely nem az index által kiszolgált. Az ilyen rendszer funkciók például tartalmazza, a felső, alsó.
  * Ha lehetséges, a lekérdezések írása partíciókulcs-szűrő használatával történjen.
  * Nagy teljesítményű eléréséhez lekérdezések kerülje a felső és alsó hívja meg a szűrő. Ehelyett normalizálása kis-és nagybetűhasználatának értékeket a szolgáltatás. Az egyes értékek beszúrása a megfelelő kis-és értékét, vagy is az eredeti értékre, és az értéket a megfelelő kis-és beszúrása. 

    Példa:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    Ebben az esetben a "JÁNOS" kezdőbetűnek vagy az adattárban tárolni a "János" az eredeti érték és a "JÁNOS". 
    
    Ha a JSON-adatok kis-és van normalizálva a lekérdezés válik:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    A második lekérdezés lesz további nagy teljesítményű, mivel nem igényel átalakításokat végez az egyes értékek annak érdekében, hogy a "JÁNOS", az értékek összehasonlítása.

Részletek: több rendszer függvény [rendszerfunkciók](sql-query-system-functions.md) cikk.

## <a name="check-indexing-policy"></a>Indexelési házirend ellenőrzése
Ellenőrizze, hogy az aktuális [indexelési szabályzata](index-policy.md) optimális:

  * Győződjön meg arról, a lekérdezésekben használt JSON elérési utak szerepelnek az indexelési házirendet az gyorsabb olvasásokhoz.
  * További nagy teljesítményű írások a lekérdezésekben nem használt elérési utak kizárása.

További információ: [hogyan való kezelése indexelési szabályzata](how-to-manage-indexing-policy.md) cikk.

## <a name="spatial-data-check-ordering-of-points"></a>Térbeli adatok: Ellenőrizze a pontok rendezése
Sokszög belül pontok óramutató járásával ellentétes irányban sorrendben kell adni. Egy megadott óramutató sorrendben sokszög benne a régió inverzét jelöli.

## <a name="optimize-join-expressions"></a>Csatlakozás kifejezések optimalizálása
`JOIN` kifejezések nagy közötti termékekbe bővítheti. Ha lehetséges, a lekérdezéshez kisebb keresés lemezterület-keresztül egy sokkal szűkebb szűrőt.

Többértékű segédlekérdezések optimalizálhatja `JOIN` kifejezések predikátumok küldésével, minden egyes kiválasztása – több kifejezés után, nem pedig minden cross-illesztéseket után a `WHERE` záradékban. Részletes példa: [csatlakozzon kifejezések optimalizálása](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) cikk.

## <a name="optimize-order-by-expressions"></a>ORDER BY kifejezésnek optimalizálása 
`ORDER BY` a lekérdezési teljesítmény romolhat, ha a mezők ritka, vagy nem része az index szabályzat.

  * A ritka mezők, például idő csökkentse a lehető legnagyobb mértékben keresési terület szűrőket. 
  * Egyetlen tulajdonság `ORDER BY`, a tulajdonság tartalmazza a index házirendben. 
  * Több tulajdonság `ORDER BY` kifejezések, adja meg egy [összetett index](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) rendezi mező alapján.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Számos nagyméretű dokumentumok betöltése és feldolgozása
Az idő és a egy lekérdezés által igényelt RUs nem csak a válaszok méretétől függ, azokat is függnek a munkát, a lekérdezés-feldolgozási folyamat végzi el. Idő és a fenntartott egységek nőhet arányosan a teljes lekérdezés-feldolgozási folyamat által végzett munka mennyisége. További munkát végez a nagy dokumentumok, így több időt és kérelemegység szükséges betölteni és feldolgozni a nagy dokumentumok.

## <a name="low-provisioned-throughput"></a>Kevés a létesített átviteli sebesség
Győződjön meg arról, kiosztott átviteli sebesség számítási feladatot képes kezelni. Növelje az érintett gyűjtemény RU költségvetés.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Próbálja ki az SDK legújabb verziójára
Meghatározni a legújabb SDK lásd [SDK letöltése és a kibocsátási megjegyzések](sql-api-sdk-dotnet.md) cikk.

## <a name="next-steps"></a>További lépések
Tekintse meg az lekérdezésenként fenntartott egységek mérése, a végrehajtási statisztika a lekérdezéseket és más finomhangolása való alábbi dokumentumokat:

* [Első SQL-lekérdezés végrehajtási metrikák .NET SDK használatával](profile-sql-api-query.md)
* [A lekérdezési teljesítmény finomhangolása az Azure Cosmos DB-vel](sql-api-sql-query-metrics.md)
* [A .NET SDK a teljesítménnyel kapcsolatos tippek](performance-tips.md)