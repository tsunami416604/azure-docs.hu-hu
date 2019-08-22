---
title: Az adatimportálás keresési indexbe Azure Portal-Azure Search használatával
description: Megtudhatja, hogyan használhatja az adatok importálása varázslót a Azure Portal az Azure-adatok Cosmos DB, blob Storage, Table Storage, SQL Database és SQL Server Azure-beli virtuális gépeken való bejárásához.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e784cbf351bd062712e0fd66332799907a3bcae8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648255"
---
# <a name="import-data-wizard-for-azure-search"></a>Adatimportálási varázsló Azure Search

Az Azure portál Azure Search irányítópultján található **Adatok importálása** varázsló segítségével az adatok betölthetők egy indexbe. A varázsló az indexelési folyamat számos lépésének automatizálásávalállítja be és hívja meg az adatforrást, az *indexet*és az indexelő: 

* Egy külső adatforráshoz csatlakozik ugyanahhoz az Azure-előfizetéshez.
* Az optikai karakterfelismerést vagy természetes nyelvi feldolgozást is integrálhat a strukturálatlan adatokból származó szöveg kinyeréséhez.
* Indexet hoz létre a külső adatforrás adatmintavételezése és metaadatai alapján.
* Feltérképezi az adatforrást kereshető tartalomra, szerializálja és betölti a JSON-dokumentumokat az indexbe.

A varázsló nem tud csatlakozni előre definiált indexhez vagy egy meglévő indexelő futtatásához, de a varázslóban új indexet vagy indexelő is beállíthat, hogy támogassa a szükséges struktúrát és viselkedést.

Mik az Azure Search újdonságai? A [gyors üzembe helyezés lépései: Az importálás, az indexelés és a lekérdezés a](search-get-started-portal.md) portál eszközeivel tesztelheti az importálási és indexelési eszközöket az **importálási** és indexelési adatkészletek használatával.

## <a name="start-importing-data"></a>Az adatimportálás megkezdése

