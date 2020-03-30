---
title: Adatok importálása keresési indexbe az Azure Portal használatával
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogy az Azure Portalon az Adatok importálása varázsló val a Cosmos DB, a Blob storage, a table storage, az SQL Database és az SQL Server azure-beli virtuális gépekről való bejárása hogyan használható.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460699"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Adatok importálása varázsló az Azure Cognitive Search szolgáltatáshoz

Az Azure Portal egy **importadatok** varázsló az Azure Cognitive Search irányítópulton prototípus-készítési és -loading index. Ez a cikk a varázsló használatának előnyeit és korlátait, bemeneteket és kimeneteket, valamint néhány használati információt ismerteti. Gyakorlati útmutatást a varázsló beépített mintaadatok használatával való átlépéséhez tekintse meg az [Azure Cognitive Search index létrehozása az Azure Portal gyorsindításhasználatával](search-get-started-portal.md) című témakört.

A varázsló által végrehajtott műveletek a következők:

1 - Csatlakozzon egy támogatott Azure-adatforráshoz.

2 - Hozzon létre egy indexsémát, amelymintavételi forrásadatokból következtethet.

3 - Opcionálisan adjunk hozzá AI dúsításokat a tartalom és a szerkezet kinyeréséhez vagy létrehozásához.

4 - Futtassa a varázslót objektumok létrehozásához, adatok importálásához, ütemezés és egyéb konfigurációs beállítások beállításához.

A varázsló számos, a keresési szolgáltatásba mentett objektumot ad ki, amelyekprogramozott módon vagy más eszközökben érhetők el.

## <a name="advantages-and-limitations"></a>Előnyök és korlátozások

Mielőtt bármilyen kódot írna, használhatja a varázslót prototípus-készítési és koncepciópróba-tesztelésre. A varázsló külső adatforrásokhoz csatlakozik, mintát vesz az adatokból egy kezdeti index létrehozásához, majd json-dokumentumként importálja az adatokat az Azure Cognitive Search indexébe. 

Mintavételezés az a folyamat, amely egy index séma következtetni, és van néhány korlátozás. Az adatforrás létrehozásakor a varázsló kivesz egy dokumentummintát annak eldöntéséhez, hogy mely oszlopok tartoznak az adatforráshoz. Nem minden fájl olvasása, mivel ez potenciálisan órákig is eltarthat a nagyon nagy adatforrások esetében. A kijelölt dokumentumok alapján a forrásmetadatak , például a mező neve vagy típusa mezőgyűjteményt hoz nak létre egy indexsémában. A forrásadatok összetettségétől függően előfordulhat, hogy a pontosság érdekében szerkeszteni kell a kezdeti sémát, vagy ki kell terjesztenie azt a teljesség érdekében. A módosításokat a tárgymutató-definíciós lapon is elláthatja.

Összességében a varázsló használatának előnyei egyértelműek: mindaddig, amíg a követelmények teljesülnek, perceken belül prototípust tud unk használni egy lekérdezhető indexből. Az indexelés néhány bonyolultságát, például a JSON-dokumentumokként történő adatszolgáltatást a varázsló kezeli.

Az ismert korlátozások a következőképpen foglalhatóak össze:

+ A varázsló nem támogatja az iterációt vagy az újrafelhasználást. A varázsló minden fázisa új indexet, képességeket és indexelő konfigurációt hoz létre. Csak adatforrások maradhatnak meg, és használhatók fel újra a varázslóban. Más objektumok szerkesztéséhez vagy finomításához a REST API-k vagy a .NET SDK használatával kell beolvasnia és módosítania a műtárgyakat.

+ A forrástartalomnak egy támogatott Azure-adatforrásban kell lennie.

+ A mintavétel a forrásadatok egy részhalmaza felett van. Nagy adatforrások esetén a varázsló kihagyhatja a mezőket. Előfordulhat, hogy ki kell terjesztenie a sémát, vagy ki kell javítania a kikövetkeztetett adattípusokat, ha a mintavétel nem elegendő.

+ A i gazdagodás, amint azt a portál on-ra is kiteszik, néhány beépített készségre korlátozódik. 

