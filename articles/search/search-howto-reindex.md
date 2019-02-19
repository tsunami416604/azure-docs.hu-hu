---
title: Azure Search-index létrehozásához vagy frissítéséhez a kereshető tartalmak – Azure Search
description: Adja hozzá az új elemeket, frissítse a meglévő elemeket vagy dokumentumok vagy elavult dokumentumok Újraépítés teljes vagy részleges növekményes indexelő frissítése az Azure Search-index törlése.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341640"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Hogyan lehet Azure Search-index újraépítése

Ez a cikk bemutatja, hogyan építse újra az Azure Search-index a körülmények között, amelyek szerint újraépíteni szükségesek és javaslatok újraépíteni a folyamatban lévő lekérdezésekre vonatkozó kérelmek hatásának csökkentése.

A *újraépítése* elvetését és a egy index, beleértve az összes mező-alapú fordított index társított fizikai adatstruktúrák ismételt létrehozásával hivatkozik. Az Azure Search szolgáltatásban nem dobható el, majd hozza létre újra az egyes mezők. Az index újraépítése, törölni kell minden mező tároló, újra létre kell hozni egy meglévő vagy új indexsémát alapul, és majd tölteni adatokkal az index leküldött vagy külső forrásokból származhatnak. Indexek újraépítése a fejlesztés során gyakran, de szükség lehet egy éles környezetre index strukturális változtatásokat, például a komplex típusok hozzáadásával, vagy hogyan adhat mezőket javaslattevők befogadásához újraépítése.

