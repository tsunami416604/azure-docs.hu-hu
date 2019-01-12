---
title: Adatok importálása az Azure Portalon – Azure Search keresési indexbe
description: Útmutató az Azure Portalon az adatok importálása varázsló segítségével bejárhatja az adatokat az Azure Cosmos DB, Blob storage, table storage, SQL Database és SQL Server Azure virtuális gépeken.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ee1b2a40dbcbd53a758ac71f30401778ef07e872
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229757"
---
# <a name="import-data-wizard-for-azure-search"></a>Az Azure Search adatok importálása varázsló

Az Azure portál Azure Search irányítópultján található **Adatok importálása** varázsló segítségével az adatok betölthetők egy indexbe. A színfalak mögött a varázsló konfigurál és meghív egy *adatforrás*, *index*, és *indexelő* -automatizálja az indexelési folyamat több lépését: 

* Az Azure-előfizetéshez egy külső adatforráshoz csatlakozik.
* Szükség esetén integrálható optikai karakterfelismerés vagy a természetes nyelvi feldolgozási szöveg beolvasása a strukturálatlan adatok számára.
* Az index, adat-mintavételezés és azok metaadatait a külső adatforrás alapján hoz létre.
* Feltérképezi az adatforrást, kereshető tartalom szerializálásához és JSON-dokumentumok betöltése az indexbe.

A varázsló nem lehet csatlakozni egy előre meghatározott indexre vagy egy meglévő indexelő futtatása, de a varázslóban be lehet állítani egy új index vagy indexelő struktúra és viselkedések kell támogatásához.

Mik az Azure Search újdonságai? Végighaladhat a [a rövid útmutató: Importálása, index és lekérdezés a portál eszközei használatával](search-get-started-portal.md) importálása és az indexelő használatával próbálhatják ki őket **adatimportálás** és a beépített realestate-minta adatkészlet.

## <a name="start-importing-data"></a>Indítsa el az adatok importálása

