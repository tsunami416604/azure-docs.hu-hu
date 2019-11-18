---
title: 'Gyors útmutató: készségkészlet létrehozása a Azure Portalban'
titleSuffix: Azure Cognitive Search
description: Az adatimportálás varázsló segítségével kognitív képességeket adhat hozzá egy indexelési folyamathoz. A kognitív képességek közé tartozik az optikai karakterfelismerés (OCR) és a természetes nyelvi feldolgozás.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 2280b718fe949384bb67b1b606ab143ddca8e077
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113349"
---
# <a name="quickstart-create-an-azure-cognitive-search-cognitive-skillset-in-the-azure-portal"></a>Gyors útmutató: Azure Cognitive Search kognitív készségkészlet létrehozása a Azure Portal

A készségkészlet olyan AI-szolgáltatás, amely a nagyméretű, nem differenciált szöveg-vagy képfájlokból származó információkat és struktúrát kinyeri, és indexelhető és kereshetővé teszi az Azure Cognitive Search teljes szöveges keresési lekérdezéseit. 

Ebben a rövid útmutatóban a készségkészlet létrehozásához egyesíti az Azure-felhőben lévő szolgáltatásokat és az adatszolgáltatásokat. Ha minden megtörtént, a portálon futtathatja az **adatimportálás** varázslót, hogy az összeset együtt kell lekérnie. A végeredmény egy olyan kereshető index, amely az AI-feldolgozás által létrehozott adatokkal van feltöltve, amelyet a portálon ([Search Explorer](search-explorer.md)) lehet lekérdezni.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és az adatterhelés

Ez a rövid útmutató az Azure Cognitive Search, az Azure Blob Storage és az [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) használatát használja az AI-hoz. 

Mivel a számítási feladatok olyan kicsik, Cognitive Services a háttérben, hogy naponta akár 20 tranzakciót is biztosítson az Azure-Cognitive Search. Az általunk megadott mintaadatok használata esetén kihagyhatja Cognitive Services erőforrás létrehozását vagy csatolását.

1. [Töltsön le mintaadatokat](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), amelyek különböző típusú fájlok kis készletéből állnak. Bontsa ki a fájlokat.

