---
title: Gyakori kérdések (GYIK)
titleSuffix: Azure Cognitive Search
description: Válaszok a Microsoft Azure Cognitive Search szolgáltatással, amely a Microsoft Azure felhőalapú keresési szolgáltatása, gyakori kérdésekre adott válaszok.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d7ba62c795e23e41a1947def77300ffe5d2cc010
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262451"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Cognitive Search – gyakori kérdések (GYAKORI KÉRDÉSEK)

 Válaszok az Azure Cognitive Search használatával kapcsolatos fogalmakkal, kódokkal és forgatókönyvekkel kapcsolatos gyakori kérdésekre.

## <a name="platform"></a>Platform

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Miben különbözik az Azure Cognitive Search a teljes szöveges kereséstől a DBMS-ben?

Az Azure Cognitive Search támogatja a több adatforrást, [a nyelvi elemzést számos nyelven,](https://docs.microsoft.com/rest/api/searchservice/language-support)az érdekes és szokatlan [adatbevitelegyéni elemzését,](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)a [pontozási profilokon](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)keresztüli keresési rangsor-vezérlőket, valamint a felhasználói élmény funkcióit, például a typeaheadt, a leütéskiemelését és a jellemzőalapú navigációt. Más szolgáltatásokat is tartalmaz, például a szinonimákat és a gazdag lekérdezésszintaxist, de ezek általában nem differenciáló funkciók.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Szüneteltethetem az Azure Cognitive Search szolgáltatást, és leállíthatom a számlázást?

A szolgáltatás nem szüneteltethető. A számítási és tárolási erőforrások kizárólagos használatra vannak lefoglalva a szolgáltatás létrehozásakor. Ezeket az erőforrásokat nem lehet igény szerint felszabadítani és visszaszerezni.

## <a name="indexing-operations"></a>Indexelési műveletek

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Áthelyezi, biztonsági másolatot készít és visszaállítja az indexeket vagy az indexpillanatképeket?

A fejlesztési fázisban érdemes lehet áthelyezni az indexet a keresési szolgáltatások között. Használhatja például az alapszintű vagy ingyenes tarifacsomagot az index fejlesztéséhez, majd át szeretné helyezni a standard vagy magasabb szintre éles használatra. 

Vagy előfordulhat, hogy biztonsági másolatot szeretne készíteni egy index-pillanatképről olyan fájlokba, amelyek később visszaállíthatók. 

Ezeket a dolgokat az **Azure** [Cognitive Search .NET mintatárkódjával](https://github.com/Azure-Samples/azure-search-dotnet-samples)teheti meg. 

Indexdefiníciót is [bármikor lekaphat](https://docs.microsoft.com/rest/api/searchservice/get-index) az Azure Cognitive Search REST API használatával.

Jelenleg nincs beépített index kinyerése, pillanatkép, vagy biztonsági mentés-visszaállítási funkció az Azure Portalon. Azonban azt fontolgatjuk, hogy hozzáadjuk a biztonsági mentési és visszaállítási funkciót egy későbbi kiadásban. Ha azt szeretnénk, hogy mutassa meg támogatását ezt a funkciót, leadott egy szavazást [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Visszaállíthatom az indexemet vagy a szolgáltatásomat, ha azt törölték?

Nem, ha töröl egy Azure Cognitive Search indexet vagy szolgáltatást, az nem állítható helyre. Ha töröl egy Azure Cognitive Search szolgáltatást, a szolgáltatás összes indexe véglegesen törlődik. Ha töröl egy Azure-erőforráscsoportot, amely egy vagy több Azure Cognitive Search-szolgáltatást tartalmaz, az összes szolgáltatás véglegesen törlődik.  

Az erőforrások, például az indexek, indexelők, adatforrások és skillsets újra létre kell hozni őket a kódból. 

Index újbóli létrehozásához újra kell indexelnie a külső forrásokból származó adatokat. Emiatt ajánlott megőrizni az eredeti adatok fő példányát vagy biztonsági mentését egy másik adattárban, például az Azure SQL Database vagy a Cosmos DB.

Alternatív megoldásként használhatja az **index-backup-restore** mintakódot ebben az [Azure Cognitive Search .NET minta tárház](https://github.com/Azure-Samples/azure-search-dotnet-samples) a json-fájlok sorozatának biztonsági mentéséhez. Később az eszközzel és a fájlokkal szükség esetén visszaállíthatja az indexet.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Indexelhető kreklikák SQL adatbázis-replikákból [(az Azure SQL Database indexelőire vonatkozik)](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)

Nincsenek korlátozások az elsődleges vagy másodlagos replikák adatforrásként való használatára vonatkozóan, amikor egy teljesen új indexet hoz létre. Az index növekményes frissítésekkel való frissítése (a módosított rekordok alapján) azonban az elsődleges kópiát igényli. Ez a követelmény az SQL Database-ből származik, amely csak az elsődleges replikák on változáskövetést garantálja. Ha másodlagos replikákat próbál használni egy indexfrissítési számítási feladathoz, nincs garancia arra, hogy az összes adatot megkapja.

## <a name="search-operations"></a>Keresési műveletek

### <a name="can-i-search-across-multiple-indexes"></a>Kereshetek több indexközött?

Nem, ez a művelet nem támogatott. A keresés hatóköre mindig egyetlen indexre történik.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Korlátozhatom a keresési indexek hozzáférését felhasználói identitás szerint?

[A biztonsági szűrőket](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) szűrővel `search.in()` valósíthatja meg. A szűrő jól áll [az identitáskezelési szolgáltatásokkal, például az Azure Active Directory(AAD-val)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) a keresési eredmények meghatározott felhasználói csoporttagság alapján való levágására.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Miért van nulla egyezés olyan feltételekkel, amelyekről tudom, hogy érvényesek?

A leggyakoribb eset nem tudva, hogy minden lekérdezéstípus támogatja a különböző keresési viselkedéseket és nyelvi elemzési szinteket. A teljes szöveges keresés, amely az elsődleges munkaterhelés, tartalmaz egy nyelvi elemzési fázist, amely a kifejezéseket gyökérformákra bontja. Ez a szempont a lekérdezés elemzési vet egy szélesebb háló a lehetséges egyezések, mert a tokenized kifejezés megegyezik a nagyobb számú változatok.

A helyettesítő karakteres, az intelligens és regex-lekérdezéseket azonban a rendszer nem elemzi, mint a normál kifejezés- vagy kifejezéslekérdezéseket, és gyenge visszahíváshoz vezethet, ha a lekérdezés nem felel meg a szó elemzett formájának a keresési indexben. A lekérdezéselemzésről és -elemzésről a [lekérdezésarchitektúra című témakörben](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)talál további információt.

### <a name="my-wildcard-searches-are-slow"></a>A helyettesítő karakteres keresések lassúak.

A legtöbb helyettesítő karakteres keresési lekérdezés, például az előtag, az intelligens és regex, belsőleg van átírva a keresési indexben lévő egyező kifejezésekkel. A keresési index vizsgálatának további feldolgozása növeli a késést. Továbbá a széles körű `a*` keresési lekérdezések, például, amelyek valószínűleg sok kifejezéssel újraírhatók, nagyon lassúak lehetnek. A helyettesítő karakteres keresések elvégzéséhez érdemes [egyéni elemzőt definiálni.](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Miért van az, hogy a keresési rangsorban minden találathoz 1,0-t kell pontszámmal eltalálni?

Alapértelmezés szerint a keresési eredmények pontozzák a [megfelelő kifejezések statisztikai tulajdonságai](search-lucene-query-architecture.md#stage-4-scoring)alapján, és az eredményhalmazban magasra és alacsonyra rendezik. Egyes lekérdezéstípusok (helyettesítő karakter, előtag, regex) azonban mindig állandó pontszámot adnak a teljes dokumentumpontszámhoz. Ez a működésmód szándékos. Az Azure Cognitive Search állandó pontszámot ír elő, amely lehetővé teszi, hogy a lekérdezésbővítés en keresztül talált egyezések szerepeljenek az eredményekközött, anélkül, hogy ez befolyásolná a rangsorolást.

Tegyük fel például, hogy egy helyettesítő karakterkeresésben a "körutazás*" bemenete "túrák", "tourettes" és "tourmaline" találatokat eredményez. Tekintettel ezen eredmények jellegére, nincs mód arra, hogy ésszerűen következtetni, hogy mely kifejezések értékesebbek, mint mások. Ezért figyelmen kívül hagyjuk a kifejezésgyakoriságokat, amikor helyettesítő karakteres, előtag és regex típusú lekérdezések eredményének pontozása során. A részleges bemeneten alapuló keresési eredmények állandó pontszámot kapnak, hogy elkerüljék a potenciálisan váratlan egyezések irányába mutató elfogultságot.

## <a name="design-patterns"></a>Tervezési minták

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Mi a legjobb módszer a honosított keresés megvalósításához?

A legtöbb ügyfél dedikált mezőket választ a gyűjtemény hez képest, amikor az ugyanabban az indexben lévő különböző területi beállítások (nyelvek) támogatásáról van szó. A területi beállításoknak megfelelő mezők lehetővé teszik a megfelelő elemző hozzárendelését. Például a Microsoft French Analyzer hozzárendelése francia karakterláncokat tartalmazó mezőhöz. Emellett leegyszerűsíti a szűrést. Ha tudja, hogy a lekérdezés fr-fr oldalon történik, a keresési eredményeket erre a mezőre korlátozhatja. Vagy hozzon létre egy [pontozási profilt,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) hogy a mező nagyobb relatív súlyt. Az Azure Cognitive Search több mint [50 nyelvi elemzőt](https://docs.microsoft.com/azure/search/search-language-support) támogat.

## <a name="next-steps"></a>További lépések

A kérdése hiányzik egy hiányzó funkcióról vagy funkcióról? Kérje a funkciót a [User Voice webhelyén.](https://feedback.azure.com/forums/263029-azure-search)

## <a name="see-also"></a>Lásd még

 [StackOverflow: Azure kognitív keresés](https://stackoverflow.com/questions/tagged/azure-search)   
 [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)  
 [Mi az az Azure Cognitive Search?](search-what-is-azure-search.md)
