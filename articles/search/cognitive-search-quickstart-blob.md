---
title: 'Rövid útmutató: Szakértelem létrehozása az Azure Portalon'
titleSuffix: Azure Cognitive Search
description: Ezen a portálon rövid útmutató, megtudhatja, hogyan használhatja az adatok importálása varázsló kognitív képességek hozzáadása egy indexelési folyamat az Azure Cognitive Search. A készségek közé tartozik az optikai karakterfelismerés (OCR) és a természetes nyelvi feldolgozás.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472417"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy Azure Cognitive Search kognitív képességek et az Azure Portalon

A skillset egy AI-szolgáltatás, amely kinyeri az információkat és a szerkezet nagy differenciálatlan szöveges vagy képfájlokat, és teszi indexelhető és kereshető a teljes szöveges keresési lekérdezések az Azure Cognitive Search. 

Ebben a rövid útmutatóban az Azure-felhőben lévő szolgáltatásokat és adatokat kombinálva hozza létre a skillsetet. Ha minden a helyén van, futtatja az **Adatok importálása** varázslót a portálon, hogy összehúzza az egészet. A végeredmény egy kereshető index, amely et a ai-feldolgozás által létrehozott adatokkal töltik fel, amelyeket lekérdezhet a portálon ([Search Explorer](search-explorer.md)).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és adatok betöltése

