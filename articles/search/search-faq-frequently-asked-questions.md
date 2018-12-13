---
title: Gyakori kérdések (GYIK) – Azure Search
description: A Microsoft Azure Search szolgáltatás, a Microsoft Azure-ban üzemeltetett felhőalapú keresési szolgáltatás kapcsolatos gyakori kérdésekre adott válaszok.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9ea4e4ec78e5613758bd9e5ff7a4fbd3273208c6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312816"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Az Azure Search – gyakori kérdések (GYIK)

 Kapcsolatos fogalmakat, a kód és a forgatókönyvek az Azure Search kapcsolatos gyakori kérdésekre adott válaszok.

## <a name="platform"></a>Platform

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Miben különbözik az Azure Search teljes szöveges keresés a saját adatbázis-kezelő?

Az Azure Search támogatja több adatforráson, [számos nyelvet nyelvi elemzés](https://docs.microsoft.com/rest/api/searchservice/language-support), [egyéni elemző számára érdekes és szokatlan streamadat típusú bemenetek](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), keresés rangsorolják képernyőolvasókkal [pontozási profilok](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), valamint felhasználói élményt funkciókat typeahead, a találatok kiemelése és a jellemzőalapú navigáció. Más szolgáltatások, például a szinonimák és funkciókban gazdag lekérdezési szintaxis, is tartalmaz, de azok vannak általában nem sokoldalúbbá funkciók.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Mi a különbség az Azure Search és az Elasticsearch között?

A keresési technológiák összehasonlításakor ügyfelek gyakran kérje meg a részletekért az Azure Search hogyan hasonlítja össze az Elasticsearch segítségével. Ajánljuk, akik az Azure Search az Elasticsearch keresztül a keresés alkalmazás projektek általában megtenni, mert hajtottunk végre a fő feladat könnyebben vagy szükségük van a beépített integráció más Microsoft-technológiákhoz:

+ Azure Search egy teljes körűen felügyelt felhőszolgáltatás 99,9 %-os szolgáltatói szerződések (SLA), (2 replika olvasási hozzáférés, olvasási és írási esetén 3 replikával) elegendő redundanciával üzembe helyezésekor.
+ A Microsoft [természetes nyelvi processzorok](https://docs.microsoft.com/rest/api/searchservice/language-support) élvonalbeli nyelvi elemzési ajánlat.  
+ [Az Azure Search-indexelők](search-indexer-overview.md) Azure-adatforrás kezdeti és a növekményes az indexelés számos bejárható.
+ Ha a lekérdezés vagy a kötetek indexelő ingadozása való gyors reagálásról van szüksége, használhatja [csúszkavezérlőt](search-manage.md#scale-up-or-down) az Azure portal, vagy futtassa egy [PowerShell-parancsprogram](search-manage-powershell.md), szilánkkezelési megkerülésével közvetlenül.  
+ [Pontozó- és finomhangolási funkciókhoz](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) adja meg az eszközöket befolyásoló keresés rangsorolják pontszámok túl az önálló keresőmotor biztosíthat.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Az Azure Search szolgáltatás felfüggesztése és leállítása a számlázás?

A szolgáltatás szüneteltetése nem. Számítási és tárolási erőforrások vannak lefoglalva a kizárólagos használatára, ha a szolgáltatás létrehozása. Nem alkalmas felszabadítása és ezen erőforrások igény szerinti felszabadítani.

## <a name="indexing-operations"></a>Indexelési műveleteket

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Biztonsági mentés és visszaállítás (vagy töltse le és áthelyezése) indexek vagy index pillanatképek?

Bár [beolvasása egy indexdefiníciót](https://docs.microsoft.com/rest/api/searchservice/get-index) bármikor, nem áll fenn index a kinyerése, pillanatkép, vagy biztonsági mentési-visszaállítási funkciót letöltésével egy *feltöltve* index a helyi rendszer, a felhőben futó vagy Helyezze át egy másik Azure Search szolgáltatást.

Az indexek gyártja és tölti be a kódot írni, és csak az Azure Search a felhőben futtatni. Ügyfelek, akik át szeretné helyezni az index és egy másik szolgáltatás általában ehhez a kódot, hogy az új végpont szerkesztésével, és futtassa a indexelés. Ha azt szeretné, hogy a pillanatképet, illetve a biztonsági mentési index lehetővé teszi, leadott szavazattal [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Vissza tudok állítani az index vagy szolgáltatás törlés után?

Nem, az indexek vagy a szolgáltatások nem állítható vissza. Ha törli az Azure Search-index, a művelet végleges, és az index nem lehet helyreállítani. Ha törli az Azure Search szolgáltatást, a szolgáltatás az összes index véglegesen törlődik. Emellett ha törli egy Azure-erőforráscsoportot, amely egy vagy több Azure Search szolgáltatást tartalmaz, minden szolgáltatás véglegesen törlődik.  

Erőforrások, például indexek, indexelők, adatforrásokat és szakértelmével visszaállítása szükséges, a kódot újra. Indexek esetén meg kell újraindexelése külső forrásokból származó adatokat. Ezért erősen ajánlott, hogy megőrizze a fő példányt vagy egy másik adattárban, például az Azure SQL Database vagy a Cosmos DB az eredeti adatokkal.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>E indexelésére használhatja az SQL adatbázis-replikák (vonatkozik [Azure SQL Database-indexelők](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Nem vonatkoznak korlátozások használatát az elsődleges vagy másodlagos replikák adatforrásként egy teljesen új index létrehozását. Index frissítése (a megváltozott rekordokat alapján) a növekményes frissítések azonban csak az elsődleges replika. Ez a követelmény a változáskövetés milyen garanciákat csak az elsődleges replika SQL-adatbázis származik. Ha a másodlagos replikák próbáljon index frissítése számítási feladat, nincs garancia arra, kap minden adat.

## <a name="search-operations"></a>Keresés a műveletekben

### <a name="can-i-search-across-multiple-indexes"></a>Is kereshető több index esetében?

Nem, ez a művelet nem támogatott. Keresés minden esetben egy egyetlen index hatókörét.

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>Korlátozhatom keresési forrásgyűjteményébe hozzáférés felhasználó identitása szerint?

Megvalósíthat [biztonsági szűrők](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) az `search.in()` szűrőt. A szűrő composes jól [identity management-szolgáltatások, például az Azure Active regisztrált alkalmazástulajdonost](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) szűkítheti a keresési eredmények alapján definiált felhasználói csoporttagság.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Miért vannak nulla megegyezik a feltételek tudom, hogy érvényesek legyenek?

A leggyakoribb eset, hogy minden egyes lekérdezés típusa támogatja-e a különböző keresési viselkedések és nyelvi elemzéseket szintjei nem ismerete. A teljes szöveges keresés, amely a domináns munkaterhelés, egy nyelvi elemzési fázisa, amely megtöri a kifejezések csak a legfelső szintű űrlapok tartalmazza. Elemzési lekérdezés ezen tulajdonsága, mert a tokenekre kifejezés megfelel variantní hodnoty nagyobb számú kerül keresztül lehetséges egyezések, egy szélesebb körű net.

Helyettesítő karakter, az intelligens és regex lekérdezéseket, azonban nem elemzi, például a reguláris kifejezést lekérdezések, és gyenge visszaírási vezethet, ha a lekérdezés nem egyezik meg a word, a keresési indexben elemzett formájában. Elemzés lekérdezési és elemzési további információkért tekintse meg [architektúra lekérdezése](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Lassúak a helyettesítő karakteres kereséssel.

A legtöbb helyettesítő karakteres keresés lekérdezéseknél, például a előtag, az intelligens és regex, a rendszer újraírja belsőleg az egyező kifejezések a search-index. Vizsgálata a search-index, a további feldolgozás késéssel ad hozzá. További, a széles körű keresési lekérdezéseket, például `a*` például, amelyek valószínűleg sok feltételeket írni nagyon lassú lehet. A nagy teljesítményű helyettesítő karakteres kereséssel, fontolja meg a meghatározása egy [egyéni elemző](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Miért van a keresési besorolás minden nyomja le az 1.0-állandó vagy azzal egyenlő pontszámot?

Alapértelmezés szerint találatok alapján kell kiértékelni a [egyezési feltételek statisztikai tulajdonságainak](search-lucene-query-architecture.md#stage-4-scoring), és rendezett magastól alacsonyig az eredményben. Azonban néhány lekérdezése (altartományokra is kibővített, előtag, regex) típusú mindig közreműködés egy állandó pontszám a teljes dokumentum pontszámot. Ez a működésmód szándékos. Az Azure Search, találat lekérdezés bővítése keresztül anélkül, hogy befolyásolná a rangsorolás foglalandó az eredményeket, hogy egy állandó pontszám ír elő.

Tegyük fel például, hogy bemenetének "bemutató *" helyettesítő karakteres keresés eredménye megfelel a "bemutatók", "tourettes" és "tourmaline". Ezekkel az eredményekkel természetéből, nincs lehetőség ésszerűen célszámítógéppel a mely feltételek, mint a többi értékét. Ebből kifolyólag azt figyelmen kívül kifejezés gyakoriságok eredmények pontozási típusok helyettesítő karakteres előtag és reguláris kifejezés a lekérdezések során. Egy részleges bemenet alapján keresési eredmények elkerülése érdekében eltérés potenciálisan váratlan egyezések felé állandó pontszámot kapnak.

## <a name="design-patterns"></a>Tervezési minták

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Mi az a legjobb módja a honosított keresés megvalósításának?

A legtöbb ügyfél esetén ugyanazt az indexet különböző területi beállításokat (nyelv) támogatása, válasszon egy gyűjtemény elemein dedikált mezőket. Területibeállítás-specifikus mezők koncepcióját rendeljen egy megfelelő elemzőt. A Microsoft francia Analyzer például hozzárendelése egy francia karakterláncokat tartalmazó mezőt. Emellett leegyszerűsíti az szűrés. Ha tudja, fr-fr oldal indul el egy lekérdezést, korlátozhatja a keresési eredmények ebben a mezőben. Vagy hozzon létre egy [relevanciaprofil](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) biztosíthat a mezőben további relatív súly. Az Azure Search támogatja keresztül [50 nyelvi elemzők](https://docs.microsoft.com/azure/search/search-language-support) közül választhat.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatás vagy funkció a kérdése van? A szolgáltatás kérése a a [felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Lásd még

 [A StackOverflow: Az Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md)  
 [Mi az az Azure Search?](search-what-is-azure-search.md)
