---
title: 'Gyors útmutató: készségkészlet létrehozása a Azure Portalban'
titleSuffix: Azure Cognitive Search
description: Ez a portál rövid útmutató bemutatja, hogyan adhat hozzá kognitív képességeket az Azure Cognitive Search indexelési folyamatához az adatimportálás varázsló segítségével. A képességek közé tartozik az optikai karakterfelismerés (OCR) és a természetes nyelvi feldolgozás.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/20/2019
ms.openlocfilehash: e2e17ba6af60fa495a03e7d46a07cfe6b66f4e68
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77472417"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Gyors útmutató: Azure Cognitive Search kognitív készségkészlet létrehozása a Azure Portal

A készségkészlet olyan AI-szolgáltatás, amely a nagyméretű, nem differenciált szöveg-vagy képfájlokból származó információkat és struktúrát kinyeri, és indexelhető és kereshetővé teszi az Azure Cognitive Search teljes szöveges keresési lekérdezéseit. 

Ebben a rövid útmutatóban a készségkészlet létrehozásához egyesíti az Azure-felhőben lévő szolgáltatásokat és az adatszolgáltatásokat. Ha minden megtörtént, a portálon futtathatja az **adatimportálás** varázslót, hogy az összeset együtt kell lekérnie. A végeredmény egy olyan kereshető index, amely az AI-feldolgozás által létrehozott adatokkal van feltöltve, amelyet a portálon ([Search Explorer](search-explorer.md)) lehet lekérdezni.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és az adatterhelés

Ez a rövid útmutató az Azure Cognitive Search, az [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/)és az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) használatát használja az AI-hoz. 

Mivel a számítási feladatok olyan kicsik, Cognitive Services a jelenetek mögött, hogy akár 20 tranzakciót is biztosítson az ingyenes feldolgozáshoz. Ilyen kis adathalmaz esetén kihagyhatja Cognitive Services erőforrás létrehozását vagy csatolását.

1. [Töltsön le mintaadatokat](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), amelyek különböző típusú fájlok kis készletéből állnak. Bontsa ki a fájlokat.

