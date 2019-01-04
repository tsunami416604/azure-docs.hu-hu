---
title: Index nagy méretű adatkészlet beépített indexelő – Azure Search használatával
description: Ismerje meg, hogy nagy mennyiségű adat az indexelés vagy nagy számítási igényű indexelő kötegelt módban, szabályozásával és milyen technikákkal lehet ütemezett, párhuzamos és elosztott indexelő stratégiák.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2f3d08a32384cea815f096f51b24eea596d0d118
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742235"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Nagy méretű adatkészleteket az Azure Search indexelése

Adatmennyiség-növekedés, vagy feldolgozási igényeinek megfelelően, előfordulhat, hogy alapértelmezett indexelési stratégiák nem lesznek gyakorlati. Azure Search szolgáltatásban a nagyobb adatkészletek, és a egy feltöltési kérés esetén egy adatforrás-specifikus indexelő segítségével ütemezett és elosztott számítási feladatokhoz való felépítésének elhelyezési több megoldása van.

Az azonos eljárások, amelyek nagy mennyiségű adat a hosszú futású folyamatok is vonatkozik. Különösen leírt lépések [párhuzamos indexelő](#parallel-indexing) hasznosak az indexelés nagy számítási igényű, például képelemzés vagy a természetes nyelvi feldolgozás alatt álló [cognitive search folyamatok](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Batch-indexelő

A legegyszerűbb mechanizmusok az indexelés egy nagyobb méretű adatkészlet egyik több dokumentumok vagy rekordokat egyetlen kérelem elküldéséhez. Mindaddig, amíg a teljes hasznos a 16 MB, egy kérelem egy tömeges feltöltés műveletben legfeljebb 1000 dokumentumot képes kezelni. Feltéve, hogy a [hozzáadása vagy frissítése dokumentumok REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), lenne becsomagolását, hogy a kérelem törzsében 1000 dokumentum.

Batch-indexelő valósítja meg az egyes kérések REST vagy a .NET használatával, vagy az indexelők révén. Az indexelők néhány eltérő korlátokkal alapján működnek. Pontosabban az Azure Blob-indexelés beállítja köteg mérete 10 dokumentumot a nagyobb méretű átlagos dokumentum elismeréseként. Az indexelők alapján a [indexelők REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ), beállíthatja a `BatchSize` argumentumának testre szabhatja ezt a beállítást, az adatok jellemzőinek megfelelő finomhangolását. 

> [!NOTE]
> Ne feledje alacsonyan tartani a dokumentumok méretétől, a kérelem nem lekérdezhető adatok kizárása. Képek és más bináris adatok nem közvetlenül kereshető, és nem tárolható az indexben. Keresési eredmények nem lekérdezhető adatokat integrálhat, meg kell határozni egy nem kereshető mező, amely tárolja az erőforrás URL-cím hivatkozást.

## <a name="add-resources"></a>Erőforrások hozzáadása

Egy üzembe helyezett szolgáltatások a [standard szintű tarifacsomag](search-sku-tier.md) gyakran eredményeztek rendelkezik kapacitás tárolási és számítási feladatok (lekérdezések vagy az indexelés), ami lehetővé teszi az [növelése a partíció és a replika számát ](search-capacity-planning.md) egy nagyobb méretű adatkészletek elhelyezési nyilvánvaló megoldás. A legjobb eredmények érdekében kell mindkét erőforrás: a partíciók a storage és az adatok feldolgozási munka replikái.

Növekvő replikák és partíciók üzenetnek, amely növelheti a költségeket, de, kivéve, ha folyamatosan indexelő maximális terhelés alatt, hozzáadhat méretezési az indexelési folyamat idejére, és indexelése követően adja meg az erőforrásszintek lefelé befejeződött.

## <a name="use-indexers"></a>Indexelők használata

[Az indexelők](search-indexer-overview.md) feltérképezi a külső adatforrásokhoz a kereshető tartalmak segítségével. Amíg nem kifejezetten szánt nagyméretű indexelő, több indexelő funkciók a következők különösen a nagyobb adatkészletek elhelyezésére:

+ Ütemezők ki, hogy Ön is teríteni idővel rendszeres időközönként indexelő csomagonkénti teszik lehetővé.
+ Ütemezett indexelő folytathatja az utolsó ismert leállításával pontján. Ha az adatforrás nem teljes mértékben bejárt 24 órás időtartamon belül, az indexelő folytatódik, ahol abbamaradtak két napon indexelő.
+ Adatok particionálása az egyes adatforrások kisebb lehetővé teszi a párhuzamos feldolgozást. Egy nagy méretű adatkészlet felosztása kisebb adatkészletek, és ezután hozzon létre több adatforrás-definíciók is indexelhető párhuzamosan.

> [!NOTE]
> Az indexelők a data-source-specifikus, így egy indexelő módszer használata esetén csak megvalósítható az Azure-ban a kijelölt adatforrások: [Az SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [a Blob storage-](search-howto-indexing-azure-blob-storage.md), [Table storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Ütemezett indexelése

Az indexelő ütemezése az egy fontos mechanizmusa nagy méretű adatkészleteket, valamint a lassan futó folyamatok, például képelemzés, a kognitív keresés folyamat feldolgozása. Az indexelő feldolgozási 24 órás időtartamon belül működik. Ha 24 órán belül befejezése meghiúsul, feldolgozás, a viselkedést az indexelő ütemezés előnyére is működik. 

A kialakításból fakadóan ütemezett adott időközönként indexelési elindul egy feladat a következő ütemezett időközönként folytatása előtt általában befejezése. Azonban feldolgozása a időtartamon belül nem fejeződik be, ha az indexelő leáll (mert idő). A következő időközönként, ahol utolsó abbamaradtak, a rendszer megtartja a feldolgozás folytatása nyomon követheti hol történik. 

Index terhelés több napig átfedés gyakorlatilag az indexelő helyezheti 24 órás ütemezés szerint. Ha folytatja, a következő 24 órás időszakra vonatkozó indexelést, azt az utolsó ismert jó dokumentum újraindul. Ezzel a módszerrel az indexelő nap után minden feldolgozatlan dokumentumok feldolgozása sorozatát keresztül működik a dokumentum a várakozó úgy. Ezzel a módszerrel kapcsolatos további információkért lásd: [nagy adatkészleteket az Azure Blob storage-indexelő](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Ütemezések általában beállításával kapcsolatos további információkért lásd: [indexelők REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Párhuzamos indexelése

Indexelő stratégia párhuzamos alapján indexelési több adatforrás egyszerre történik, ahol minden adatforrás-definíciót adja meg az adatok egy részét. 

Nem rutin, nagy számítási igényű indexelési követelmények – például a beolvasott dokumentumokat, a kognitív keresés folyamat, képelemzés, vagy természetes nyelvi feldolgozás - OCR stratégia indexelő párhuzamos gyakran a helyes megközelítés a egy a lehető legrövidebb időn hosszú ideig futó folyamat. Kiküszöbölése, vagy csökkentse a lekérdezési kérelmek, ha egy szolgáltatás, amely egyszerre nem kezeli a lekérdezések párhuzamos indexelő beállítás a legjobb stratégia a feldolgozása egy nagy terjesztett tartalom lassú feldolgozása révén. 

Párhuzamos feldolgozás rendelkezik, ezeket az elemeket:

+ Feloszthatja a forrásadatok között több tároló vagy több virtuális mappákban belül ugyanazt a tárolót. 
+ Képezze le az egyes mini adatkészlethez a saját [dátum forrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source), a saját párosított [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Cognitive Search hivatkozhat azonos [indexmezők](https://docs.microsoft.com/rest/api/searchservice/create-skillset) minden indexelő-definícióban.
+ Írja be a azonos cél keresési indexhez. 
+ Minden indexelő futtatása egy időben ütemezése.

> [!NOTE]
> Az Azure Search nem támogatja a rendelne a replikákat és partíciókat az adott számítási feladatokhoz. A nagy egyidejű indexelő kockázata a rendszer a lekérdezési teljesítmény rovására van túlterhelése. Ha olyan tesztkörnyezetben dolgozik, a párhuzamos indexelési hiba először megérteni a kompromisszumot kínál a megvalósítása.

### <a name="how-to-configure-parallel-indexing"></a>Párhuzamos indexelő konfigurálása

Az indexelők esetében a feldolgozási kapacitás lazán alapul egy indexelő alrendszer a search szolgáltatás által használt minden egyes szolgáltatás egység (SU). Több egyidejű indexelők az Azure Search-szolgáltatás legalább két replika kellene alap vagy Standard szint kiosztott is. 

1. Az a [az Azure portal](https://portal.azure.com), a search szolgáltatás irányítópultján **áttekintése** lapon kattintson a **tarifacsomag** lehetővé teszi párhuzamos indexelő megerősítéséhez. Alap és Standard rétegei több replikával.

2. A **beállítások** > **méretezési**, [növelni a replikák](search-capacity-planning.md) párhuzamos feldolgozáshoz: egy további replika indexelő csoportban minden számítási feladathoz. Hagyja bejelölve a meglévő lekérdezés köteten elegendő. Az indexelés a lekérdezési számítási feladatok feláldozása nem megfelelő egyensúlyt.

3. Az Azure Search-indexelők elérő szinten több tároló terjesztheti az adatait. Ez lehet több táblájából az Azure SQL Database, az Azure Blob storage-ban több tároló vagy több gyűjteményt. Adjon meg egy adatforrás-objektum mindegyik táblához vagy a tároló.

4. Hozzon létre, és több indexelők párhuzamos ütemezheti:

   + Tegyük fel, szolgáltatás, amely hat. Állítsa be a hat indexelők, mindegyik leképezve az adatkészlet esetében a 6 – kétutas felosztása a teljes adatkészlet hatodára kiterjedőre tartalmazó adatforrást. 

   + Minden indexelő mutasson ugyanazt az indexet. Cognitive search számítási feladatokhoz mutasson a minden indexelő az azonos képességek alkalmazási lehetőségét.

   + Minden indexelő meghatározásán futásidejű végrehajtási minta ütemezése. Ha például `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` minden indexelő nyolc órás időközönként futó 2018-05-15 egy ütemezés jön létre.

Az ütemezett időpontban minden indexelő esetében először végrehajtási, az adatok betöltése, végrehajtott információbeolvasás (Ha konfigurálta a kognitív keresés folyamat) alkalmazása és az index írása. Az Azure Search nem zárolja az index frissítéseit. Egyidejű írások Újrapróbálkozással, ha egy adott írási nem sikerül az első próbálkozás történik.

> [!Note]
> Növelje a replikák, vegye figyelembe a partíciók száma növelése, ha az index mérete előre jelzett költségről jelentősen megnő. Partíció tárolhat indexelt tartalom; szeletek a további partíciókra van, annál kisebb a szelet mindegyik rendelkezik tárolásához.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Az indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database-indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság az Azure Search szolgáltatásban](search-security-overview.md)