Újraépíteni az indexet a hálózatról, ellentétben *Adatfrissítés* háttérfeladatként futtatja. Adhat hozzá, távolítsa el, és cserélje le a dokumentumok a minimális megszakítás lekérdezési számítási feladatok, bár a lekérdezések általában hosszabb időt vesz igénybe. Tartalom indexelése frissítésével kapcsolatos további információkért lásd: [törlése dokumentumok hozzáadása, frissítése vagy](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="rebuild-conditions"></a>Építse újra a feltételek

| Állapot | Leírás |
|-----------|-------------|
| Egy mező definíció módosítása | A mező nevét, a adattípus vagy a specifikus indítják [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index) (kereshető, szűrhető, rendezhető, kategorizálható) egy teljes Újraépítés igényel. |
| Egy elemző hozzárendelése egy mező | [Elemzők](search-analyzers.md) meghatározott index, és hozzárendeli az mezőket. Adhat hozzá egy új analyzer definíció index bármikor, de csak *hozzárendelése* egy elemző, ha a mező jön létre. Ez igaz mind a **analyzer** és **indexAnalyzer** tulajdonságait. A **searchAnalyzer** tulajdonság értéke (hozzárendelheti ezt a tulajdonságot existující Pole) kivételt. |
| Frissítse vagy egy index elemző-definíció törlése | Nem lehet törölni, vagy egy meglévő elemző konfigurációjának módosítása (elemző, tokenizer, jogkivonat-szűrő vagy char szűrő) az indexben, kivéve, ha a teljes index újraépítése. |
| Adjon hozzá egy mezőt a javaslattevő | Ha a mező már létezik, és adja hozzá a kívánt egy [Javaslattevők](index-add-suggesters.md) hozhatnak létre, újra kell építenie az indexet. |
| Mező törlése | Fizikailag távolítsa el az összes nyomkövetési egy mező, kell építenie az indexet. Egy azonnali rebuild nem célszerű, ha a "törölt" mezőben való hozzáférés letiltása az alkalmazás kódjában módosíthatja. Fizikailag a mező definíciója és annak tartalmát marad az index a következő készítse el, amikor alkalmazza a mező az áttekinthetőség kedvéért kihagyja sémát az adott. |
| Váltson a Rétegek | Ha nagyobb kapacitásra van szüksége, nem nem helyszíni frissítését. Egy új szolgáltatás létrehozása az új kapacitást pontján, és teljesen új indexek kell elkészíteni az új szolgáltatás a. |

Minden egyéb módosítás lehet kapcsolódni a meglévő fizikai struktúrák befolyásolása nélkül. Pontosabban, hajtsa végre a következő módosításokat *nem* egy indexkészítés megkövetelése:

+ Adjon hozzá egy új mezőt
+ Állítsa be a **lekérhető** existující Pole attribútum
+ Állítsa be a **searchAnalyzer** meglévő mezőre
+ Adja hozzá az új analyzer definícióját egy indexben
+ Hozzáadása, frissítése vagy törlése a pontozási profilok
+ Hozzáadása, frissítése vagy törlése a CORS-beállítások
+ Hozzáadása, frissítése vagy törlése synonymMaps

Amikor hozzáad egy új mezőt, a meglévő indexelt dokumentumok kapnak null értéket az új mező. Egy jövőbeli adatok frissítése a külső adatforrást értékeket cserélje le az Azure Search által hozzáadott null értékeket. Tartalom indexelése frissítésével kapcsolatos további információkért lásd: [törlése dokumentumok hozzáadása, frissítése vagy](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="partial-or-incremental-indexing"></a>Részleges vagy növekményes indexelése

Az Azure Search szolgáltatásban nem tudja vezérelni, mező alapon indexelő törlése, és hozza létre újra az adott mezők kiválasztása. Ehhez hasonlóan nem esetén nincs beépített mechanizmus [indexelést a dokumentumok kritériumok alapján](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents). Rendelkezik a feltételek adatvezérelt indexelő kapcsolatos követelmények egyéni teljesülniük kell.

Viszont, mi mindenre képes egyszerű, *dokumentumok frissítése* egy indexben. Számos keresési megoldás, a külső adatforrást az ideiglenes, és forráskód és a egy keresési index közötti szinkronizálás általános gyakorlat. A kódban, hívja a [törlése dokumentumok hozzáadása, frissítése vagy](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) műveletet, vagy a [.NET egyenértékű](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) frissíteni a tartalom indexelése, vagy adja meg az új mező adatait.

## <a name="partial-indexing-with-indexers"></a>Indexelők az indexelő részleges

[Az indexelők](search-indexer-overview.md) egyszerűsítheti az adatok frissítése. Az indexelő csak akkor tudja indexelni, több táblából vagy nézetből a külső adatforrásban. Több tábla indexelése, a legegyszerűbb, amelyhez csatlakozik, táblák és projektek nézet létrehozása az index kívánt oszlopokat. 

Indexelők, amelyek feltérképeznek külső adatforrások használata esetén ellenőrizze a forrásadatok egy "magas vízjelbe beleszámított" oszlophoz. Ha van ilyen, akkor is használja léptetési észlelési úgy, hogy csak a megadott új vagy módosított tartalmat tartalmazó sorok felvételét. A [Azure Blob storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), amely egy `lastModified` mezővel. A [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` ugyanazt a célt szolgálja. Ehhez hasonlóan mindkét [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) és [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md#indexing-changed-documents) mező a sor frissítések megjelölése. 

Az indexelők kapcsolatos további információkért lásd: [indexelő áttekintése](search-indexer-overview.md) és [alaphelyzetbe állítása az indexelő REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="how-to-rebuild-an-index"></a>Hogyan index újraépítése

A gyakori, teljes csomag újraépíti aktív fejlesztés során, amikor index sémák fluxus állapotban vannak. Alkalmazások már éles környezetben azt javasoljuk, amelyen egymás mellett egy meglévő index lekérdezés üzemszünet elkerülése érdekében új index létrehozását.

A szolgáltatási szintű olvasási és írási engedélyek szükségesek index frissítéseit. 

A portálon az index nem hozható létre újra. Programozott módon, meghívhatja [Update Index REST API-val](https://docs.microsoft.com/rest/api/searchservice/update-index) vagy [egyenértékű .NET API-k](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) teljesen újra kell építenie a. Index frissítési kérelmet megegyezik az [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index), de van egy másik környezetben.

Az alábbi munkafolyamat a REST API felé torzítatlan van, de a .NET SDK-t egyaránt érvényes.

1. Amikor szakember újból felhasználja az index nevét [dobja el a meglévő index](https://docs.microsoft.com/rest/api/searchservice/delete-index). 

   Újjáépítésére célzó lekérdezéseket a rendszer azonnal elveti. Index törlése nem vonható vissza, a mezők gyűjteménye és egyéb szerkezetek fizikai tároló megsemmisítése. Győződjön meg arról, hogy a következményekkel járhat az index törlése előtt dobja el, törölje. 

2. Állítson össze egy [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) a szolgáltatásvégpont-kérelem API-kulcsot, és a egy [adminisztrációs kulcsot](https://docs.microsoft.com/azure/search/search-security-api-keys). Egy rendszergazdai kulcsot kötelező megadni, az írási műveletek.

3. A kérelem törzsében adja meg az indexsémát megváltozott vagy módosított mező definíciókkal. A kérés törzse tartalmazza az indexsémát, valamint létrehozza a pontozási profilok, elemzőket, javaslattevőket és CORS-beállítások. A követelmények séma [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

4. Küldjön egy [Index frissítése](https://docs.microsoft.com/rest/api/searchservice/update-index) kérelem a fizikai kifejezést az Azure Search-index újraépítése. 

5. [Az index-dokumentumok betöltése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) külső forrásból.

Az index létrehozásakor fizikai tároló számára kíván lefoglalni az indexsémát az egyes mezők a fordított indexet létrehozni minden kereshető mező. Mezők, amelyek nem kereshető szűrők vagy kifejezéseket is használható, de nem rendelkezik fordított indexeket és a nem teljes szöveges vagy intelligens kereshető. Az index újraépítése a fordított indexekhez törölte és újra létrehozta, adja meg az indexsémát alapján.

Az index betöltésekor minden mező fordított index feltöltése az összes, a megfelelő dokumentum azonosítók leképezést, egyes dokumentumok egyedi, tokenekre szó. Például ha egy "Hotels" adatkészlet indexelést, fordított indexet létrehozni egy város mezőhöz tartalmazhat feltételek a budapesti, Portland, és így tovább. Dokumentumok, amelyek tartalmazzák a Seattle vagy Portland az városa mező kellene a dokumentumazonosító mellett a kifejezés szerepel. Bármely [hozzáadása, frissítése és törlése](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) műveletet, a feltételeket és a dokumentum Hálóazonosítóinak listája ennek megfelelően frissülnek.

> [!NOTE]
> Ha szigorúbb SLA követelményeinek, akkor érdemes kiépítési egy új szolgáltatás kimondottan a munkát, fejlesztési, és a teljes elkülönítést egy éles indexből előforduló indexelés. Egy különálló szolgáltatás fut, a saját hardveren, így kiküszöböli az erőforrás-versengés lehetőségét. Fejlesztési befejeződése után, vagy azt jelentené az új indexre helyen, a lekérdezések átirányítása az új végpont és az index, vagy futtatná a befejezett kód egy módosított indexe az eredeti Azure Search-szolgáltatás közzétételéhez. Jelenleg nincs olyan mechanizmus, egy használatra kész index áthelyezése egy másik szolgáltatásnak.

## <a name="view-updates"></a>Frissítések megtekintése

Elkezdheti egy index lekérdezése, amint az első dokumentum betöltése. Ha ismeri a dokumentum Azonosítóját, a [keresési dokumentum REST API-val](https://docs.microsoft.com/rest/api/searchservice/lookup-document) adja vissza az adott dokumentumhoz. A szélesebb körű tesztelés kell várja meg, hogy az index teljes betöltése, és lekérdezések használatával ellenőrizze a környezetet, a várt.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Index nagy mennyiségű nagy méretű adatkészletekhez](search-howto-large-index.md)
+ [Az indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database-indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság az Azure Search szolgáltatásban](search-security-overview.md)