---
title: Keresési index újraépítése
titleSuffix: Azure Cognitive Search
description: Új elemek hozzáadása, meglévő elemek vagy dokumentumok frissítése, vagy elavult dokumentumok törlése teljes újraépítés vagy részleges indexelés az Azure Cognitive Search index frissítéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498380"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Index újraépítése az Azure Cognitive Search szolgáltatásban

Ez a cikk ismerteti, hogyan lehet újraépíteni az Azure Cognitive Search index, a körülmények, amelyek között újraépítések szükségesek, és javaslatokat a folyamatban lévő lekérdezési kérelmek újraépítések hatásának enyhítésére.

Az *újraépítés* az indexhez társított fizikai adatstruktúrák elvetésére és újbóli létrehozására utal, beleértve az összes mezőalapú fordított indexet is. Az Azure Cognitive Search, nem lehet dobni, és újra az egyes mezőket. Az index újraépítéséhez az összes mezőtárolót törölni kell, újra létre kell hozni egy meglévő vagy módosított indexséma alapján, majd újra kell tölteni az indexbe leadott vagy külső forrásokból lekért adatokkal. 

Gyakori, hogy a fejlesztés során újraépíti az indexeket, de előfordulhat, hogy újra kell építenie egy termelési szintű indexet a szerkezeti változások, például összetett típusok hozzáadása vagy mezők hozzáadása a javaslatjavasolókhoz.

## <a name="rebuild-conditions"></a>Újraépítési feltételek

Ha az alábbi feltételek bármelyike teljesül, húzzon le, és hozzon létre újra indexet. 