+ A varázsló által létrehozható [tudástároló](knowledge-store-concept-intro.md)néhány alapértelmezett vetületre korlátozódik. Ha menteni szeretné a varázsló által létrehozott bővített dokumentumokat, a blobtároló és a táblák alapértelmezett nevekkel és szerkezettel vannak ellátni.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Adatforrás-bevitel

Az **Adatok importálása** varázsló csatlakozik egy külső adatforráshoz az Azure Cognitive Search indexelők által biztosított belső logikával, amelyek rendelkeznek a forrás mintavételezéséhez, a metaadatok olvasásához, a dokumentumok feltöréséhez a tartalom és a struktúra olvasásához, és a tartalom jsonként történő szerializálása az Azure Cognitive Search későbbi importálásához.

Csak egyetlen táblából, adatbázisnézetből vagy azzal egyenértékű adatszerkezetből importálhat, azonban a szerkezet hierarchikus vagy beágyazott alstruktúrákat is tartalmazhat. További információ: [Complex típusok modellezése.](search-howto-complex-data-types.md)

A varázsló futtatása előtt hozza létre ezt az egyetlen táblát vagy nézetet, amelynek tartalmat kell tartalmaznia. Nyilvánvaló okokból nincs értelme az Adatok **importálása** varázslót üres adatforráson futtatni.

