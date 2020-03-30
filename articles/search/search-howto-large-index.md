---
title: Nagy adatkészlet indexelése beépített indexelők használatával
titleSuffix: Azure Cognitive Search
description: Stratégiák nagy adatindexeléshez vagy számítási szempontból intenzív indexeléshez kötegelt módban, erőforrás-elemzésen és az ütemezett, párhuzamos és elosztott indexelési technikákon keresztül.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190959"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Nagy adatkészletek indexelése az Azure Cognitive Search szolgáltatásban

Az adatkötetek növekedésével vagy a feldolgozási igények változásával előfordulhat, hogy az egyszerű vagy alapértelmezett indexelési stratégiák már nem praktikusak. Az Azure Cognitive Search esetében számos módszer létezik a nagyobb adatkészletek elhelyezésére, az adatfeltöltési kérelmek strukturálásától kezdve az ütemezett és elosztott számítási feladatok forrásspecifikus indexelőjének használatáig.

Ugyanezek a technikák vonatkoznak a hosszú ideig futó folyamatokra is. A [párhuzamos indexelésben](#parallel-indexing) ismertetett lépések különösen hasznosak a számításigényes indexeléshez, például a képelemzéshez vagy a természetes nyelvi feldolgozáshoz egy [AI dúsítási folyamatban.](cognitive-search-concept-intro.md)

A következő szakaszok három, nagy mennyiségű adat indexelésének technikáját ismertetik.

## <a name="option-1-pass-multiple-documents"></a>1. lehetőség: Több dokumentum átadása

A nagyobb adatkészletek indexelésének egyik legegyszerűbb mechanizmusa, ha egyetlen kérelemben több dokumentumot vagy rekordot küld el. Mindaddig, amíg a teljes hasznos adat 16 MB alatt van, a kérelem legfeljebb 1000 dokumentumot képes kezelni egy tömeges feltöltési műveletben. Ezek a korlátok attól függetlenül érvényesek, hogy a [Documents REST hozzáadása API-t](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) vagy a .NET SDK [Index metódusát](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) használja. Mindkét API esetében 1000 dokumentumot kell csomagolnia az egyes kérelmek törzsébe.

A kötegindexelés rest vagy .NET vagy indexelők használatával történő egyedi kérelmekhez van megvalósítva. Néhány indexelő konként működik. Pontosabban az Azure Blob indexelése a kötegméretet 10 dokumentumra állítja be a nagyobb átlagos dokumentumméret elismeréseként. [Indexelő létrehozása REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)alapú indexelők esetén `BatchSize` beállíthatja, hogy az argumentum testre szabja ezt a beállítást, hogy jobban megfeleljen az adatok jellemzőinek. 

> [!NOTE]
> A dokumentum méretének lekicsiben tartása érdekében ne adjon hozzá nem lekérdezhető adatokat az indexhez. A képek és más bináris adatok közvetlenül nem kereshetők, és nem tárolhatók az indexben. Ha nem lekérdezhető adatokat szeretne integrálni a keresési eredményekbe, meg kell határoznia egy nem kereshető mezőt, amely az erőforrás URL-hivatkozását tárolja.

## <a name="option-2-add-resources"></a>2. lehetőség: Erőforrások hozzáadása

Szolgáltatások, amelyek a standard [tarifacsomagok](search-sku-tier.md) egyikén vannak kiépítve, gyakran kihasználatlan kapacitással rendelkeznek mind a tárolási, mind a számítási feladatokhoz (lekérdezések vagy indexelés), ami [a partíció és a replika számának növelését](search-capacity-planning.md) a nagyobb adatkészletek elhelyezésére nyilvánvaló megoldássá teszi. A legjobb eredmény érdekében mindkét erőforrásra szüksége van: a tárolási partíciókra és az adatbetöltési munka replikáira.

A replikák és partíciók növelése számlázható események, amelyek növelik a költségeket, de ha nem folyamatosan indexel maximális terhelés alatt, hozzáadhat léptéket az indexelési folyamat időtartama alatt, majd az indexelés után visszaállíthatja az erőforrásszinteket lefelé. Kész.

## <a name="option-3-use-indexers"></a>3. lehetőség: Indexelők használata

[Az indexelők](search-indexer-overview.md) a támogatott Azure-adatforrások kereshető tartalomfeltérképezésére szolgálnak. Bár nem kifejezetten nagyméretű indexeléshez készült, számos indexelő képesség különösen hasznos a nagyobb adatkészletek befogadásához:

+ Az ütemezők lehetővé teszik az indexelés rendszeres időközönkénttörténő csomagolását, hogy az idő múlásával eloszthassa.
+ Az ütemezett indexelés az utolsó ismert megállóhelyen folytatódhat. Ha egy adatforrás nem teljesen bejárása egy 24 órás ablakon belül, az indexelő folytatja az indexelést a második napon, ahol abbahagyta.
+ Az adatok kisebb egyedi adatforrásokra történő particionálása lehetővé teszi a párhuzamos feldolgozást. A forrásadatokat kisebb összetevőkre bonthatja, például az Azure Blob storage több tárolóra, majd létrehozhat megfelelő, több [adatforrás-objektumot](https://docs.microsoft.com/rest/api/searchservice/create-data-source) az Azure Cognitive Search szolgáltatásban, amelyek párhuzamosan indexelhetők.

> [!NOTE]
> Az indexelők adatforrás-specifikusak, így az indexelő megközelítés használata csak az Azure-beli kiválasztott adatforrások esetében használható: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob storage](search-howto-indexing-azure-blob-storage.md), Table [storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Ütemezett indexelés

Indexelő ütemezése fontos mechanizmus a nagy adatkészletek feldolgozásához, valamint a lassú folyamatok, például a képelemzés egy kognitív keresési folyamat. Az indexelő feldolgozása 24 órás időszakon belül működik. Ha a feldolgozás nem fejeződik be 24 órán belül, az indexelő ütemezésének viselkedése az Ön előnyére válhat. 

Az ütemezett indexelés tervezés szerint meghatározott időközönként kezdődik, és a feladat általában befejeződik, mielőtt a következő ütemezett intervallumban folytatná. Ha azonban a feldolgozás nem fejeződik be az intervallumon belül, az indexelő leáll (mert kifutott az időből). A következő időközönként a feldolgozás ott folytatódik, ahol az utolsó abbahagyta, és a rendszer nyomon követi, hogy ez hol történik. 

A gyakorlatban a több napon át terjedő indexterhelések esetében az indexelőt 24 órás ütemezésszerint helyezheti el. Amikor az indexelés folytatódik a következő 24 órás ciklus, újraindul az utolsó ismert jó dokumentum. Ily módon az indexelő napok on-át dolgozhat egy dokumentumhátralékon, amíg az összes feldolgozatlan dokumentumot fel nem dolgozza fel. Erről a megközelítésről további információt a [Nagy adatkészletek indexelése az Azure Blob storage-ban című témakörben talál.](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets) Az ütemezések beállításáról bővebben az [Indexelő REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) című témakörben vagy [az Azure Cognitive Search indexelők ütemezése](search-howto-schedule-indexers.md)című témakörben olvashat bővebben.

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Párhuzamos indexelés

A párhuzamos indexelési stratégia több adatforrás egyszerre történő indexelésén alapul, ahol minden adatforrás-definíció az adatok egy részét határozza meg. 

A nem rutinszerű, számításigényes indexelési követelmények – például a kognitív keresési folyamatban, képelemzésben vagy természetes nyelvi feldolgozásban beolvasott dokumentumokon – a párhuzamos indexálási stratégia gyakran a megfelelő megközelítés a a legrövidebb idő alatt. Ha kiküszöbölheti vagy csökkentheti a lekérdezési kérelmeket, a lekérdezéseket nem egyszerre kezelő szolgáltatás párhuzamos indexelése a legjobb stratégia a lassú feldolgozású tartalom nagy törzsén való munkavégzéshez. 

A párhuzamos feldolgozás a következő elemekből áll:

+ Ossza fel a forrásadatokat több tároló vagy több virtuális mappa között ugyanabban a tárolóban. 
+ Az egyes mini adatkészleteket a saját [adatforrásához](https://docs.microsoft.com/rest/api/searchservice/create-data-source)rendelje hozzá , párosítva a saját [indexelőjével.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ A kognitív kereséshez hivatkozzon az egyes indexelő definíciók ban ugyanazokat a [skillseteket.](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ Írja be ugyanazt a célkeresési indexet. 
+ Ütemezze az összes indexelőt egyszerre való futtatásra.

> [!NOTE]
> Az Azure Cognitive Search, nem rendelhet hozzá egyedi replikák vagy partíciók indexelés vagy lekérdezésfeldolgozás. A rendszer határozza meg az erőforrások felhasználásának módját. A lekérdezési teljesítményre gyakorolt hatás megértéséhez megpróbálhatja a párhuzamos indexelést egy tesztkörnyezetben, mielőtt éles környezetbe görgetné.  

### <a name="how-to-configure-parallel-indexing"></a>Párhuzamos indexelés konfigurálása

Indexelők esetében a feldolgozási kapacitás lazán alapul egy indexelő alrendszer minden egyes szolgáltatási egység (SU) által használt keresési szolgáltatás. Több egyidejű indexelők is lehetséges az Azure Cognitive Search-szolgáltatások alapszintű vagy standard szintű, legalább két replikával rendelkező azure cognitive Search-szolgáltatások. 

1. Az [Azure Portalon](https://portal.azure.com)a keresési szolgáltatás **irányítópultjának áttekintése** lapon ellenőrizze a **tarifacsomag,** hogy tudja kezelni a párhuzamos indexelés. Mind az alapszintű, mind a standard szintű szintek több replikát kínálnak.

2. A **Beállítások** > **méretezése**párbeszédpanelen növelje a párhuzamos feldolgozás [replikáit:](search-capacity-planning.md) minden egyes indexelő-munkaterheléshez egy további replika. Hagyjon elegendő számot a meglévő lekérdezési kötethez. A lekérdezési számítási feladatok indexeléshez való feláldozása nem jó kompromisszum.

3. Az adatokat több tárolóba oszthatja olyan szinten, amelyet az Azure Cognitive Search indexelői elérhetnek. Ez lehet több tábla az Azure SQL Database-ben, több tároló az Azure Blob storage-ban, vagy több gyűjtemény. Minden táblához vagy tárolóhoz adjon meg egy adatforrás-objektumot.

4. Hozzon létre és ütemezzen több indexelőt párhuzamosan futtatandó anno:

   + Tegyük fel, hogy egy szolgáltatás hat replikák. Konfiguráljon hat indexelőt, amelyek mindegyike az adatkészlet egyhatodát tartalmazó adatforráshoz van rendelve a teljes adatkészlet hatirányú felosztásához. 

   + Mutasson minden indexelőt ugyanarra az indexre. A kognitív keresési számítási feladatok, pont minden indexelő ugyanazzal a skillset.

   + Az egyes indexelő-definíciókon belül ütemezze ugyanazt a futásidejű végrehajtási mintát. Például `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` létrehoz egy ütemezést a 2018-05-15 minden indexelők, nyolc órás időközönként futó.

Az ütemezett időpontban az összes indexelők megkezdik a végrehajtást, az adatok betöltését, a dúsítások alkalmazását (ha kognitív keresési folyamatot konfigurált), és írásban az indexbe. Az Azure Cognitive Search nem zárolja a frissítések indexét. Az egyidejű írások kezelése történik, és az újrapróbálkozás, ha egy adott írási művelet sikertelen az első próbálkozásra.

> [!Note]
> Replikák növelésekor fontolja meg a partíciók számának növelése, ha az index mérete az előrejelzések szerint jelentősen növekszik. A partíciók indexelt tartalom szeleteit tárolják; minél több partícióvan, annál kisebb a szelet mindegyik kell tárolni.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság az Azure Cognitive Search területén](search-security-overview.md)