Ez a rövid útmutató az Azure Cognitive Search, [az Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/)és az Azure Cognitive [Services](https://azure.microsoft.com/services/cognitive-services/) a ai. 

Mivel a számítási feladatok olyan kicsi, a Cognitive Services a színfalak mögött, hogy ingyenes feldolgozás tegyék lebonyolításra legfeljebb 20 tranzakció. Egy ilyen kis adatkészlet, kihagyhatja a Cognitive Services-erőforrás létrehozása vagy csatolása.

1. [Töltsön le mintaadatokat](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), amelyek különböző típusú fájlok kis készletéből állnak. Csomagolja ki a fájlokat.

1. [Hozzon létre egy Azure-tárfiókot,](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) vagy [keressen egy meglévő fiókot.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 

   Válassza ki ugyanazt a régiót, mint az Azure Cognitive Search sávszélesség-díjak elkerülése érdekében. 
   
   Válassza ki a StorageV2 (általános célú V2) fiók típusát, ha később egy másik forgatókönyvben szeretné kipróbálni a tudástároló funkciót. Ellenkező esetben válasszon bármilyen típust.

1. Nyissa meg a Blob-szolgáltatások lapjait, és hozzon létre egy tárolót. Használhatja az alapértelmezett nyilvános hozzáférési szintet. 

1. A tárolóban kattintson a **Feltöltés gombra** az első lépésben letöltött mintafájlok feltöltéséhez. Figyelje meg, hogy a tartalomtípusok széles skálájával rendelkezik, beleértve azokat a képeket és alkalmazásfájlokat is, amelyek nem kereshetők teljes szövegben a natív formátumukban.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Hozzon létre egy Azure Cognitive Search szolgáltatást,](search-create-service-portal.md) vagy [keressen egy meglévő szolgáltatást.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) A rövid útmutatóhoz ingyenes szolgáltatást használhat.

Most már készen áll az Adatok importálása varázslóra való áttérésre.

## <a name="run-the-import-data-wizard"></a>Az Adatok importálása varázsló futtatása

A keresési szolgáltatás áttekintése lapon kattintson az **Adatok importálása** a parancssávon a kognitív dúsítás négy lépésben történő beállításához.

  ![Adatok importálása parancs](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>1. lépés – Adatforrás létrehozása

1. A **Csatlakozás az adatokhoz**lehetőséget **az Azure Blob storage**, válassza ki a storage-fiók és a létrehozott tároló. Adjon nevet az adatforrásnak, a többi beállításnál pedig használja az alapértelmezett értékeket. 

   ![Azure Blob-konfiguráció](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Folytassa a következő lappal.

### <a name="step-2---add-cognitive-skills"></a>2. lépés - Kognitív képességek hozzáadása

Ezután konfigurálja az AI-dúsítást az OCR, a képelemzés és a természetes nyelvi feldolgozás meghívására. 

1. Ehhez a rövid útmutatóhoz az **ingyenes** cognitive services erőforrást használjuk. A mintaadatok 14 fájlból állnak, így a Cognitive Services 20 tranzakcióingyenes lekötése elegendő ehhez a rövid útmutatóhoz. 

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Bontsa ki **a Hozzáadás idílemények csomópontot,** és négy kijelölést tehet ki. 

   Az OCR engedélyezése képelemzési készségek hozzáadásához a varázsló laphoz.

   Állítsa be a részletességet az Oldalak beállításhoz, hogy a szöveget kisebb adattömbökre bontsa. Számos szövegismerete5 KB bemenetre korlátozódik.

   Válassza ki az entitásfelismerést (személyek, szervezetek, helyek) és a képelemzési készségeket.

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/skillset.png)

   Folytassa a következő lappal.

### <a name="step-3---configure-the-index"></a>3. lépés - Az index konfigurálása

Az index tartalmazza a kereshető tartalmat, és az **Adatok importálása** varázsló általában létrehozhatja a sémát az adatforrás mintavételezésével. Ebben a lépésben tekintse át a létrehozott sémát, és potenciálisan vizsgálja felül a beállításokat. Az alábbiakban a demo Blob-adatkészlethez létrehozott alapértelmezett séma látható.

Ebben a rövid útmutatóban a varázsló észszerű alapértelmezett beállításokat határoz meg:  

+ Az alapértelmezett mezők a meglévő blobok tulajdonságain és a dúsítási kimenetet tartalmazó új mezőkön alapulnak (például `people`, `organizations`, `locations`). Az adattípusok metaadatokból és adatmintavételezésből következnek.

+ Az alapértelmezett dokumentumkulcs *metadata_storage_path* (mert a mező egyedi értékeket tartalmaz).

+ Az alapértelmezett attribútumok **a beolvasható** és **a kereshető**. **A kereshető** lehetővé teszi a teljes szöveges keresést egy mezőben. **A visszakereshető** azt jelenti, hogy az eredmények ben mezőértékek adhatók vissza. A varázsló feltételezi, hogy ezeket a mezőket visszakereshetőnek és kereshetőnek szeretné tekinteni, mert egy skillset segítségével hozta létre őket.

  ![Indexmezők](media/cognitive-search-quickstart-blob/index-fields.png)

Figyelje meg az áthúzást és a `content` kérdőjelet a **mező által visszakereshető** attribútumon. A szöveg-nehéz blob `content` dokumentumok, a mező tartalmazza a fájl nagy részét, potenciálisan futó több ezer sorok. Egy ilyen mező nehézkes a keresési eredmények között, ezért ki kell zárnia a bemutatót. 

Ha azonban át kell adnia a fájl tartalmát az ügyfélkódnak, győződjön meg arról, hogy **a Visszakereshető** továbbra is ki van jelölve. Ellenkező esetben fontolja meg `content` az attribútum törlését, `people`ha `organizations` `locations`a kinyert elemek (például , , , stb.

A mező **visszakereshetőként** való megjelölése nem jelenti azt, hogy a *mezőnek* jelen kell lennie a keresési eredmények között. A keresési eredmények összetételét pontosan szabályozhatja a **$select** lekérdezési paraméter rel, hogy megadja, mely mezőket kell szerepelni. Az olyan szöveggel `content`nehéz mezők esetében, mint **a** $select paraméter a megoldás az alkalmazás emberi felhasználói számára kezelhető keresési eredmények biztosítására, miközben biztosítja, hogy az ügyfélkód minden szükséges információhoz hozzáférjen a **Visszaérhető** attribútumon keresztül.
  
Folytassa a következő lappal.

### <a name="step-4---configure-the-indexer"></a>4. lépés - Az indexelő konfigurálása

Az indexelő az indexelési folyamatot irányító magas szintű erőforrás. Megadja az adatforrás nevét, a célindexet és a végrehajtás gyakoriságát. Az **Adatok importálása** varázsló több objektumot hoz létre, és ezek közül mindig olyan indexelő, amelyet többször futtathat.

1. Az **Indexelő** lapon elfogadhatja az alapértelmezett nevet, és az **Egyszeri** ütemezés lehetőségre kattintva azonnal futtathatja. 

   ![Az indexelő definíciója](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Kattintson **a Küldés** gombra az indexelő létrehozásához és egyidejű futtatásához.

## <a name="monitor-status"></a>Figyelő állapota

Kognitív képességek indexelés hosszabb időt vesz igénybe, mint a tipikus szöveg-alapú indexelés, különösen OCR és képelemzés. A folyamat figyeléséhez lépjen az Áttekintés lapra, és kattintson az Oldal közepén az **Indexelők** elemre.

  ![Az Azure Cognitive Search értesítése](./media/cognitive-search-quickstart-blob/indexer-notification.png)

A figyelmeztetések a tartalomtípusok széles skálája miatt normálisak. Egyes tartalomtípusok nem érvényesek bizonyos képességekre, és az alacsonyabb szinteken közös az [indexelő korlátokkal való találkozáshoz.](search-limits-quotas-capacity.md#indexer-limits) Például a 32 000 karakteres csonkolási értesítések az ingyenes szint indexelőkorlátja. Ha ezt a bemutatót egy magasabb szinten futtatja, sok csonkolási figyelmeztetés elmenne.

A figyelmeztetések vagy hibák ellenőrzéséhez kattintson a Figyelmeztetés állapotra az Indexelők listában a Végrehajtási előzmények lap megnyitásához.

Ezen a lapon kattintson ismét a Figyelmeztetés állapota gombra az alábbihoz hasonló figyelmeztetések listájának megtekintéséhez. 

  ![Indexelő figyelmeztetési listája](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

A részletek akkor jelennek meg, ha egy adott állapotsorra kattint. Ez a figyelmeztetés azt mondja, hogy az egyesítés leállt a maximális küszöbérték elérése után (ez a PDF nagy).

  ![Figyelmeztetés részletei](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Lekérdezés a Keresési ablakban

Az index létrehozása után lekérdezéseket futtathat az eredmények visszaadásához. A portálon használja a **Kereséskezelőt** ehhez a feladathoz. 

1. A keresési szolgáltatás Irányítópult lapján kattintson a parancssávon található **Keresési ablak** elemre.

1. A létrehozott index kiválasztásához kattintson az **Index módosítása** lehetőségre az oldal tetején.

1. Írjon be egy keresési karakterláncot `search=Microsoft&$select=people,organizations,locations,imageTags`az index lekérdezéséhez, például .

Az eredmények JSON-ként kerülnek visszaadásra, amely részletes és nehezen olvasható lehet, különösen az Azure-blobokból származó nagy dokumentumokban. Néhány tipp a keresés ebben az eszközben a következő technikákat:

+ Hozzáfűzés `$select` az eredményekben szerepeljen mezők meghatározásához. 
+ A CTRL-F billentyűkombinációval a JSON-on belül kereshet bizonyos tulajdonságokat vagy kifejezéseket.

A lekérdezési karakterláncok nem veszik figyelembe a kis- és nagybetűket, ezért ha "ismeretlen mező" üzenetet kap, ellenőrizze **a Mezők** vagy **a Tárgymutató-definíció (JSON)** című üzenetet a név és a kis- és nagybetű ellenőrzéséhez. 

  ![Keresési ablak – példa](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Legfontosabb ismeretek

Most már létrehozta az első skillset és megtanulta a fontos fogalmakat hasznos prototípus egy bővített keresési megoldás segítségével a saját adatait.

Az általunk közvetíteni kívánt legfontosabb alapelvek egyike az Azure-adatforrásoktól való függőség. A skillset van kötve egy indexelő, és az indexelők az Azure és a forrás-specifikus. Bár ez a rövid útmutató az Azure Blob Storage-ot használja, más Azure-adatforrások használata is lehetséges. További információ: [Indexelők az Azure Cognitive Search.](search-indexer-overview.md) 

Egy másik fontos fogalom az, hogy a szakértelem a tartalomtípusokon keresztül működik, és amikor heterogén tartalommal dolgozik, egyes bemenetek kimaradnak. Emellett a nagy fájlok vagy mezők is meghaladhatják a szolgáltatási szint indexelő korlátait. Normális, ha figyelmeztetéseket látunk, amikor ezek az események bekövetkeznek. 

A kimenet egy keresési indexre irányul, és az indexelés során létrehozott név-érték párok és az index egyes mezői között van leképezés. Belsőleg a portál [jegyzeteket](cognitive-search-concept-annotations-syntax.md) állít be és definiál egy [képességcsoportot](cognitive-search-defining-skillset.md), amellyel megalapozza a műveletek sorrendjét és az általános folyamatot. Ezeket a lépéseket a rendszer elrejti a portálon, ám az alapelvek ismerete fontos, amikor hozzálát a kód írásához.

Végül megtanulta, hogy az index lekérdezésével ellenőrizheti a tartalmat. Végül az Azure Cognitive Search egy kereshető indexet biztosít, amelyet lekérdezhet az [egyszerű](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vagy [a teljes mértékben kiterjesztett lekérdezésszintaxisával.](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) A bővített mezőket tartalmazó index ugyanúgy működik, akár az összes többi. Ha azt szeretné, hogy bele szabványos vagy [egyéni analizátorok,](search-analyzers.md) [pontozási profilok,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) [szinonimák,](search-synonyms.md) [jellemzőszűrők,](search-filters-facets.md)geo-keresés, vagy bármely más Azure Cognitive Search funkció, akkor biztosan megteheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

Az erőforrásokat a portálon keresheti meg és kezelheti a bal oldali navigációs ablak **Minden erőforrás** vagy **Erőforráscsoport** hivatkozásával.

Ha ingyenes szolgáltatást használ, ne feledje, hogy három indexelésre, indexelőre és adatforrásra van korlátozva. Törölheti az egyes elemeket a portálon, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

A portálon, a .NET SDK-n vagy a REST API-n keresztül hozhat létre skillseteket. A további ismeretek, próbálja ki a REST API postman és több mintaadatokat.

> [!div class="nextstepaction"]
> [Oktatóanyag: Szöveg és struktúra kinyerése JSON-blobokból REST API-k használatával](cognitive-search-tutorial-blob.md)

> [!Tip]
> Ha meg szeretné ismételni ezt a gyakorlatot, vagy ki próbál egy másik AI-bővítési forgatókönyvet, törölje az indexelőt a portálon. Az indexelő törlése visszaállítja az ingyenes napi tranzakciószámláló t nullára a Cognitive Services feldolgozása.