Ez a szakasz a varázsló elindítását és az egyes lépések áttekintését ismerteti.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a keresési szolgáltatás lapot az irányítópulton, vagy [Keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatás listában.

2. A felül található szolgáltatás áttekintése lapon kattintson az adatimportáláselemre.

   ![Adatimportálási parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az adatimportálás varázsló elindítása")

   > [!NOTE]
   > Az **adatok importálását** más Azure-szolgáltatásokból is elindíthatja, többek között Azure Cosmos DB, Azure SQL Database és az Azure Blob Storage-ból. Keresse meg a **hozzáadás Azure Search** a bal oldali navigációs ablaktáblán a szolgáltatás áttekintése oldalon.

3. Megnyílik a varázsló az **adataihoz**való kapcsolódáshoz, ahol kiválaszthatja az importáláshoz használni kívánt külső adatforrást. Ennek a lépésnek több tudnivalója is van, ezért a további részletekért olvassa [](#data-source-inputs) el az adatforrás bemenetek szakaszt.

   ![Adatimportálási varázsló a portálon](./media/search-import-data-portal/import-data-wizard-startup.png "Adatimportálási varázsló Azure Search")

4. A következő a **kognitív keresés hozzáadása**abban az esetben, ha a képfájlok szövegének optikai karakterfelismerését (OCR), vagy a strukturálatlan adatokon végzett szöveges elemzést szeretné megadni. A feladathoz Cognitive Services AI-algoritmusok vannak behúzva. Ez a lépés két részből áll:
  
   Először [csatoljon egy Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md) egy Azure Search készségkészlet.
  
   Másodszor válassza ki, hogy mely AI-gazdagítók szerepeljenek a készségkészlet. A bemutató szemléltetését ebben a rövid [](cognitive-search-quickstart-blob.md)útmutatóban találhatja meg.

   Ha csak az adatimportálást szeretné, hagyja ki ezt a lépést, és lépjen közvetlenül az index definíciója elemre.

5. A következő a **célként megadott index testreszabása**, ahol elfogadhatja vagy módosíthatja a varázslóban bemutatott index sémát. A varázsló kikövetkezteti a mezőket és adattípusokat a mintavételezési adatok alapján, és metaadatokat olvas be a külső adatforrásból.

   Az egyes mezőknél [jelölje be az index attribútumokat](#index-definition) az egyes viselkedések engedélyezéséhez. Ha nem választ ki attribútumokat, az index nem használható. 

6. Ezután **hozzon létre egy**indexelő, amely a varázsló terméke. Az indexelő egy olyan webbejáró, amely egy külső Azure-adatforrásból gyűjt kereshető adatokat és metaadatokat. Ha kijelöli az adatforrást, és csatolja a szakértelmével (nem kötelező) és az indexet, akkor a varázsló egyes lépésein áthelyezve egy indexelő konfigurálhat.

   Adja meg az indexelő nevét, és kattintson a **Submit (Küldés** ) gombra az importálási folyamat megkezdéséhez. 

Az indexelést a portálon figyelheti, ha az indexelő lehetőségre kattint az **Indexelő** listában. A dokumentumok betöltésével a meghatározott index dokumentumszámlálója növekszik. Néha beletelik néhány percbe, mire a portál oldala tükrözi a legfrissebb adatokat.

Az index készen áll a lekérdezésre, amint az első dokumentum betöltődik. Ehhez a feladathoz használhatja a [keresési Explorert](search-explorer.md) .

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Adatforrás bemenetei

Az **adatok importálása** varázsló egy állandó adatforrás-objektumot hoz létre, amely megadja a kapcsolódási adatokat egy külső adatforráshoz. Az adatforrás-objektumot kizárólag [Indexelő](search-indexer-overview.md) anyagokkal lehet használni, és a következő adatforrásokhoz hozhatók létre: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure-Table Storage](search-howto-indexing-azure-tables.md) (a [kognitív keresési](cognitive-search-concept-intro.md) folyamatok esetében nem támogatott)

Csak egyetlen táblából, adatbázis-nézetből vagy ezzel egyenértékű adatszerkezetből importálhat, azonban a struktúra hierarchikus vagy beágyazott alstruktúrákat is tartalmazhat. További információkért lásd: [komplex típusok modellezése](search-howto-complex-data-types.md).

A varázsló futtatása előtt létre kell hoznia ezt az adatstruktúrát, és tartalmaznia kell a tartalmat. Ne futtassa az **adatimportálás** varázslót üres adatforráson.

|  Választás | Leírás |
| ---------- | ----------- |
| **Meglévő adatforrás** |Ha a Search-szolgáltatásban már meg vannak adva indexelők, másik importálási folyamathoz kiválaszthat egy meglévő adatforrás-definíciót. Azure Search az adatforrás-objektumokat csak indexelő használják. Az adatforrás-objektumokat programozott módon vagy az **adatimportálás** varázslóval is létrehozhatja.|
| **Példák**| Azure Search egy ingyenes globális Azure SQL Database-adatbázist üzemeltet, amelyről megtudhatja, hogyan importálhatja és kérdezheti le a kérelmeket a Azure Searchban. Lásd [: gyors útmutató: Az importálás, az index és a lekérdezés a](search-get-started-portal.md) portál eszközeivel egy forgatókönyvhöz. |
| **Azure SQL Database** |A szolgáltatás neve, az olvasási engedéllyel rendelkező adatbázis-felhasználó hitelesítő adatai, valamint az adatbázis neve megadható vagy az oldalon, vagy az ADO.NET kapcsolati sztring használatával. A tulajdonságok megtekintéséhez vagy testreszabásához válassza ki a kapcsolati sztring lehetőséget. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| **Azure virtuális gépen futó SQL Server** |Adja meg a teljes szolgáltatásnevet, a felhasználói azonosítót és a jelszót, valamint az adatbázist a kapcsolatok karakterláncként. Az adatforrás használatához a helyi tárolóban rendelkeznie kell egy korábban telepített tanúsítvánnyal, amely titkosítja a kapcsolatot. Részletes útmutatásért lásd az [SQL virtuális gép az Azure Search-höz történő csatlakoztatását](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) ismertető témakört. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| **Cosmos DB** |A követelmények a következőket tartalmazzák: a fiók, az adatbázis és a gyűjtemény. Az index tartalmazza a gyűjteményben szereplő összes dokumentumot. Megadhat egy lekérdezést, amely lelapul vagy szűrheti a sorhalmazt, vagy hagyja üresen a lekérdezést. A varázslóban nincs szükség lekérdezésre.|
| **Azure Blob Storage** |A követelmények a következőket tartalmazzák: a tárolási fiók és egy tároló. Szükség esetén, ha csoportosítási célból a blob-nevek egy virtuális elnevezési konvenciót követnek, akkor a név virtuáliskönyvtár-részét a tárolóban található mappa neveként is megadhatja. A további információkat [a Blob Storage indexelését](search-howto-indexing-azure-blob-storage.md) ismertető témakör tartalmazza. |
| **Azure Table Storage** |A követelmények a következőket tartalmazzák: a tárolási fiók és egy táblanév. Szükség esetén megadhat egy lekérdezést a táblák részhalmazának beolvasásához. A további információkat [a Table Storage indexelését](search-howto-indexing-azure-tables.md) ismertető témakör tartalmazza. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Index attribútumai

Az **adatok importálása** varázsló létrehoz egy indexet, amely a bemeneti adatforrásból beszerzett dokumentumokkal lesz feltöltve. 

Funkcionális index esetén győződjön meg arról, hogy a következő elemek vannak meghatározva.

1. Az egyik mezőt **kulcsként**kell megjelölni, amely az egyes dokumentumok egyedi azonosítására szolgál. A **kulcsnak** *EDM. String típusúnak*kell lennie. 

   Ha a mezőértékek szóközöket vagy kötőjeleket tartalmaznak, a **Speciális beállítások**területen a **Base-64 kódolási kulcs** beállítását kell megadnia a következő karakterek érvényesítési ellenőrzésének mellőzéséhez.

1. Adja meg az egyes mezők index attribútumait. Ha a nincs attribútumok lehetőséget választja, az index lényegében üres, a szükséges kulcs mező kivételével. Legalább egy ilyen attribútumot ki kell választania az egyes mezőkhöz.
   
   + A beolvasható érték a keresési eredményekben lévő mezőt adja vissza. A keresési eredményekhez tartalmat biztosító minden mezőnek rendelkeznie kell ezzel az attribútummal. A mező beállítása nem befolyásolja az index méretét.
   + **Szűrhetővé** teszi, hogy a mező hivatkozhat a szűrési kifejezésekben. A **$Filter** kifejezésben használt összes mezőnek rendelkeznie kell ezzel az attribútummal. A szűrési kifejezések pontos egyezéseket biztosítanak. Mivel a szöveges karakterláncok érintetlenek maradnak, további tárterületre van szükség a Verbatim-tartalom befogadásához.
   + A kereshetővé teszi a teljes szöveges keresést. Az ingyenes űrlapos lekérdezésekben vagy a lekérdezési kifejezésekben használt összes mezőnek rendelkeznie kell ezzel az attribútummal. A rendszer fordított indexeket hoz létre minden Kereshetőként megjelölt mezőhöz.

1. Igény szerint állítsa be a következő attribútumot:

   + A rendezhető beállítás lehetővé teszi, hogy a mező egy sorba legyen használva. Az **$OrderBy** -kifejezésben használt összes mezőnek rendelkeznie kell ezzel az attribútummal.
   + A többtényezős Navigálás lehetővé teszi a mező számára a sokoldalú navigációt. Csak a szűrőként megjelölt mezők jelölhetők meg.

1. Ha többnyelvű indexelést és lekérdezést szeretne használni, állítson be egy elemzőt. Az alapértelmezett érték a *standard Lucene* , de Ön a *Microsoft angol nyelvét* is kiválaszthatja, ha a Microsoft Analyzert speciális lexikális feldolgozási folyamatra kívánja használni, például a szabálytalan főnév és a művelet-űrlapok feloldására.

   + Válassza a **kereshető** lehetőséget az **analizátorok** listájának engedélyezéséhez.
   + Válassza ki a listában megadott elemzőt. 
   
   Ekkor csak a nyelvi elemzőket adhatja meg. Egyéni elemző vagy nem nyelvi elemző, például kulcsszó, minta és hasonló elemek használata esetén szükség van a kódra. További információ az elemzők használatáról: [index létrehozása több nyelven elérhető dokumentumokhoz](search-language-support.md).

1. A jelölőnégyzet bejelölésével engedélyezheti a típus-előre megadott lekérdezési javaslatokat a kiválasztott mezőkön.


## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat nyújtanak az indexelőkről:

* [Az Azure SQL Database indexelése](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Az Azure Cosmos DB indexelése](search-howto-index-cosmosdb.md)
* [A Blob Storage indexelése](search-howto-indexing-azure-blob-storage.md)
* [A Table Storage indexelése](search-howto-indexing-azure-tables.md)