|  Kiválasztás | Leírás |
| ---------- | ----------- |
| **Meglévő adatforrás** |Ha már definiált indexelők vannak definiálva a keresési szolgáltatásban, előfordulhat, hogy rendelkezik egy meglévő adatforrás-definícióval, amelyet újra felhasználhat. Az Azure Cognitive Search, adatforrás-objektumok csak az indexelők által használt. Az adatforrás-objektumokat programozott módon vagy az **Adatok importálása** varázslón keresztül hozhatja létre, és szükség szerint újra felhasználhatja.|
| **Minták**| Az Azure Cognitive Search két beépített mintaadatforrást biztosít, amelyeket oktatóanyagokban és rövid útmutatókban használnak: egy ingatlanSQL-adatbázist és egy Hotel adatbázist a Cosmos DB-n. A Hotelek minta alapján a [Hozzon létre indexet az Azure Portal](search-get-started-portal.md) on rövid útmutató című témakörben található. |
| [**Azure SQL Database**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |A szolgáltatás neve, az olvasási engedéllyel rendelkező adatbázis-felhasználó hitelesítő adatai, valamint az adatbázis neve megadható vagy az oldalon, vagy az ADO.NET kapcsolati sztring használatával. A tulajdonságok megtekintéséhez vagy testreszabásához válassza ki a kapcsolati sztring lehetőséget. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet.|
| **Azure virtuális gépen futó SQL Server** |Adja meg a teljesen minősített szolgáltatásnevét, felhasználói azonosítóját és jelszavát, valamint az adatbázist kapcsolati karakterláncként. Az adatforrás használatához a helyi tárolóban rendelkeznie kell egy korábban telepített tanúsítvánnyal, amely titkosítja a kapcsolatot. További információt az [SQL Virtuálisgép-kapcsolat az Azure Cognitive Search szolgáltatással című témakörben talál.](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|A követelmények a következőket tartalmazzák: a fiók, az adatbázis és a gyűjtemény. Az index tartalmazza a gyűjteményben szereplő összes dokumentumot. A lekérdezést megadhat a sorhalmaz összeolvasztására vagy szűrésére, vagy üresen hagyhatja a lekérdezést. Ebben a varázslóban nincs szükség lekérdezésre.|
| [**Azure Blob Storage**](search-howto-indexing-azure-blob-storage.md) |A követelmények a következőket tartalmazzák: a tárolási fiók és egy tároló. Szükség esetén, ha csoportosítási célból a blob-nevek egy virtuális elnevezési konvenciót követnek, akkor a név virtuáliskönyvtár-részét a tárolóban található mappa neveként is megadhatja. A további információkat [a Blob Storage indexelését](search-howto-indexing-azure-blob-storage.md) ismertető témakör tartalmazza. |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |A követelmények a következőket tartalmazzák: a tárolási fiók és egy táblanév. Szükség esetén megadhat egy lekérdezést a táblák részhalmazának beolvasásához. A további információkat [a Table Storage indexelését](search-howto-indexing-azure-tables.md) ismertető témakör tartalmazza. |

## <a name="wizard-output"></a>Varázsló kimenete

A színfalak mögött a varázsló létrehozza, konfigurálja és meghívja a következő objektumokat. A varázsló futtatása után a kimeneta megtalálható a portállapokon. A szolgáltatás Áttekintés lapján indexek, indexelők, adatforrások és skillsets listáját. Az indexdefiníciók teljes JSON-ban megtekinthetők a portálon. Más definíciók, használhatja a [REST API-t](https://docs.microsoft.com/rest/api/searchservice/) adott objektumok get.

| Objektum | Leírás | 
|--------|-------------|
| [Adatforrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Megőrzi a forrásadatokhoz való kapcsolódási adatok megőrzését, beleértve a hitelesítő adatokat is. Az adatforrás-objektum kizárólag indexelőkkel használható. | 
| [Index](https://docs.microsoft.com/rest/api/searchservice/create-index) | A teljes szöveges kereséshez és egyéb lekérdezésekhez használt fizikai adatstruktúra. | 
| [Skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | A tartalom kezelésére, átalakítására és alakítására vonatkozó utasítások teljes készlete, beleértve a képfájlokból származó információk elemzését és kibontását. A nagyon egyszerű és korlátozott struktúrák kivételével tartalmaz egy hivatkozást egy Cognitive Services-erőforrásra, amely gazdagítást biztosít. Opcionálisan tartalmazhat tudástároló-definíciót is.  | 
| [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Adatforrást, célindexet, választható képességeket, választható ütemezést és választható konfigurációs beállításokat megadó konfigurációs objektum a hibaátadáshoz és az alap-64 kódoláshoz. |


## <a name="how-to-start-the-wizard"></a>A varázsló indítása

Az Adatok importálása varázsló a szolgáltatás áttekintése lap parancssávjáról indul.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a keresési szolgáltatás lap az irányítópulton, vagy [keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatás listában.

2. A szolgáltatás áttekintése lapon a tetején kattintson az **Adatok importálása gombra.**

   ![Adatok importálása parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az Adatok importálása varázsló indítása")

Más Azure-szolgáltatásokból, például az Azure Cosmos DB-ből, az Azure SQL Database-ből és az Azure Blob storage-ból származó **adatok importálása** is elindíthatja. Keresse **meg** az Azure Cognitive Search hozzáadása a bal oldali navigációs ablaktáblán a szolgáltatás áttekintése oldalon.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Indexséma szerkesztése vagy befejezése a varázslóban

A varázsló hiányos indexet hoz létre, amelyet a bemeneti adatforrásból beszerzett dokumentumok kal tölt fel. Funkcionális index esetén győződjön meg arról, hogy a következő elemek vannak definiálva.

1. Kész a mezőlista? Adjon hozzá új mezőket, amelyek mintavételezik a kihagyott t, és távolítsa el azolyan mezőket, amelyek nem adnak értéket a keresési élményhez, vagy amelyek nem lesznek használva [szűrőkifejezésben](search-query-odata-filter.md) vagy [pontozási profilban.](index-add-scoring-profiles.md)

1. Megfelelő-e az adattípus a bejövő adatokhoz? Az Azure Cognitive Search támogatja az [entitásadatmodell (EDM) adattípusait.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Az Azure SQL-adatok, van [leképezési diagram,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) amely meghatározza az egyenértékű értékeket. További hátteret a [Mezőleképezések és átalakítások témakörben.](search-indexer-field-mappings.md)

1. Van egy mező, amely szolgálhat a *kulcs?* Ennek a mezőnek Edm.string nek kell lennie, és egyedileg kell azonosítania a bizonylatot. Relációs adatok esetén előfordulhat, hogy egy elsődleges kulcshoz van rendelve. A blobok esetében lehet `metadata-storage-path`a . Ha a mezőértékek szóközöket vagy kötőjeleket is tartalmaznak, akkor a **64-es kulcs kódása** beállítást a **Create a Indexer (Indexelő létrehozása)** **csoportSpeciális beállítások csoportjában**kell beállítania a karakterek érvényesítési ellenőrzésének letiltásához.

1. Attribútumok beállítása annak meghatározására, hogy a mező hogyan használható az indexben. 

   Szánjon időt erre a lépésre, mert az attribútumok határozzák meg az index mezőinek fizikai kifejezését. Ha később , még programozott módon is módosítani szeretné az attribútumokat, szinte mindig el kell dobnia és újra kell építenie az indexet. Az olyan alapvető attribútumok, mint **a kereshető** és **visszakereshető,** [elhanyagolható hatással vannak a tárolásra.](search-what-is-an-index.md#index-size) A szűrők engedélyezése és a javaslattevők használata növeli a tárolási követelményeket. 
   
   + **A kereshető** lehetővé teszi a teljes szöveges keresést. A szabad formátumú lekérdezésekben vagy lekérdezési kifejezésekben használt minden mezőnek rendelkeznie kell ezzel az attribútummal. Fordított indexek jönnek létre minden olyan mezőhöz, amelyet **kereshetőként**jelöl meg.

   + **A visszakereshető** függvény a keresési eredmények között adja vissza a mezőt. Minden olyan mezőnek, amely tartalmat biztosít a keresési eredményeknek, rendelkeznie kell ezzel az attribútummal. A mező beállítása nem befolyásolja észrevehetően az index méretét.

   + **A Szűrővel funkció** lehetővé teszi a mező szűrési kifejezésekben való hivatkozását. A **kifejezésben használt $filter** minden mezőnek rendelkeznie kell ezzel az attribútummal. A szűrőkifejezések pontos egyezéseket keresnek. Mivel a szöveges karakterláncok érintetlenek maradnak, további tárhelyre van szükség a szó szerinti tartalom befogadásához.

   + **A Facetable** lehetővé teszi a mező tetszéses navigációt. Csak **a Szűrhetőként** megjelölt mezők jelölhetők **meg Facetable**néven.

   + **A Rendezés** lehetővé teszi a mező tanként történő beállítását. A $Orderby kifejezésben használt **minden** mezőnek rendelkeznie kell ezzel az attribútummal.

1. [Lexikális elemzésre](search-lucene-query-architecture.md#stage-2-lexical-analysis)van szüksége? A **kereshető**Edm.string mezők esetén beállíthat **egy Elemzőt,** ha a nyelvvel bővített indexelést és lekérdezést szeretne. 

   Az alapértelmezett beállítás a *Standard Lucene,* de választhatja a *Microsoft English* nyelvet, ha a Microsoft analizátorát szeretné használni a speciális lexikai feldolgozáshoz, például a szabálytalan főnév és az igealak feloldásához. A portálon csak nyelvi elemzők adhatók meg. Egy egyéni analizátor vagy egy nem nyelvi analizátor, mint a kulcsszó, minta, és így tovább, meg kell tenni programozott módon. Az elemzőkkel kapcsolatos további információkért [lásd: Nyelvi elemzők hozzáadása.](search-language-support.md)

1. Szüksége van typeahead funkcióra automatikus kiegészítés vagy javasolt eredmények formájában? Jelölje be a **Javaslattevő** jelölőnégyzetet, ha engedélyezni szeretné [a typeahead lekérdezési javaslatokat és az automatikus kiegészítést](index-add-suggesters.md) a kijelölt mezőkön. A javaslatjavasolók hozzáadódnak a tokenizált kifejezések számához az indexben, és így több tárhelyet fogyasztanak.


## <a name="next-steps"></a>További lépések

A varázsló előnyeinek és korlátainak megértésének legjobb módja, ha végiglép rajta. Az alábbi rövid útmutató végigvezeti az egyes lépésein.

> [!div class="nextstepaction"]
> [Azure Cognitive Search index létrehozása az Azure Portal használatával](search-get-started-portal.md)