| Állapot | Leírás |
|-----------|-------------|
| Meződefiníció módosítása | Mezőnév, adattípus vagy adott [tárgymutató-attribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index) (kereshető, szűrhető, rendezett, facetable) felülvizsgálatához teljes újraépítésre van szükség. |
| Elemző hozzárendelése mezőhöz | [Az elemzők](search-analyzers.md) egy indexben vannak definiálva, majd mezőkhöz vannak rendelve. Az indexhez bármikor hozzáadhat új analizátor-definíciót, de csak a mező létrehozásakor *rendelhet hozzá* elemzőt. Ez mind az **elemző,** mind az **indexanalyzer** tulajdonságaira igaz. A **searchAnalyzer** tulajdonság kivétel (ezt a tulajdonságot hozzárendelheti egy meglévő mezőhöz). |
| Elemződefiníció frissítése vagy törlése indexben | Az indexben nem törölheti vagy módosíthatja a meglévő elemzőkonfigurációt (elemző, jogkivonatoló, jogkivonat-szűrő vagy karakterszűrő), hacsak nem építi újra a teljes indexet. |
| Mező hozzáadása javaslatajánlóhoz | Ha egy mező már létezik, és hozzá szeretné adni egy [javaslatajánló](index-add-suggesters.md) konstruktrábhoz, újra kell építenie az indexet. |
| Mező törlése | A mező összes nyomkövetésének fizikai eltávolításához újra kell építenie az indexet. Ha az azonnali újraépítés nem praktikus, módosíthatja az alkalmazáskódot, hogy letiltsa a "törölt" mezőhöz való hozzáférést, vagy a [$select lekérdezési paraméterrel](search-query-odata-select.md) kiválaszthatja, hogy mely mezők jelenjenek meg az eredményhalmazban. Fizikailag a meződefiníció és a tartalom a következő újraépítésig az indexben marad, amikor olyan sémát alkalmaz, amely kihagyja a kérdéses mezőt. |
| Kapcsolószintek | Ha több kapacitásra van szüksége, nincs helybenfrissítés az Azure Portalon. Létre kell hozni egy új szolgáltatást, és az indexeket a nulláról kell építeni az új szolgáltatáson. A folyamat automatizálása érdekében használhatja az **index-backup-restore** mintakódot ebben az [Azure Cognitive Search .NET mintatárban.](https://github.com/Azure-Samples/azure-search-dotnet-samples) Ez az alkalmazás biztonsági másolatot készít az indexről egy sor JSON fájlra, majd újra létrehozza az indexet egy megadott keresési szolgáltatásban.|

## <a name="update-conditions"></a>Állapotok frissítése

Sok más módosításokat lehet tenni anélkül, hogy befolyásolnák a meglévő fizikai struktúrák. Pontosabban a következő módosítások *nem* igényelnek index-újraépítést. Ezekhez a módosításokhoz frissítheti az [indexdefiníciót](https://docs.microsoft.com/rest/api/searchservice/update-index) a módosításokkal.

+ Új mező hozzáadása
+ **Visszakereshető** attribútum beállítása meglévő mezőben
+ **SearchAnalyzer** beállítása meglévő mezőben
+ Új elemződefiníció hozzáadása indexhez
+ Pontozási profilok hozzáadása, frissítése vagy törlése
+ CORS-beállítások hozzáadása, frissítése vagy törlése
+ Szinonimaképek hozzáadása, frissítése vagy törlése

Új mező hozzáadásakor a meglévő indexelt bizonylatok null értéket kapnak az új mezőhöz. Egy jövőbeli adatfrissítés, a külső forrásadatokból származó értékek az Azure Cognitive Search által hozzáadott nulls helyett. Az indextartalom frissítésével kapcsolatos további tudnivalókért olvassa el a [Dokumentumok hozzáadása, frissítése vagy törlése című témakört.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)

## <a name="how-to-rebuild-an-index"></a>Index újraépítése

A fejlesztés során az indexséma gyakran változik. Megtervezheti azt olyan indexek létrehozásával, amelyek egy kis reprezentatív adatkészlettel gyorsan törölhetők, újralétrehozhatók és újratölthetők.

A már éles környezetben lévő alkalmazások esetében azt javasoljuk, hogy hozzon létre egy új indexet, amely egy meglévő index mellett fut, hogy elkerülje a lekérdezési állásidőt. Az alkalmazáskód átirányítást biztosít az új indexhez.

Az indexelés nem fut a háttérben, és a szolgáltatás kiegyenlíti a további indexelést a folyamatban lévő lekérdezésekkel. Az indexelés során figyelheti a [lekérdezési kérelmeket](search-monitor-queries.md) a portálon, hogy a lekérdezések időben befejeződjenek.

1. Határozza meg, hogy szükség van-e újraépítésre. Ha csak mezőket ad hozzá, vagy az index egy olyan részét módosítja, amely nem kapcsolódik a mezőkhöz, egyszerűen [frissítheti a definíciót](https://docs.microsoft.com/rest/api/searchservice/update-index) törlés, újbóli létrehozása és teljes újratöltése nélkül.

1. [Szerezzen be egy indexdefiníciót](https://docs.microsoft.com/rest/api/searchservice/get-index) arra az esetre, ha későbbi használatra lenne szüksége.

1. [Dobja el a meglévő indexet,](https://docs.microsoft.com/rest/api/searchservice/delete-index)feltételezve, hogy nem fut az új és a régi indexek egymás mellett. 

   Az indexet célzó lekérdezések azonnal elvetésre kerülnek. Ne feledje, hogy az index törlése visszafordíthatatlan, tönkretéve a mezők gyűjteményének és más konstrukciókfizikai tárolásának. Szünet gondolkodni a következményeit, mielőtt elejtette. 

1. [Hozzon létre egy módosított indexet,](https://docs.microsoft.com/rest/api/searchservice/create-index)ahol a kérelem törzse módosított vagy módosított meződefiníciókat tartalmaz.

1. [Töltse be az indexet](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) külső forrásból származó dokumentumokkal.

Az index létrehozásakor a rendszer az indexséma minden mezőjéhez fizikai tárolást rendel, és minden kereshető mezőhöz létrehoz egy fordított indexet. A nem kereshető mezők szűrőkben vagy kifejezésekben használhatók, de nem rendelkeznek fordított indexekkel, és nem teljes szöveges vagy fuzzy kereshetőek. Az index újraépítésekor ezek a fordított indexek törlődnek, és újra létrejönnek a megadott indexséma alapján.

Az index betöltésekénél minden mező fordított indexe az egyes dokumentumok összes egyedi, tokenizált szavaval együtt lesz feltöltve, a megfelelő dokumentumazonosítókhoz való leképezéssel. Például egy hotel adatkészletének indexelésekénél a Város mezőhöz létrehozott fordított index tartalmazhat Seattle, Portland és így tovább kifejezéseket. Azok a dokumentumok, amelyek a Város mezőben a Seattle vagy Portland mezőben szerepelnek, a dokumentumazonosítójuk a kifejezés mellett jelenik meg. Bármely [Hozzáadási, Frissítési vagy Törlési](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) műveletben a feltételek és a dokumentumazonosító-lista ennek megfelelően frissül.

> [!NOTE]
> Ha szigorú SLA-követelményekkel rendelkezik, érdemes lehet egy új szolgáltatás kiépítését kifejezetten ehhez a munkához, a fejlesztés és az indexelés a termelési indextől teljes elkülönítve történik. Egy külön szolgáltatás fut a saját hardver, kiküszöbölve az erőforrás-versengés lehetőségét. Ha a fejlesztés befejeződött, vagy hagyja az új indexet a helyén, átirányítva a lekérdezéseket az új végpontra és az indexre, vagy a kész kódot futtatja, hogy közzétegye a módosított indexet az eredeti Azure Cognitive Search szolgáltatáson. Jelenleg nincs mechanizmus a használatra kész index másik szolgáltatásba való áthelyezésére.

## <a name="check-for-updates"></a>Frissítések keresése

Az index lekérdezése az első dokumentum betöltése után azonnal megkezdhető. Ha ismeri egy dokumentum azonosítóját, a [Lookup Document REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) az adott dokumentumot adja vissza. A szélesebb körű tesztelés, meg kell várni, amíg az index teljesen betöltődik, és majd a lekérdezések segítségével ellenőrizze a környezetben várhatóan látni.

A [Kereséskezelő](search-explorer.md) vel vagy a [Postmanhez](search-get-started-postman.md) hasonló webes tesztelő eszközzel kereshet frissített tartalmat.

Ha mezőt adott hozzá vagy nevezett át, [$select](search-query-odata-select.md) adja vissza a mezőt:`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Nagy adatkészletek indexelése nagy méretekben](search-howto-large-index.md)
+ [Indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság az Azure Cognitive Search területén](search-security-overview.md)