Ez a szakasz azt ismerteti, hogyan indítsa el a varázslót, és magas szintű áttekintést nyújt az egyes lépések.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a search szolgáltatás oldalát az irányítópultról vagy [keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatások listájában.

2. A szolgáltatás Áttekintés lap tetején kattintson **adatimportálás**.

   ![Adatok importálása parancs portálon](./media/search-import-data-portal/import-data-cmd2.png "indítsa el az adatok importálása varázsló")

   > [!NOTE]
   > Már el is indíthatja **adatimportálás** a más Azure-szolgáltatások, többek között az Azure Cosmos DB, az Azure SQL Database és az Azure Blob storage. Keressen **Azure Search hozzáadása** a áttekintés oldalát a bal oldali navigációs ablaktáblán.

3. Ekkor megnyílik a varázsló **csatlakozás az adatokhoz**, ahol kiválaszthatja az importáláshoz használandó külső adatforráshoz. Számos ebben a lépésben ismertek, ezért mindenképpen olvassa el a [adatforrás-bemenetek](#data-source-inputs) további részleteket a következő szakaszban.

   ![Adatok importálása varázsló portálon](./media/search-import-data-portal/import-data-wizard-startup.png "Azure Search adatok importálása varázsló")

4. Következő **hozzáadása a cognitive search**, abban az esetben, ha fel szeretne venni az optikai karakterfelismerés (OCR) szöveg képfájlok vagy szövegelemzés strukturálatlan adatokon. AI-algoritmusokat a Cognitive Services rendszer lekért erre a célra. Ebben a lépésben két részből áll:
  
   Először [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) , egy Azure Search-képességek alkalmazási lehetőségét.
  
   Másodszor válassza ki a melyik AI végrehajtott információbeolvasás készségeitől szerepeljenek. Lásd: Ez a forgatókönyv bemutatásához [rövid](cognitive-search-quickstart-blob.md).

   Ha csak át szeretné importálni az adatokat, kihagyhatja ezt a lépést, és lépjen közvetlenül az index definícióját.

5. Következő **célindex testreszabása**, ahol Ön is fogadja el vagy módosítsa az indexséma bemutatni a varázslóban. A varázsló kikövetkezteti a mezők és adattípusok adatok mintavételezésének és a külső adatforrásból metaadatait.

   Az egyes mezőkhöz tartozó [ellenőrizze az index attribútumainak](#index-definition) bizonyos viselkedéseinek engedélyezéséhez. Ha további attribútumokat ne válassza ki, az index nem lesz használható. 

6. Következő **hozzon létre egy indexelőt**, amely olyan, a varázsló. Az indexelő egy olyan webbejáró, amely kinyeri a kereshető adatok és metaadatok egy külső Azure adatforrásból. Jelölje ki az adatforrást, és csatolja (nem kötelező) szakértelmével és a egy index, már lett konfigurál egy indexelőt, hogy a varázsló lépéseit.

   Adja meg az indexelő nevét, és kattintson a **küldés** az importálási folyamat elindításához. 

Nyomon követheti a portálon kattintson az indexelő az indexelés a **indexelők** listája. A dokumentumok betöltésével a meghatározott index dokumentumszámlálója növekszik. Néha beletelik néhány percbe, mire a portál oldala tükrözi a legfrissebb adatokat.

Az index készen áll a lekérdezésre, amint az első dokumentum betöltése. Használhat [keresési ablak](search-explorer.md) erre a célra.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Forrás típusú bemenetek

A **adatimportálás** varázsló létrehoz egy állandó adatforrás-objektum megadása egy külső adatforráshoz való kapcsolódáshoz szükséges adatokat. Az adatforrás-objektum kizárólag a szolgál [indexelők](search-indexer-overview.md) és a következő adatforrások hozható létre: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Az Azure Table Storage](search-howto-indexing-azure-tables.md) (nem támogatott [cognitive search](cognitive-search-concept-intro.md) folyamatok)

Egybesimított adatkészlet a kötelező bemenet. Az importálás kizárólag egyedi táblából, adatbázisnézetből és egyenértékű adatszerkezetből végezhető el. Az adatszerkezetet a varázsló futtatása előtt létre kell hoznia.

|  Kiválasztás | Leírás |
| ---------- | ----------- |
| **Meglévő adatforrás** |Ha a Search-szolgáltatásban már meg vannak adva indexelők, másik importálási folyamathoz kiválaszthat egy meglévő adatforrás-definíciót. Az Azure Search indexelők adatforrás-objektumok csak segítségével. Programozott módon létrehozhat egy adatforrás-objektum vagy a **adatimportálás** varázsló.|
| **Példák**| Az Azure Search egy ingyenes nyilvános Azure SQL database segítségével ismerje meg az Azure Search importálása és a lekérdezési kérelmek üzemelteti. Lásd: [a rövid útmutató: Importálása, index és lekérdezés a portál eszközei használatával](search-get-started-portal.md) bemutató. |
| **Azure SQL Database** |A szolgáltatás neve, az olvasási engedéllyel rendelkező adatbázis-felhasználó hitelesítő adatai, valamint az adatbázis neve megadható vagy az oldalon, vagy az ADO.NET kapcsolati sztring használatával. A tulajdonságok megtekintéséhez vagy testreszabásához válassza ki a kapcsolati sztring lehetőséget. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| **Azure virtuális gépen futó SQL Server** |Adjon meg egy teljesen minősített szolgáltatásnevet, Felhasználóazonosítót és jelszót és adatbázis kapcsolati karakterláncként. Az adatforrás használatához a helyi tárolóban rendelkeznie kell egy korábban telepített tanúsítvánnyal, amely titkosítja a kapcsolatot. Részletes útmutatásért lásd az [SQL virtuális gép az Azure Search-höz történő csatlakoztatását](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) ismertető témakört. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| **Azure Cosmos DB** |A követelmények a következőket tartalmazzák: a fiók, az adatbázis és a gyűjtemény. Az index tartalmazza a gyűjteményben szereplő összes dokumentumot. Definiálhat egy lekérdezést a sorkészlet egybesimításához vagy szűréséhez, illetve a módosított dokumentumok észleléséhez a későbbi adatfrissítési műveletek elvégzése érdekében. |
| **Azure Blob Storage** |A követelmények a következőket tartalmazzák: a tárolási fiók és egy tároló. Szükség esetén, ha csoportosítási célból a blob-nevek egy virtuális elnevezési konvenciót követnek, akkor a név virtuáliskönyvtár-részét a tárolóban található mappa neveként is megadhatja. A további információkat [a Blob Storage indexelését](search-howto-indexing-azure-blob-storage.md) ismertető témakör tartalmazza. |
| **Azure Table Storage** |A követelmények a következőket tartalmazzák: a tárolási fiók és egy táblanév. Szükség esetén megadhat egy lekérdezést a táblák részhalmazának beolvasásához. A további információkat [a Table Storage indexelését](search-howto-indexing-azure-tables.md) ismertető témakör tartalmazza. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Index attribútumainak

A **adatimportálás** varázsló hoz létre az indexet, amely tölti fel a bemeneti adatok forrásból kapott dokumentumokat. 

A egy működési index ellenőrizze, van definiálva a következő elemeket.

1. Egy mezőt kell megjelölni egy **kulcs**, amely egyes dokumentumok egyedi azonosítására szolgál. A **kulcs** kell *Edm.string*. 

   Mezőértékek szóközöket vagy kötőjeleket tartalmaznak, be kell állítani a **Base-64 kódolása kulcs** beállítást a **hozzon létre egy indexelőt** lépést, a **speciális beállítások**, hogy letiltsa a Ezek a karakterek érvényességi ellenőrzését.

1. Állítsa be az egyes mezőkhöz tartozó indexattribútumokat. Ha nincsenek attribútumai, az index lényegében üres, kivéve a szükséges kulcsmező. Minimális válasszon egy vagy több ezeket az egyes mezőkhöz tartozó attribútumokat.
   
   + **Lekérhető** visszaadja a mező a keresési eredmények között. Minden mezőt, amely biztosítja a tartalmat a keresési eredmények kell rendelkeznie ehhez az attribútumhoz. Ebben a mezőben beállítása nincs számottevő hatással a index mérete.
   + **Szűrhető** lehetővé teszi, hogy a mezőt a szűrőkifejezésekben. Minden mezőt használja egy **$filter** ennek az attribútumnak kell tartoznia. Pontos egyezés szűrőkifejezésekben vonatkoznak. Szöveges karakterláncok változatlanok maradnak, mert további tárhely szükséges a szó tartalom megfeleljen.
   + **Kereshető** lehetővé teszi a teljes szöveges keresést. Minden mezőt szabad formátumú lekérdezések vagy a lekérdezési kifejezéseket használni ezt az attribútumot kell rendelkeznie. Fordított indexek jönnek létre az egyes mezőkhöz az egyenértékűként megjelölt **kereshető**.

1. Beállíthatja ezeket attribútum igény szerint:

   + **Rendezhető** segítségével az adott mezőt használhatjuk egy rendezés alapjaként. Minden mezőt használja egy **$Orderby** ennek az attribútumnak kell tartoznia.
   + **Kategorizálható** lehetővé teszi, hogy a mező, a jellemzőalapú navigáció. Csak a mezők is megjelölve **Filterable** megjelölhető **kategorizálható**.

1. Állítsa be egy **Analyzer** Ha azt szeretné, hogy a nyelvi fokozott indexelése és lekérdezéséhez. Az alapértelmezett érték *Standard Lucene* is választhat, de *Microsoft English* speciális lexikai feldolgozásra, például a szabálytalan főnevet és művelet űrlapok megoldása a Microsoft analyzer használatának szeretné.

   + Válassza ki **kereshető** engedélyezéséhez a **Analyzer** listája.
   + Válassza ki a listában megadott egy elemzőt. 
   
   Ekkor csak a nyelvi elemzőket adhatja meg. Egyéni elemző vagy nem nyelvi elemző, például kulcsszó, minta és hasonló elemek használata esetén szükség van a kódra. Elemzőkkel kapcsolatos további információkért lásd: [több nyelven elérhető dokumentumok indexének létrehozása](search-language-support.md).

1. Válassza ki a **javaslattevő** be a jelölőnégyzetet a gépelés közbeni lekérdezési javaslatok engedélyezéséhez a kijelölt mezők.


## <a name="next-steps"></a>További lépések
Az alábbi hivatkozások további információkat nyújtanak az indexelőkről:

* [Az Azure SQL Database indexelése](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Az Azure Cosmos DB indexelése](search-howto-index-cosmosdb.md)
* [A Blob Storage indexelése](search-howto-indexing-azure-blob-storage.md)
* [A Table Storage indexelése](search-howto-indexing-azure-tables.md)