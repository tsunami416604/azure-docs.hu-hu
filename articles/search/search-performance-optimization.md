---
title: Teljesítményorientált skálázás
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Azure Cognitive Search teljesítményének finomhangolásához és az optimális méretezés konfigurálásához szükséges technikákat és gyakorlati tanácsokat.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212386"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Teljesítményméretezés az Azure Cognitive Search szolgáltatásban

Ez a cikk a méretezhetőség és a rendelkezésre állás kifinomult követelményeit alkalmazó speciális forgatókönyvek ajánlott eljárásokat ismerteti.

## <a name="start-with-baseline-numbers"></a>Kezdés alaptervszámokkal

Mielőtt egy nagyobb üzembe helyezési erőfeszítés, győződjön meg róla, hogy egy tipikus lekérdezési terhelés néz ki. Az alábbi irányelvek segíthetnek az alapszintű lekérdezési számok megérkezése érdekében.

1. Válassza ki azt a célkésést (vagy maximális időtartamot), amelyet egy tipikus keresési kérelemnek teljesítenie kell.

1. Hozzon létre és teszteljen egy valós számítási feladatot a keresési szolgáltatással egy reális adatkészlettel a késési arányok méréséhez.

1. Kezdje a lekérdezések másodpercenkénti alacsony számával (QPS), majd fokozatosan növelje a tesztben végrehajtott aknak számát, amíg a lekérdezés késése az előre meghatározott cél alá nem csökken. Ez egy fontos viszonyítási alap, amely segít a méretezés megtervezésében, ahogy az alkalmazás használat növekszik.

1. Ahol csak lehetséges, használja fel újra a HTTP-kapcsolatokat. Ha az Azure Cognitive Search .NET SDK-t használja, ez azt jelenti, hogy újra fel kell használnia egy példányt vagy a [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) példányt, és ha a REST API-t használja, újra fel kell használnia egyetlen HttpClient-t.

1. Módosíthatja a lekérdezési kérelmek tartalmát, hogy a keresés az index különböző részein történjen. A módosítás azért fontos, mert ha folyamatosan ugyanazokat a keresési kérelmeket hajtja végre, az adatok gyorsítótárazása a teljesítmény jobb megjelenését eredményezi, mint egy eltérő lekérdezéskészlet teljében.

1. A lekérdezési kérelmek szerkezetének változhat, így különböző típusú lekérdezéseket kaphat. Nem minden keresési lekérdezés hajt végre ugyanazon a szinten. Például egy dokumentum keresési vagy keresési javaslat általában gyorsabb, mint a lekérdezés jelentős számú aspektusait és szűrőit. A teszt összetétele különböző lekérdezéseket is tartalmaznia kell, nagyjából ugyanolyan arányban, mint az éles környezetben elvárná.  

A tesztszámítási feladatok létrehozása során az Azure Cognitive Search néhány jellemzőjét szem előtt kell tartania:

+ Lehetséges, hogy túlterheli a szolgáltatást, ha egyszerre túl sok keresési lekérdezést lenyom. Amikor ez megtörténik, megjelenik a HTTP 503 válaszkódok. Az 503-as számú keresés elkerülése érdekében kezdje a keresési kérelmek különböző tartományaival, hogy a további keresési kérelmek hozzáadásakor láthassa a késési arányok közötti különbségeket.

+ Az Azure Cognitive Search nem futtat indexelési feladatokat a háttérben. Ha a szolgáltatás egyidejűleg kezeli a lekérdezési és indexelési számítási feladatokat, vegye figyelembe ezt az indexelési feladatok bevezetésével a lekérdezési tesztekben, vagy az indexelési feladatok csúcsidőn kívüli futtatásának lehetőségeinek feltárásával.

> [!Tip]
> Valósághű lekérdezési terhelést szimulálhat a terheléstesztelési eszközökkel. Próbálja ki [a betöltési tesztelést az Azure DevOps segítségével,](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) vagy használja az alábbi [alternatívák](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)egyikét.

## <a name="scale-for-high-query-volume"></a>Nagy lekérdezési mennyiség méretezése

A szolgáltatás túlterhelt, ha a lekérdezések túl sokáig tart, vagy amikor a szolgáltatás elindul a kérelmek eldobása. Ha ez történik, kétféleképpen oldhatja meg a problémát:

