---
title: Horizontális felskálázás beépített indexelő – Azure Search-indexelő
description: Adja hozzá az új elemeket, frissítse a meglévő elemeket vagy dokumentumok vagy elavult dokumentumok Újraépítés teljes vagy részleges növekményes indexelő frissítése az Azure Search-index törlése.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5f268de43f4f860458c062cb80e5bea0134b4407
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316678"
---
# <a name="how-to-scale-out-indexing-in-azure-search"></a>Hogyan lehet horizontális felskálázás az Azure Search szolgáltatásban az indexelés

Adatmennyiség-növekedés, vagy feldolgozási igényeinek megfelelően, Észreveheti, hogy egyszerű [újraépíteni és a feladatok újraindexelés](search-howto-reindex.md) elegendő nem állnak. 

Első lépésként érdekében nagyobb terhelést, azt javasoljuk, hogy növelje a [méretezési csoport és a kapacitás](search-capacity-planning.md) a meglévő szolgáltatás határain belül. 

A második lépésben, ha [indexelők](search-indexer-overview.md), méretezhető indexeléshez mechanizmusok hozzáadja. Indexelők az egy beépített Scheduler használatával, amely lehetővé teszi, hogy rendszeres időközönként indexelése ki csomagonkénti származnak, vagy feldolgozási meghaladja a 24 órás időszak kiterjesztése. Ezenkívül párosítva az adatforrás-definíciók, az indexelők segítségével egy formája párhuzamosság elérése adatok particionálása és az ütemezések használatával párhuzamosan.

### <a name="scheduled-indexing-for-large-data-sets"></a>Ütemezett nagy méretű adatkészleteket indexelése

Ütemezése az egy fontos mechanizmusa nagy méretű adatkészleteket és a lassú lefutású elemzéseket, például képelemzés, a kognitív keresés folyamat feldolgozása. Az indexelő feldolgozási 24 órás időtartamon belül működik. Ha 24 órán belül befejezése meghiúsul, feldolgozás, a viselkedést az indexelő ütemezés előnyére is működik. 

A kialakításból fakadóan ütemezett adott időközönként indexelési elindul egy feladat a következő ütemezett időközönként folytatása előtt általában befejezése. Azonban feldolgozása a időtartamon belül nem fejeződik be, ha az indexelő leáll (mert idő). A következő időközönként, ahol utolsó abbamaradtak, a rendszer megtartja a feldolgozás folytatása nyomon követheti hol történik. 

Index terhelés több napig átfedés gyakorlatilag az indexelő helyezheti 24 órás ütemezés szerint. Ha folytatja, a következő 24 órás stint az indexelést, azt az utolsó ismert jó dokumentum újraindul. Ezzel a módszerrel az indexelő nap után minden feldolgozatlan dokumentumok feldolgozása sorozatát keresztül működik a dokumentum a várakozó úgy. Ezzel a módszerrel kapcsolatos további információkért lásd: [nagy méretű adatkészletek indexelése](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Párhuzamos indexelése

Egy második el egy indexelő stratégia párhuzamos beállításához. A nem rendszeres nagy számítási igényű követelményeket, például a beolvasott dokumentumokat, a kognitív keresés folyamat, optikai Karakterfelismerés indexelő stratégia indexelő párhuzamos lehet a megfelelő módszer az adott cél elérésében tett. A kognitív keresés Adatbővítés folyamat képelemzés, és a természetes nyelvi feldolgozás hosszúak futtatása. Párhuzamos indexelő szolgáltatás, amely egyszerre nem kezeli a lekérdezési kérelmek az oka az lehet, hogy egy nagy terjesztett tartalom lassú feldolgozási végighalad egy működőképes beállítása. 

Egy párhuzamos feldolgozási stratégiát ezeket az elemeket:

+ A szakasz az forrás adatok között több tároló vagy több virtuális mappákban belül ugyanazt a tárolót. 
+ Egyes mini adatkészlethez való leképezése egy [dátum forrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source), a saját párosított [indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Cognitive Search hivatkozhat azonos [indexmezők](https://docs.microsoft.com/rest/api/searchservice/create-skillset) minden indexelő-definícióban.
+ Írja be a azonos cél keresési indexhez. 
+ Minden indexelő futtatása egy időben ütemezése.

> [!Note]
> Az Azure Search nem támogatja a rendelne a replikákat és partíciókat az adott számítási feladatokhoz. A nagy egyidejű indexelő kockázata a rendszer a lekérdezési teljesítmény rovására van túlterhelése. Ha olyan tesztkörnyezetben dolgozik, a párhuzamos indexelési hiba először megérteni a kompromisszumot kínál a megvalósítása.

## <a name="configure-parallel-indexing"></a>Párhuzamos indexelés konfigurálása

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