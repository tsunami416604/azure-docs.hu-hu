---
title: Nagyméretű adathalmaz indexelése beépített indexelő használatával – Azure Search
description: Megtudhatja a nagyméretű adatindexeléssel vagy a számítási igényű adatindexeléssel kapcsolatos stratégiákat kötegelt módban, újraelosztással és az ütemezett, párhuzamos és elosztott indexelési módszerekkel.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: heidist
ms.openlocfilehash: 44a8136c4e02d4eceb5b11231bbbfed010159e75
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172882"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Nagyméretű adatkészletek indexelése Azure Search

Mivel az adatmennyiség növekedése vagy feldolgozása szükségessé vált, előfordulhat, hogy az egyszerű vagy az alapértelmezett indexelési stratégiák már nem hatékonyak. Azure Search esetében több módszer áll rendelkezésre a nagyobb adatkészletek elhelyezésére, az adatfeltöltési kérelem szerkezetének módjától kezdve az ütemezett és elosztott számítási feladatokhoz forrás-specifikus indexelő használatával.

Ugyanezek a technikák a nagy adatmennyiségek esetében is érvényesek a hosszan futó folyamatokra. A [párhuzamos indexelésben](#parallel-indexing) leírt lépések különösen hasznosak a számítási igényű indexeléshez, mint például a képelemzés vagy a természetes nyelvi feldolgozás a [kognitív keresési folyamatokban](cognitive-search-concept-intro.md).

A következő részekben a nagy mennyiségű adatindexeléshez három módszer található.

## <a name="option-1-pass-multiple-documents"></a>1\. módszer: Több dokumentum továbbítása

A nagyobb adatkészletek indexelésének egyik legegyszerűbb mechanizmusa több dokumentum vagy rekord küldése egyetlen kérelembe. Ha a teljes adattartalom 16 MB alatti, a kérések legfeljebb 1000 dokumentumot kezelhetnek tömeges feltöltési műveletekben. Ezek a korlátozások érvényesek, függetlenül attól, hogy a .NET SDK-ban [REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) vagy [IndexBatch](https://docs.microsoft.com/otnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) használ. Mindkét API esetében a 1000-es dokumentumokat az egyes kérések törzsébe csomagoljuk.

A Batch-indexelés a REST vagy a .NET használatával, vagy az indexelő segítségével valósítható meg az egyes kérelmek esetében. Néhány indexelő több különböző korlátozás alatt működik. Pontosabban, az Azure Blob indexelése a köteg méretét 10 dokumentumra állítja be a nagyobb méretű dokumentumok méretének elismeréseként. Az indexelő [REST API létrehozásán](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer )alapuló indexek esetében beállíthatja az `BatchSize` argumentumot úgy, hogy testreszabja ezt a beállítást, hogy jobban megfeleljen az adatok jellemzőinek. 

> [!NOTE]
> A dokumentumok méretének megőrzéséhez ne adjon hozzá nem lekérdezhető adatmennyiséget egy indexhez. A képek és más bináris adatfájlok nem kereshetők közvetlenül, és nem tárolhatók az indexben. A nem lekérdezhető adatmennyiség keresési eredményekbe való integrálásához meg kell adnia egy nem kereshető mezőt, amely az erőforrás URL-hivatkozását tárolja.

## <a name="option-2-add-resources"></a>2\. lehetőség: Erőforrások hozzáadása

A [standard szintű díjszabási](search-sku-tier.md) szinten kiépített szolgáltatások gyakran kihasználatlan kapacitást biztosítanak a tároláshoz és a munkaterhelésekhez (lekérdezésekhez vagy indexeléshez), ami [növeli a partíciót és a replikát, és](search-capacity-planning.md) nyilvánvaló megoldást kínál a következőhöz: nagyobb adatkészletek elhelyezése. A legjobb eredmény érdekében mindkét erőforrásra szüksége van: partíciók tároláshoz, valamint az adatfeldolgozáshoz szükséges replikák.

A megnövelt replikák és partíciók olyan számlázható események, amelyek növelik a költségeket, de ha a maximális terhelés alatt folyamatosan indexeli az indexelést, az indexelési folyamat időtartamára skálát adhat hozzá, majd az indexelés után lefelé állíthatja az erőforrás-szinteket. befejeződött.

## <a name="option-3-use-indexers"></a>3\. lehetőség: Indexelő használata

Az [Indexelő](search-indexer-overview.md) segítségével külső adatforrásokat lehet feltérképezni a támogatott Azure-adatplatformokon a kereshető tartalomhoz. Míg a nem kifejezetten a nagyméretű indexeléshez készült, több indexelő képesség különösen hasznos a nagyobb adatkészletek fogadásához:

+ Az ütemező segítségével rendszeres időközönként kioszthatja az indexelést, így az idő múlásával kiterjesztheti.
+ Az ütemezett indexelés az utolsó ismert leállítási ponton is folytatódhat. Ha egy adatforrást nem teljes egészében bemászott egy 24 órás időszakon belül, az indexelő a második napon folytatja az indexelést, bárhol marad.
+ Az adatmennyiség kisebb különálló adatforrásokra való particionálásával párhuzamos feldolgozást tesz lehetővé. A nagyméretű adathalmazok kioszthatók kisebb adatkészletekre, és létrehozhatók több indexelő adatforrás-definíciók is, amelyek párhuzamosan indexelhető.

> [!NOTE]
> Az indexelő az adatforrás-specifikus, ezért az indexelő megközelítés használata csak az Azure-beli kiválasztott adatforrások esetén használható: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos db](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Ütemezett indexelés

Az indexelő ütemezése fontos mechanizmus a nagyméretű adatkészletek feldolgozásához, valamint a lassú futású folyamatokhoz, például a képelemzéshez a kognitív keresési folyamatokban. Az indexelő feldolgozása egy 24 órás időszakon belül működik. Ha a feldolgozást 24 órán belül nem sikerül befejezni, az indexelő ütemezésének viselkedése működhet az előnyben. 

A tervezés szerint a ütemezett indexelés adott időközönként indul el, és a következő ütemezett időközön végzett folytatás előtt a feladatok általában befejeződik. Ha azonban a feldolgozás nem fejeződik be az intervallumon belül, az indexelő leáll (mert elfogyott az idő). A következő időszakban a feldolgozás folytatja az utolsó leállást, és a rendszer nyomon követi, hogy hol történik. 

A gyakorlatban az indexelési terhelés több napra kiterjed, és az indexelő 24 órás időpontra állíthatja. Ha a következő 24 órás ciklusban folytatódik az indexelés, az újraindul az utolsó ismert jó dokumentumon. Így az indexelő az összes feldolgozatlan dokumentum feldolgozását követően több napon belül is dolgozhat a dokumentumon. További információ erről a megközelítésről: [nagyméretű adatkészletek indexelése az Azure Blob Storage-ban](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Az ütemtervek beállításával kapcsolatos további információkért tekintse meg az [indexelő REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax) című témakört, vagy tekintse [meg, hogyan ütemezhet indexelő Azure Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Párhuzamos indexelés

A párhuzamos indexelési stratégia a több adatforrások egyidejű indexelésén alapul, ahol az egyes adatforrás-definíciók az adathalmazt határozzák meg. 

A nem rutin jellegű, számítási igényű indexelési követelmények – például a beolvasott dokumentumok OCR-je egy kognitív keresési folyamatban, képelemzés vagy természetes nyelvi feldolgozás – a párhuzamos indexelési stratégia gyakran a megfelelő megközelítés a a hosszú ideig futó folyamat a legrövidebb idő alatt. Ha törölheti vagy csökkentheti a lekérdezési kérelmeket, a párhuzamos indexelés olyan szolgáltatáson, amely nem kezeli egyszerre a lekérdezéseket, a legjobb stratégiai lehetőség a lassú feldolgozású tartalom nagy törzse. 

A párhuzamos feldolgozás a következő elemekből áll:

+ A forrásadatok felosztása több tároló vagy ugyanazon tárolóban található több virtuális mappa között. 
+ Rendelje hozzá az egyes mini-adatkészleteket saját [adatforrásához](https://docs.microsoft.com/rest/api/searchservice/create-data-source), párosítva a saját [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer)szolgáltatásával.
+ A kognitív kereséshez az egyes indexelő definíciók azonos [készségkészlet](https://docs.microsoft.com/rest/api/searchservice/create-skillset) hivatkozhat.
+ Írás ugyanabba a cél keresési indexbe. 
+ Ütemezze az összes indexelő futtatását egy időben.

> [!NOTE]
> A Azure Search nem támogatja a replikák vagy partíciók adott számítási feladatokhoz való kihelyezését. Az egyidejű indexelés kockázata nagy mértékben terheli a rendszerét a lekérdezési teljesítmény rovására. Ha tesztelési környezettel rendelkezik, először párhuzamos indexelést kell végrehajtania a kompromisszumok megismeréséhez.

### <a name="how-to-configure-parallel-indexing"></a>Párhuzamos indexelés konfigurálása

Az indexek esetében a feldolgozási kapacitás lazán a keresési szolgáltatás által használt egyes szolgáltatási egységek (SU) egy indexelő alrendszerén alapul. Több párhuzamos indexelő is lehetséges a Azure Search olyan alapszintű vagy standard szinten kiosztott szolgáltatások esetében, amelyek legalább két replikával rendelkeznek. 

1. A [Azure Portal](https://portal.azure.com)keresési szolgáltatás irányítópultjának **Áttekintés** lapján tekintse meg a **díjszabási szintet** annak megerősítéséhez, hogy képes-e párhuzamos indexelést fogadni. Az alapszintű és a standard csomag több replikát is kínál.

2. A **Beállítások** > **méretezése**elemnél növelje a párhuzamos feldolgozás [replikáit](search-capacity-planning.md) : egy további replikát az egyes indexelő munkaterhelésekhez. Hagyjon elegendő számot a meglévő lekérdezési kötethez. Az indexeléshez szükséges lekérdezési számítási feladatok feláldozása nem jó kompromisszum.

3. Az adatküldés több tárolóba is elosztható, Azure Search indexelő elérheti. Ez több tábla lehet Azure SQL Database, több tároló az Azure Blob Storage-ban vagy több gyűjtemény is. Definiáljon egy adatforrás-objektumot minden táblához vagy tárolóhoz.

4. Több indexelő létrehozása és ütemezett futtatása párhuzamosan:

   + Tegyük fel, hogy egy szolgáltatás hat replikával rendelkezik. A hat indexelő beállítása, amelyek mindegyike egy olyan adatforráshoz van rendelve, amely a teljes adathalmaz 6 irányú felosztására szolgáló adathalmaz egy-hatodik példányát tartalmazza. 

   + Minden indexelő ugyanarra az indexre mutasson. A kognitív keresési munkaterhelések esetében minden indexelő ugyanarra a készségkészlet mutat.

   + Az egyes indexelő definíciók között ütemezze a futtatási időpontot. Például `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` létrehoz egy 2018-05-15-os ütemtervet az összes indexelő esetében, amely nyolc órás időközönként fut.

Az ütemezett időpontban minden indexelő megkezdi a végrehajtást, az adattöltést, a dúsítások alkalmazását (ha a kognitív keresési folyamatot konfigurálta), és az indexbe ír. Azure Search nem zárolja a frissítések indexét. Az egyidejű írásokat a rendszer felügyeli, és újrapróbálkozik, ha egy adott írás nem sikerül az első kísérlet során.

> [!Note]
> A replikák növelésekor érdemes lehet növelni a partíciók darabszámát, ha az index mérete várhatóan jelentősen megnő. A partíciók az indexelt tartalom szeleteit tárolják; minél több partíció van, annál kisebb a szelet, amelyet egyenként tárolnia kell.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság a Azure Search](search-security-overview.md)