1. [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a jelenlegi előfizetése alatt. 

   Válassza ki ugyanazt a régiót, mint az Azure Cognitive Search. Ha később szeretné kipróbálni a Knowledge Store szolgáltatást, akkor válassza a StorageV2 (általános célú v2) fiók típusát, egy másik útmutatóban. Egyéb esetben válassza a tetszőleges típust.

1. Nyissa meg a blob Services-lapokat, és hozzon létre egy tárolót. Használhatja az alapértelmezett nyilvános hozzáférési szintet. 

1. A tárolóban kattintson a **feltöltés** gombra, és töltse fel az első lépésben letöltött minta fájlokat. Figyelje meg, hogy számos tartalomtípust tartalmaz, beleértve a képeket és az alkalmazás fájljait, amelyek nem teljes szöveges kereshetők a natív formátumokban.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

1. [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) ugyanahhoz az előfizetéshez. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

<!-- 1. You are almost done with this resource, but before you leave these pages, use a link on the left navigation pane to open the **Access Keys** page. In many tutorials, especially those that use the REST API, you will need a connection string to retrieve data from Blob storage. A connection string looks similar to the following example: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` -->

Most már készen áll az adatimportálás varázsló áthelyezésére.

## <a name="run-the-import-data-wizard"></a>Az adatimportálás varázsló futtatása

A keresési szolgáltatás áttekintése lapon kattintson az **adatimportálás** parancsra a parancssávban a kognitív dúsítás beállításához négy lépésben.

  ![Adatok importálása parancs](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1\. lépés: Adatforrás létrehozása

1. A **Kapcsolódás az adataihoz**lapon válassza az **Azure Blob Storage**lehetőséget, majd válassza ki a létrehozott Storage-fiókot és-tárolót. Adjon nevet az adatforrásnak, a többi beállításnál pedig használja az alapértelmezett értékeket. 

   ![Azure Blob-konfiguráció](./media/cognitive-search-quickstart-blob/blob-datasource.png)

1. Folytassa a következő oldallal.

### <a name="step-2-add-cognitive-skills"></a>2\. lépés: Kognitív képességek hozzáadása

Ezután vegyen fel kognitív képességeket természetes nyelvi feldolgozás meghívásához. A mintaadatok 12 fájlból állnak, így az Cognitive Services 20 tranzakció ingyenes kiosztása elegendő ehhez a rövid útmutatóhoz. Mivel nem használunk OCR-t, a folyamat csak a nem képképeket fogja megszámolni, megrepedt és használni.

1. Ebben a rövid útmutatóban az **ingyenes** Cognitive Services erőforrást használjuk.

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/cog-search-attach.png)

1. Bontsa ki a **szaktudás hozzáadása** lehetőséget, és válassza ki a természetes nyelvi feldolgozást végző szaktudást. Ebben a rövid útmutatóban válassza az entitások felismerése lehetőséget a személyekhez, szervezetekhez és helyekhez.

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/skillset.png)

1. Fogadja el az alapértelmezett forrás mezőt: `content`. Ez egy kis célpontnak tűnhet, de az Azure-Blobok esetében a `content` mező a blob-dokumentum (például egy Word Doc vagy PowerPoint-pakli) többségét tartalmazza, ami jó választást tesz lehetővé.

1. Folytassa a következő oldallal.

> [!NOTE]
> A természetes nyelvi feldolgozási képességek a mintaadatkészletbeli szöveges tartalmakon végeznek műveleteket. Mivel nem választottuk ki az OCR beállítást, a rendszer nem dolgozza fel a minta adatkészletben található JPEG-és PNG-fájlokat ebben a rövid útmutatóban. 

### <a name="step-3-configure-the-index"></a>3\. lépés: Az index konfigurálása

Az Azure Cognitive Searchban az index tartalmazza a kereshető tartalmat, és az **adatimportálás** varázsló általában az adatforrás mintavételezésével hozza létre a sémát. Ebben a lépésben tekintse át a generált sémát, és esetleg módosítsa a beállításokat. Az alábbi alapértelmezett séma lett létrehozva a bemutató blob adatkészlethez.

Ebben a rövid útmutatóban a varázsló észszerű alapértelmezett beállításokat határoz meg: 

+ Az alapértelmezett név a *azureblob-index* az adatforrás típusa alapján. 

+ Az alapértelmezett mezők az eredeti forrásoldali adatmezőn (`content`), valamint a kognitív képességek által létrehozott kimeneti mezőkön (`people`, `organizations`és `locations`) alapulnak. Az alapértelmezett adattípusok a metaadatok és az adatok mintavételezésére utalnak.

+ Az alapértelmezett dokumentum kulcsa *metadata_storage_path* (kiválasztva, mert a mező egyedi értékeket tartalmaz).

+ Az alapértelmezett attribútumok lekérhető és **kereshetők** ezekhez a mezőkhöz. **Kereshető** azt jelzi, hogy egy mező kereshető. A lekérdezhető érték **azt jelenti,** hogy visszaadható az eredmények között. A varázsló feltételezi, hogy ezek a mezők lekérhető és kereshetők, mert egy készségkészlet keresztül hozta létre őket.

  ![Indexmezők](media/cognitive-search-quickstart-blob/index-fields.png)

Figyelje meg a **lekéréses** attribútum áthúzott és kérdőjel értékét a `content` mező alapján. Szöveg – nagy méretű blob-dokumentumok esetén a `content` mező a fájl nagy részét tartalmazza, ami akár több ezer sorra is felhasználható. Ha meg kell adnia a fájl tartalmát az ügyfél kódjához, győződjön meg arról, hogy a **beolvasható** maradok lehetőség van kiválasztva. Ellenkező esetben érdemes lehet törölni ezt az attribútumot `content`, ha a kinyert elemek (`people`, `organizations`és `locations`) elegendőek a célra.

A mezők **beolvasható** való megjelölése nem jelenti azt, hogy a mezőnek jelen *kell lennie* a keresési eredmények között. A keresési eredmények összetételét pontosan vezérelheti a **$Select** lekérdezési paraméterrel, hogy megadja, hogy mely mezők szerepeljenek hozzá. A nagy méretű mezőkhöz, például a `content`hoz a **$Select** paraméter a felügyelhető keresési eredmények az alkalmazás emberi felhasználói számára való biztosítására szolgáló megoldás, **miközben az ügyfél** kódjának hozzáférése van az összes szükséges információhoz a lekérhető attribútumon keresztül.
  
Folytassa a következő oldallal.

### <a name="step-4-configure-the-indexer"></a>4\. lépés: Az indexelő konfigurálása

Az indexelő az indexelési folyamatot irányító magas szintű erőforrás. Meghatározza az adatforrás nevét, a célként megadott indexet és a végrehajtás gyakoriságát. Az **adatimportálás** varázsló több objektumot hoz létre, és ezek mindig egy indexelő, amelyet többször is futtathat.

1. Az **Indexelő** lapon fogadja el az alapértelmezett nevet, majd kattintson az **egyszeres** ütemterv lehetőségre, hogy azonnal fusson. 

   ![Az indexelő definíciója](media/cognitive-search-quickstart-blob/indexer-def.png)

1. Kattintson a **Submit (Küldés** ) gombra az indexelő létrehozásához és egyidejű futtatásához.

## <a name="monitor-status"></a>Figyelő állapota

A kognitív képességek indexelése hosszabb időt vesz igénybe, mint a szokásos szöveges indexelés. A folyamat figyeléséhez nyissa meg az Áttekintés lapot, és kattintson a lap közepén található **Indexelő** elemre.

A figyelmeztetés azért fordul elő, mert a JPG-és PNG-lemezképfájlok az adatforrásban vannak, és a folyamatból kihagyták az OCR-képességet. Emellett csonkolt értesítéseket is talál. A kinyerési érték legfeljebb 32 000 karakter hosszú lehet az ingyenes szinten.

  ![Azure Cognitive Search értesítés](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Az indexelés és a bővítés sok időt vehet igénybe, ezért a funkció kipróbálásához kisebb adatkészletek használatát javasoljuk. 

A Azure Portal az értesítési tevékenység naplóját is figyelheti egy kattintható **Azure Cognitive Search értesítési** állapot hivatkozására. A végrehajtás több percet is igénybe vehet.

## <a name="query-in-search-explorer"></a>Lekérdezés a Keresési ablakban

Miután az index létrejött, lekérdezéseket küldhet be, amelyek dokumentumokat adnak vissza az indexből. A portálon használja a **Keresési ablakot** a lekérdezések futtatásához és az eredmények megtekintéséhez. 

1. A keresési szolgáltatás Irányítópult lapján kattintson a parancssávon található **Keresési ablak** elemre.

1. A létrehozott index kiválasztásához kattintson az **Index módosítása** lehetőségre az oldal tetején.

1. Adja meg a keresési karakterláncot az index lekérdezéséhez, például `search=Microsoft&searchFields=Organizations`.

A rendszer JSON-formátumban adja vissza az eredményeket, amely különösen az Azure-blobokból származó nagyméretű dokumentumok esetén részletes és nehezen olvasható lehet. Ha az eredményeket nem lehet egyszerűen áttekinteni, a CTRL-F billentyűkombinációval kereshet a dokumentumokban. Ehhez a lekérdezéshez a JSON-ban megadott feltételek alapján kereshet. 

A CTRL-F billentyűkombináció abban is segítségére lehet, hogy meghatározza az egy adott eredményhalmazban szereplő dokumentumok számát. Az Azure-blobok esetében a portál a „metadata_storage_path” kulcsot választja, mert annak minden értéke egyedi a dokumentumban. A CTRL-F billentyűkombinációval keressen a „metadata_storage_path” kifejezésre, amely visszaadja a dokumentumok számát. 

  ![Keresési ablak – példa](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Legfontosabb ismeretek

Most létrehozta az első készségkészlet, és fontos fogalmakat tanult meg, amelyek hasznosak lehetnek a saját adataival bővített keresési megoldások prototípusához.

Az általunk közvetíteni kívánt legfontosabb alapelvek egyike az Azure-adatforrásoktól való függőség. Egy készségkészlet egy indexelő van kötve, és az indexelő az Azure és a forrás-specifikus. Bár ez a rövid útmutató az Azure Blob Storage-ot használja, más Azure-adatforrások használata is lehetséges. További információ: [Indexelő az Azure Cognitive Searchban](search-indexer-overview.md).

Egy másik fontos alapelv szerint a képességek a bemeneti mezőkön végeznek műveleteket. A portálon minden képességhez egyetlen forrásmezőt kell választania. A kódban a bemenetek lehetnek más mezők, vagy egy felsőbb rétegbeli képesség kimenetei.

A kimenet egy keresési indexre van irányítva, és az indexelés során és az index egyes mezőiben létrehozott név-érték párok között van leképezés. Belsőleg a portál [jegyzeteket](cognitive-search-concept-annotations-syntax.md) állít be és definiál egy [képességcsoportot](cognitive-search-defining-skillset.md), amellyel megalapozza a műveletek sorrendjét és az általános folyamatot. Ezeket a lépéseket a rendszer elrejti a portálon, ám az alapelvek ismerete fontos, amikor hozzálát a kód írásához.

Végezetül megtanulta, hogy az index lekérdezésével ellenőrizheti a tartalmat. A végén az Azure Cognitive Search egy kereshető indexet tartalmaz, amelyet az [egyszerű](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vagy [teljes kibővített lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)használatával lehet lekérdezni. A bővített mezőket tartalmazó index ugyanúgy működik, akár az összes többi. Ha standard vagy [Egyéni elemzőket](search-analyzers.md), [pontozási profilokat](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [szinonimákat](search-synonyms.md), [sokoldalú szűrőket](search-filters-facets.md), Geo-keresést vagy bármely más Azure Cognitive Search funkciót szeretne beépíteni, akkor természetesen megteheti.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

Szakértelmével a portál, a .NET SDK vagy a REST API használatával hozhat létre. Az ismeretek megismeréséhez próbálja ki a REST API a Poster és a több mintaadatok használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: struktúra hozzáadása strukturálatlan tartalomhoz mesterséges intelligenciával](cognitive-search-tutorial-blob.md)

> [!Tip]
> Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje az indexelő a portálon. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját a Cognitive Services feldolgozáshoz.