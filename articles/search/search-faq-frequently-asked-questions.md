---
title: Gyakori kérdések (GYIK) – Azure Search
description: Válaszok a Microsoft Azure Search Serviceekkel kapcsolatos gyakori kérdésekre, a felhőben üzemeltetett keresési szolgáltatás a Microsoft Azureon.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d4aae2f2ef9ccbc645647125682d999c11c99ab6
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649831"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Azure Search – gyakran ismételt kérdések (GYIK)

 A Azure Search kapcsolódó fogalmakkal, kóddal és forgatókönyvekkel kapcsolatos gyakori kérdésekre adott válaszokat talál.

## <a name="platform"></a>Platform

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Miben különbözik az Azure Search a teljes szöveges kereséstől az adatbázis-kezelőben?

Azure Search több adatforrást támogat, [nyelvi elemzést készít számos nyelvre](https://docs.microsoft.com/rest/api/searchservice/language-support), valamint [az érdekes és szokatlan adatbevitelek egyéni elemzését](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), a keresési rangsor-vezérlést [pontozási profilokon](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)keresztül, valamint felhasználói élményt biztosító funkciókkal (például typeahead, találatok kiemelése és csiszolatlan navigálás. Emellett más funkciókat is tartalmaz, például a szinonimákat és a részletes lekérdezési szintaxist, de ezek általában nem különböztetik meg a funkciókat.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Mi a különbség a Azure Search és a Elasticsearch között?

A keresési technológiák összehasonlításakor az ügyfelek gyakran kérik a Azure Search összehasonlítását a Elasticsearch. Azok az ügyfelek, akik a keresési alkalmazás projektjeihez több mint Elasticsearch Azure Search választanak, általában azért, mert a kulcsfontosságú feladat egyszerűbb, vagy a Microsoft más technológiáinak beépített integrálására van szükségük:

+ A Azure Search egy teljes körűen felügyelt, 99,9%-os szolgáltatói szerződéssel (SLA) rendelkező felhőalapú szolgáltatás, amely elegendő redundanciával (2 replikával rendelkezik az olvasási hozzáféréshez, 3 replika az írási és olvasási műveletek esetében).
+ A Microsoft [természetes nyelvi processzorai](https://docs.microsoft.com/rest/api/searchservice/language-support) élvonalbeli nyelvi elemzést nyújtanak.  
+ [Azure Search indexelő](search-indexer-overview.md) számos Azure-adatforrást képes bejárni a kezdeti és a növekményes indexeléshez.
+ Ha a lekérdezési és indexelési kötetek esetében gyors reagálásra van szüksége, használhatja a Azure Portal [csúszka vezérlőelemeit](search-manage.md#scale-up-or-down) , vagy futtathat egy [PowerShell](search-manage-powershell.md)-parancsfájlt, amely megkerüli a szilánkok felügyeletét közvetlenül.  
+ A [pontozási és hangolási funkciók](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) lehetővé teszik a keresési rangsorolási pontszámok befolyásolását, ami azt jelenti, hogy a keresőmotor nem tudja megszabni a keresést.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Szüneteltethető Azure Search szolgáltatás, és nem lehet leállítani a számlázást?

A szolgáltatás nem szüneteltethető. A számítási és tárolási erőforrások a szolgáltatás létrehozásakor kizárólagos használatra vannak lefoglalva. Az erőforrások igény szerinti felszabadítása és visszaigénylése nem lehetséges.

## <a name="indexing-operations"></a>Indexelési műveletek

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>A biztonsági mentés és a visszaállítás (vagy letöltés és áthelyezés) indexek vagy indexek

Bár az [index definíciója](https://docs.microsoft.com/rest/api/searchservice/get-index) bármikor lekérhető, nem áll rendelkezésre index-kinyerési, pillanatfelvétel-visszaállítási funkció, amely a felhőben futó, egy helyi rendszerre vagy egy másik Azure Search szolgáltatásba helyezi át a feltöltött indexet.

Az indexek az Ön által írt kód alapján lettek felépítve és feltöltve, és csak a felhőben Azure Search futnak. Általában azok az ügyfelek, akik egy indexet egy másik szolgáltatásba kívánnak áthelyezni, egy új végpont használatára szerkesztik a kódokat, majd újra kell futtatniuk az indexelést. Ha azt szeretné, hogy pillanatképet vagy biztonsági másolatot készítsen egy indexről, szavazzon a [felhasználói hangon](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Visszaállíthatom az indexet vagy a szolgáltatást a törlés után?

Nem, az indexek és a szolgáltatások nem állíthatók vissza. Ha töröl egy Azure Search indexet, a művelet végleges, és az index nem állítható helyre. Azure Search szolgáltatás törlésekor a szolgáltatásban lévő összes index véglegesen törlődik. Ha töröl egy olyan Azure-erőforráscsoportot, amely egy vagy több Azure Search-szolgáltatást tartalmaz, az összes szolgáltatás véglegesen törlődik.  

Az olyan erőforrások visszaállításához, mint az indexek, az indexelő, az adatforrások és a szakértelmével, újra létre kell hozni őket a kódból. Indexek esetén a külső forrásokból származó adatok újraindexelése szükséges. Ezért erősen ajánlott megőrizni az eredeti adatok főmásolatát vagy biztonsági mentését egy másik adattárban, például Azure SQL Database vagy Cosmos DB.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Indexelhető az SQL Database-replikák ( [Azure SQL Database indexelő](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)alkalmazásra vonatkozik)

Az elsődleges vagy másodlagos replikák adatforrásként való használata nem vonatkozik a teljesen új indexek létrehozásakor. Az indexnek a növekményes frissítésekkel (módosított rekordok alapján) való frissítése azonban az elsődleges replikát igényli. Ez a követelmény SQL Databaseból származik, amely csak az elsődleges replikák változás-követését garantálja. Ha másodlagos replikákat próbál használni egy index-frissítési feladathoz, akkor nem garantáljuk az összes adat beszerzését.

## <a name="search-operations"></a>Keresési műveletek

### <a name="can-i-search-across-multiple-indexes"></a>Kereshetek több index között?

Nem, ez a művelet nem támogatott. A keresés mindig egyetlen indexre terjed ki.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Korlátozható a keresési index hozzáférése a felhasználói identitás alapján?

A`search.in()` szűrővel [biztonsági szűrőket](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) alkalmazhat. A szűrő jól együttműködik a [(z) Azure Active Directory (HRE) identitás-kezelési szolgáltatásokkal](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) , hogy a keresési eredményeket a definiált felhasználói csoporttagság alapján vágja ki.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Miért van nulla egyezés az érvényes feltételek alapján?

A leggyakoribb eset az, hogy az egyes lekérdezési típusok a különböző keresési viselkedéseket és a nyelvi elemzések szintjét támogatják. A teljes szöveges keresés, amely a legelterjedtebb munkaterhelés, olyan nyelvi elemzési fázist tartalmaz, amely lebontja a kifejezést a legfelső szintű űrlapokra. A lekérdezések elemzésének ezen aspektusa szélesebb körű, a lehetséges egyezésekre mutató, a jogkivonatok által megadottnál nagyobb számú változatot vet fel.

A helyettesítő karakteres, a zavaros és a regex lekérdezéseket azonban nem elemezze, például a reguláris kifejezéseket vagy a kifejezéseket, és gyenge visszahívást eredményezhet, ha a lekérdezés nem felel meg a szó elemzett formátumának a keresési indexben. A lekérdezések elemzésével és elemzésével kapcsolatos további információkért lásd: [lekérdezési architektúra](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>A helyettesítő karakteres keresések lassúak.

A legtöbb helyettesítő karakteres keresési lekérdezést, például az előtagot, a fuzzy és a regexet, a rendszer a keresési indexben lévő egyező kifejezésekkel együtt írja felül. A keresési index ellenőrzésének további feldolgozása a késéshez. Emellett a széles körű keresési lekérdezések, `a*` mint például a sok kifejezéssel valószínűleg újraírhatók, nagyon lassúak lehetnek. Az elvégezhető helyettesítő karakteres keresések esetében érdemes lehet [Egyéni elemzőt](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)definiálni.

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Miért rangsorolja a keresés a 1,0 konstans vagy egyenlő pontszámát minden találatot illetően?

Alapértelmezés szerint a keresési eredmények a [megfeleltetési feltételek statisztikai tulajdonságai](search-lucene-query-architecture.md#stage-4-scoring)alapján vannak kiértékelve, és az eredményhalmaz magas – alacsony értékre vannak rendezve. Néhány lekérdezési típus (helyettesítő karakter, előtag, regex) azonban mindig a teljes dokumentum pontszámával járul hozzá az állandó pontszámhoz. Ez a működésmód szándékos. A Azure Search egy állandó pontszámot ír elő, amely lehetővé teszi a lekérdezési kiterjesztésen keresztül talált egyezések felvételét az eredményekbe, anélkül, hogy ez befolyásolná a rangsorolást.

Tegyük fel például, hogy a helyettesítő karakteres keresésben szereplő "Tour *" bemenet a "Tours", a "Tourettes" és a "turmalin" egyezéseket eredményez. Ezeknek az eredményeknek a jellegéből adódóan nem lehet ésszerűen következtetni, hogy mely feltételek sokkal értékesek, mint mások. Ezért figyelmen kívül hagyja a kifejezés gyakoriságát, ha a helyettesítő karakteres, előtag és regex típusú lekérdezéseket eredményez. A keresési eredmények részleges bevitel alapján állandó pontszámot kapnak, hogy elkerülje a potenciálisan váratlan egyezések torzítását.

## <a name="design-patterns"></a>Tervezési minták

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Mi a legjobb megközelítés a honosított keresés megvalósításához?

A legtöbb ügyfél dedikált mezőket választ egy gyűjteményben, amikor különböző területi beállításokat (nyelveket) támogat ugyanabban az indexben. A területi beállításokkal megadott mezők segítségével megfelelő elemzőt rendelhet hozzá. Tegyük fel például, hogy a Microsoft francia analizátort egy francia sztringeket tartalmazó mezőhöz rendeli. Emellett leegyszerűsíti a szűrést. Ha tudja, hogy egy lekérdezés egy fr-fr oldalon van kezdeményezve, a keresési eredmények erre a mezőre korlátozhatók. Vagy hozzon létre egy [pontozási profilt](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , hogy a mező a relatív súlyozást adja. Azure Search támogatja a több mint [50 nyelvi elemzőt](https://docs.microsoft.com/azure/search/search-language-support) , hogy közül választhat.

## <a name="next-steps"></a>További lépések

Egy hiányzó funkcióval vagy funkcióval kapcsolatos kérdése van? Kérje a szolgáltatást a [felhasználói](https://feedback.azure.com/forums/263029-azure-search)hangposta webhelyén.

## <a name="see-also"></a>Lásd még

 [StackOverflow Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [A teljes szöveges keresés működése Azure Search](search-lucene-query-architecture.md)  
 [Mi az az Azure Search?](search-what-is-azure-search.md)