+ **Replikák hozzáadása**  

  Minden replika az adatok másolata, amely lehetővé teszi a szolgáltatás számára, hogy több példányban betöltse az elosztási kérelmeket.  Minden terheléselosztás és az adatok replikálása az Azure Cognitive Search által kezelt, és bármikor módosíthatja a szolgáltatás számára lefoglalt replikák számát. Egy szabványos keresési szolgáltatásban legfeljebb 12 replikát és 3 replikát foglalhat le egy alapszintű keresési szolgáltatásban. A replikák az [Azure Portalon](search-create-service-portal.md) vagy a [PowerShellben](search-manage-powershell.md)módosíthatók.

+ **Új szolgáltatás létrehozása magasabb szinten**  

  Az Azure Cognitive Search [számos rétegben érkezik,](https://azure.microsoft.com/pricing/details/search/) és mindegyik különböző teljesítményszinteket kínál. Bizonyos esetekben előfordulhat, hogy olyan sok lekérdezések, hogy a réteg, amelyen van, nem tud elegendő átfutási idő, még akkor is, ha a replikák maxed ki. Ebben az esetben fontolja meg egy magasabb teljesítményű szint, például a standard S3 szint, amelynek célja a forgatókönyvek nagy számú dokumentum és rendkívül magas lekérdezési számítási feladatok.

## <a name="scale-for-slow-individual-queries"></a>Méretezés lassú egyéni lekérdezésekhez

A magas késési arány másik oka egyetlen lekérdezés, amely túl sokáig tart. Ebben az esetben a replikák hozzáadása nem segít. Két lehetséges lehetőség, amelyek segíthetnek a következők:

+ **Partíciók növelése**

  A partíció felosztja az adatokat további számítási erőforrások között. Két partíció kettéosztja az adatokat, a harmadik partíció harmadokra osztja fel, és így tovább. Az egyik pozitív mellékhatása, hogy a lassabb lekérdezések néha gyorsabban teljesítenek a párhuzamos számítástechnikának köszönhetően. Az alacsony szelektivitású lekérdezések, például a sok dokumentumnak megfelelő lekérdezések vagy a nagyszámú dokumentum hozadékát biztosító metszenivalók párhuzamosságát észleltük. Mivel jelentős számításra van szükség a dokumentumok relevanciájának pontozásához vagy a dokumentumok számának kiszámításához, a további partíciók hozzáadása gyorsabban segít a lekérdezések teljesítésében.  
   
  A Standard search szolgáltatásban legfeljebb 12 partíció, az alapszintű keresési szolgáltatásban pedig 1 partíció lehet. A partíciók az [Azure Portalon](search-create-service-portal.md) vagy a [PowerShellben](search-manage-powershell.md)módosíthatók.

+ **Nagy számossági mezők korlátozása**

  A nagy számosságú mező egy olyan facetable vagy szűrhető mezőből áll, amely jelentős számú egyedi értékkel rendelkezik, és ennek eredményeképpen jelentős erőforrásokat használ fel az eredmények számításakor. Ha például egy Termékazonosítót vagy Leírás mezőt laptáblaként/szűrhetőként állít be, az nagy számosságnak számít, mivel a dokumentumtól a dokumentumig a legtöbb érték egyedi. Ahol csak lehetséges, korlátozza a magas számosságú mezők számát.

+ **Keresési szint növelése**  

  Egy magasabb Azure Cognitive Search szintre való áttérés egy másik módja lehet a lassú lekérdezések teljesítményének javításának. Minden magasabb szintű gyorsabb processzorokat és több memóriát biztosít, amelyek mindegyike pozitív hatással van a lekérdezés teljesítményére.

## <a name="scale-for-availability"></a>Méretezés az elérhetőséghez

Replikák nem csak segít csökkenteni a lekérdezés késését, de azt is lehetővé teszi a magas rendelkezésre állás. Egyetlen kópia esetén a szoftverfrissítések után a kiszolgáló újraindítása vagy más karbantartási események miatt időszakos állásidőre kell számítania. Ennek eredményeképpen fontos figyelembe venni, ha az alkalmazás megköveteli a keresések (lekérdezések) és az írási (indexelési események) magas rendelkezésre állását. Az Azure Cognitive Search sla-beállításokat kínál az összes fizetős keresési ajánlathoz, a következő attribútumokkal:

+ Két replikák magas rendelkezésre állású csak olvasható számítási feladatok (lekérdezések)

+ Három vagy több kópia az írási és olvasási számítási feladatok magas rendelkezésre állásához (lekérdezések és indexelés)

További részletekért látogasson el az [Azure Cognitive Search szolgáltatásiszint-szerződésre.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)

Mivel a replikák az adatok másolatai, több replikák lehetővé teszi az Azure Cognitive Search gép újraindítása és karbantartása egy replika, míg a lekérdezés végrehajtása folytatódik más replikákon. Ezzel szemben, ha a replikák el, akkor a lekérdezés teljesítményének romlása, feltételezve, hogy ezek a replikák voltak a kihasználatlan erőforrás.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Méretezés földrajzilag elosztott számítási feladatokhoz és georedundancia

A földrajzilag elosztott számítási feladatok esetén a gazdaadatközponttól távol található felhasználók magasabb késési arányokkal rendelkeznek. Az egyik megoldás az, hogy több keresési szolgáltatást olyan régiókban, amelyek közelebb ezek hez a felhasználókhoz.

Az Azure Cognitive Search jelenleg nem biztosít automatizált módszert az Azure Cognitive Search-indexek régiók közötti georeplikálására, de vannak olyan technikák, amelyek segítségével ez a folyamat egyszerűen megvalósítható és kezelhető. Ezeket a következő néhány szakasz ismerteti.

A keresési szolgáltatások földrajzilag elosztott készletének célja, hogy két vagy több index érhető el két vagy több régióban, ahol a felhasználó az Azure Cognitive Search szolgáltatáshoz kerül, amely a példában látható legalacsonyabb késést biztosítja:

   ![Szolgáltatások keresztlapja régiónként][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Az adatok szinkronizálásának megtartása több szolgáltatás között

Két lehetőség van az elosztott keresési szolgáltatások szinkronban tartására, amelyek az [Azure Cognitive Search Indexer](search-indexer-overview.md) vagy a Push API (más néven az [Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/)használatával állnak.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Indexelők használata több szolgáltatás tartalmának frissítéséhez

Ha már használja az indexelőt egy szolgáltatáson, konfigurálhatja egy második indexelőt egy másik szolgáltatáson, hogy ugyanazt az adatforrás-objektumot használja, és adatokat vonjon le ugyanahelyről. Minden egyes régióban minden szolgáltatás rendelkezik saját indexelővel és célindexszel (a keresési index nincs megosztva, ami azt jelenti, hogy az adatok duplikálódnak), de minden indexelő ugyanarra az adatforrásra hivatkozik.

Itt van egy magas szintű vizuális, hogy milyen lenne, hogy az építészet nézne ki.

   ![Egyetlen adatforrás elosztott indexelővel és szolgáltatáskombinációkkal][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>REST API-k használata több szolgáltatás tartalomfrissítéseinek lenyomásához

Ha az Azure Cognitive Search REST API-t használja az [Azure Cognitive Search indexében lévő tartalom leküldéses leküldéses,](https://docs.microsoft.com/rest/api/searchservice/update-index)a különböző keresési szolgáltatások szinkronban tarthatja a különböző keresési szolgáltatások módosításait, amikor frissítésre van szükség. A kódban ügyeljen arra, hogy kezelje azokat az eseteket, amikor egy keresési szolgáltatás frissítése sikertelen, de más keresési szolgáltatások esetében sikeres.

## <a name="leverage-azure-traffic-manager"></a>Az Azure Traffic Manager kihasználása

[Az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) lehetővé teszi, hogy a kérelmeket több földrajzi helyen lévő webhelyekre irányítsa, amelyeket ezután több keresési szolgáltatás támogat. A Traffic Manager egyik előnye, hogy az Azure Cognitive Search vizsgálata annak érdekében, hogy elérhető legyen, és a felhasználókat állásidő esetén alternatív keresési szolgáltatásokhoz irányíthatja. Emellett ha keresési kérelmeket küld az Azure-webhelyeken keresztül, az Azure Traffic Manager lehetővé teszi, hogy terheléselosztási esetekben, ahol a webhely fel, de nem az Azure Cognitive Search. Íme egy példa arra, hogy mi az architektúra, amely kihasználja a Traffic Manager.

   ![Szolgáltatások keresztlapja régiónként, központi forgalomkezelővel][3]

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az egyes csomagok és szolgáltatások korlátairól, olvassa el a Szolgáltatáskorlátok című [témakört.](search-limits-quotas-capacity.md) A partíció- és replikakombinációkról a [Kapacitás megtervezése.](search-capacity-planning.md)

A jelen cikkben tárgyalt technikák teljesítményéről és bemutatásáról a következő videót olvashatja:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