1. [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Válassza ki ugyanazt a régiót, mint az Azure Cognitive Search a sávszélességgel kapcsolatos költségek elkerülése érdekében. 
   
   Ha később szeretné kipróbálni a Knowledge Store szolgáltatást, akkor válassza a StorageV2 (általános célú v2) fiók típusát, egy másik útmutatóban. Egyéb esetben válassza a tetszőleges típust.

1. Nyissa meg a blob Services-lapokat, és hozzon létre egy tárolót. Használhatja az alapértelmezett nyilvános hozzáférési szintet. 

1. A tárolóban kattintson a **feltöltés** gombra, és töltse fel az első lépésben letöltött minta fájlokat. Figyelje meg, hogy számos tartalomtípust tartalmaz, beleértve a képeket és az alkalmazás fájljait, amelyek nem teljes szöveges kereshetők a natív formátumokban.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

Most már készen áll az adatimportálás varázsló áthelyezésére.

## <a name="run-the-import-data-wizard"></a>Az adatimportálás varázsló futtatása

A keresési szolgáltatás áttekintése lapon kattintson az **adatimportálás** parancsra a parancssávban a kognitív dúsítás beállításához négy lépésben.

  ![Adatok importálása parancs](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1---create-a-data-source"></a>1. lépés – adatforrás létrehozása

1. A **Kapcsolódás az adataihoz**lapon válassza az **Azure Blob Storage**lehetőséget, majd válassza ki a létrehozott Storage-fiókot és-tárolót. Adjon nevet az adatforrásnak, a többi beállításnál pedig használja az alapértelmezett értékeket. 

   ![Azure Blob-konfiguráció](./media/cognitive-search-quickstart-blob/blob-datasource.png)

    Folytassa a következő oldallal.

### <a name="step-2---add-cognitive-skills"></a>2. lépés – kognitív képességek hozzáadása

Ezután konfigurálja az AI-bővítést az OCR, a képelemzés és a természetes nyelvi feldolgozás meghívásához. 

1. Ebben a rövid útmutatóban az **ingyenes** Cognitive Services erőforrást használjuk. A mintaadatok 14 fájlból állnak, így az Cognitive Services 20 tranzakció ingyenes kiosztása elegendő ehhez a rövid útmutatóhoz. 

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Bontsa ki a bővítések **hozzáadása** lehetőséget, és adjon meg négy választ. 

   Képelemzési képességek hozzáadásának engedélyezése az OCR számára a varázsló oldalán.

   Állítsa be a részletességet lapokra a szöveg kisebb adattömbökbe való bontásához. Számos szöveges ismeret legfeljebb 5 KB-os bemenetre korlátozódik.

   Válassza az entitások felismerése (személyek, szervezetek, helyszínek) és a képelemzési képességek elemet.

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/skillset.png)

   Folytassa a következő oldallal.

### <a name="step-3---configure-the-index"></a>3. lépés – az index konfigurálása

Az index tartalmazza a kereshető tartalmat, az **adatimportálás** varázsló általában az adatforrás mintavételezésével hozza létre a sémát. Ebben a lépésben tekintse át a generált sémát, és esetleg módosítsa a beállításokat. Az alábbi alapértelmezett séma lett létrehozva a bemutató blob adatkészlethez.

Ebben a rövid útmutatóban a varázsló észszerű alapértelmezett beállításokat határoz meg:  

+ Az alapértelmezett mezők a meglévő Blobok tulajdonságain, valamint a dúsítási kimenetet tartalmazó új mezőkön alapulnak `organizations`( `locations`például `people`:,,). Az adattípusok a metaadatokból és az adatok mintavételezésével vannak kikövetkeztetve.

+ Az alapértelmezett dokumentum kulcsa *metadata_storage_path* (kiválasztva, mert a mező egyedi értékeket tartalmaz).

+ Az alapértelmezett attribútumok **megkereshetők és** **kereshetők**. A **kereshető** szöveg teljes szöveges keresést tesz lehetővé a mezőkben. Lekérhető **értékek: a** mezőértékek visszaadhatók az eredményekben. A varázsló feltételezi, hogy ezek a mezők lekérhető és kereshetők, mert egy készségkészlet keresztül hozta létre őket.

  ![Indexmezők](media/cognitive-search-quickstart-blob/index-fields.png)

Figyelje meg a **lekéréses** attribútum áthúzott és kérdőjel `content` értékét a mező alapján. Szöveg – nagy méretű blob-dokumentumok esetén `content` a mező a fájl nagy részét tartalmazza, amely akár több ezer sorra is futhat. Az ehhez hasonló mező nem a keresési eredményekben, hanem a bemutatóban kizárható. 

Ha azonban meg kell adnia a fájl tartalmát az ügyfél kódjához, győződjön meg arról, hogy a **beolvasható** marad lehetőség ki van választva. Ellenkező esetben érdemes lehet törölni ezt az `content` attribútumot, ha a kinyert `organizations`elemek `locations`(például `people`,, stb.) elegendőek.

A mezők **beolvasható** való megjelölése nem jelenti azt, hogy a mezőnek jelen *kell lennie* a keresési eredmények között. A keresési eredmények összetételét pontosan vezérelheti a **$Select** lekérdezési paraméterrel, hogy megadja, hogy mely mezők szerepeljenek hozzá. A Text-nagy méretű mezőkhöz hasonlóan `content`a **$Select** paraméter a felügyelhető keresési eredmények az alkalmazás emberi felhasználói számára való biztosítására szolgáló megoldás, miközben az ügyfél kódjának hozzáférése van az összes szükséges információhoz a lekérhető attribútum használatával. **Retrievable**
  
Folytassa a következő oldallal.

### <a name="step-4---configure-the-indexer"></a>4. lépés – az indexelő konfigurálása

Az indexelő az indexelési folyamatot irányító magas szintű erőforrás. Meghatározza az adatforrás nevét, a célként megadott indexet és a végrehajtás gyakoriságát. Az **adatimportálás** varázsló több objektumot hoz létre, és ezek mindig egy indexelő, amelyet többször is futtathat.

1. Az **Indexelő** lapon fogadja el az alapértelmezett nevet, majd kattintson az **egyszeres** ütemterv lehetőségre, hogy azonnal fusson. 

   ![Az indexelő definíciója](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Kattintson a **Submit (Küldés** ) gombra az indexelő létrehozásához és egyidejű futtatásához.

## <a name="monitor-status"></a>Figyelő állapota

A kognitív képességek indexelése hosszabb időt vesz igénybe, mint a szokásos szöveges indexelés, különösen az OCR és a képelemzés. A folyamat figyeléséhez nyissa meg az Áttekintés lapot, és kattintson a lap közepén található **Indexelő** elemre.

  ![Azure Cognitive Search értesítés](./media/cognitive-search-quickstart-blob/indexer-notification.png)

A figyelmeztetések a tartalomtípusok széles köre miatt normálisak. Egyes tartalomtípusok nem érvényesek bizonyos szaktudáshoz, és alacsonyabb szinten vannak, mint az [Indexelő korlátainak](search-limits-quotas-capacity.md#indexer-limits)megtapasztalása. A 32 000 karakterből álló csonkolt értesítések például az ingyenes szinten indexelő korlátot képeznek. Ha a bemutatót egy magasabb szintű szinten futtatta, sok csonkolt figyelmeztetés fog elindulni.

A figyelmeztetések és hibák vizsgálatához kattintson a figyelmeztetési állapotra az indexelő listában a végrehajtási Előzmények lap megnyitásához.

Ezen az oldalon kattintson ismét a figyelmeztetési állapot elemre az alább láthatóhoz hasonló figyelmeztetések listájának megtekintéséhez. 

  ![Indexelő figyelmeztetési lista](./media/cognitive-search-quickstart-blob/indexer-warnings.png)

A részletek akkor jelennek meg, amikor egy adott állapotjelző sorra kattint. Ez a figyelmeztetés azt mondja, hogy az egyesítés leállt a maximális küszöb elérése után (ez az adott PDF-fájl nagy).

  ![Figyelmeztetés részletei](./media/cognitive-search-quickstart-blob/warning-detail.png)

## <a name="query-in-search-explorer"></a>Lekérdezés a Keresési ablakban

Az index létrehozása után lekérdezéseket futtathat az eredmények visszaküldéséhez. A portálon használja a **keresési Explorert** ehhez a feladathoz. 

1. A keresési szolgáltatás Irányítópult lapján kattintson a parancssávon található **Keresési ablak** elemre.

1. A létrehozott index kiválasztásához kattintson az **Index módosítása** lehetőségre az oldal tetején.

1. Adja meg a keresési karakterláncot az index lekérdezéséhez, `search=Microsoft&$select=people,organizations,locations,imageTags`például:.

Az eredményeket JSON-ként adja vissza, amely részletes és nehezen olvasható, különösen az Azure-blobokból származó nagyméretű dokumentumokban. Néhány tipp az eszközön való kereséshez a következő technikákat tartalmazza:

+ Hozzáfűzéssel `$select` adja meg, hogy mely mezők szerepeljenek az eredmények között. 
+ A CTRL-F billentyűkombinációval keresheti meg az adott tulajdonságokat vagy kifejezéseket a JSON-n belül.

A lekérdezési karakterláncok megkülönböztetik a kis-és nagybetűket, így ha "ismeretlen mező" üzenetet kap, a név és az eset ellenőrzéséhez ellenőrizze a **mezők** vagy az **index definícióját (JSON)** . 

  ![Keresési ablak – példa](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Legfontosabb ismeretek

Most létrehozta az első készségkészlet, és fontos fogalmakat tanult meg, amelyek hasznosak lehetnek a saját adataival bővített keresési megoldások prototípusához.

Az általunk közvetíteni kívánt legfontosabb alapelvek egyike az Azure-adatforrásoktól való függőség. Egy készségkészlet egy indexelő van kötve, és az indexelő az Azure és a forrás-specifikus. Bár ez a rövid útmutató az Azure Blob Storage-ot használja, más Azure-adatforrások használata is lehetséges. További információ: [Indexelő az Azure Cognitive Searchban](search-indexer-overview.md). 

Egy másik fontos szempont, hogy a képességek többek között a tartalomtípusok és a heterogén tartalom használata esetén is kimaradnak. Emellett előfordulhat, hogy a nagyméretű fájlok vagy mezők túllépik a szolgáltatási szintek indexelő korlátait. A figyelmeztetések az események bekövetkezésekor normálisan jelennek meg. 

A kimenet egy keresési indexre van irányítva, és az indexelés során és az index egyes mezőiben létrehozott név-érték párok között van leképezés. Belsőleg a portál [jegyzeteket](cognitive-search-concept-annotations-syntax.md) állít be és definiál egy [képességcsoportot](cognitive-search-defining-skillset.md), amellyel megalapozza a műveletek sorrendjét és az általános folyamatot. Ezeket a lépéseket a rendszer elrejti a portálon, ám az alapelvek ismerete fontos, amikor hozzálát a kód írásához.

Végezetül megtanulta, hogy az index lekérdezésével ellenőrizheti a tartalmat. A végén az Azure Cognitive Search egy kereshető indexet tartalmaz, amelyet az [egyszerű](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vagy [teljes kibővített lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)használatával lehet lekérdezni. A bővített mezőket tartalmazó index ugyanúgy működik, akár az összes többi. Ha standard vagy [Egyéni elemzőket](search-analyzers.md), [pontozási profilokat](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [szinonimákat](search-synonyms.md), [sokoldalú szűrőket](search-filters-facets.md), Geo-keresést vagy bármely más Azure Cognitive Search funkciót szeretne beépíteni, akkor természetesen megteheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Szakértelmével a portál, a .NET SDK vagy a REST API használatával hozhat létre. Az ismeretek megismeréséhez próbálja ki a REST API a Poster és a több mintaadatok használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: a JSON-Blobok szövegének és szerkezetének kinyerése a REST API-k használatával](cognitive-search-tutorial-blob.md)

> [!Tip]
> Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje az indexelő a portálon. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját a Cognitive Services feldolgozáshoz.