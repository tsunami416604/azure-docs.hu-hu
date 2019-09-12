---
title: 'Gyors útmutató: Ismerje meg a kognitív keresés AI-bővítését Azure Portal-Azure Search'
description: Adatok kinyerése, természetes nyelvi és képfeldolgozási képességek egy Azure Search indexelő portálon a Azure Portal és a mintaadatok használatával.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: heidist
ms.openlocfilehash: ff32be5410876d1ba61b3bce2fd5f57868745e50
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881088"
---
# <a name="quickstart-create-an-ai-enrichment-pipeline-using-cognitive-skills-in-azure-search"></a>Gyors útmutató: AI-dúsítási folyamat létrehozása a Azure Search kognitív képességeinek használatával

A Azure Search a [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), a tartalom kinyerését, a természetes nyelvi feldolgozást (NLP) és a képfeldolgozási képességeket egy Azure Search indexelési folyamatba integrálja, így a kereshető vagy strukturálatlan tartalmak kereshetők. 

Számos Cognitive Services erőforrás – például az [OCR](cognitive-search-skill-ocr.md), a [nyelvfelismerés](cognitive-search-skill-language-detection.md), az [entitások felismerése](cognitive-search-skill-entity-recognition.md) , hogy a név néhány – egy indexelési folyamathoz csatolható. A Cognitive Services AI-algoritmusai olyan minták, funkciók és tulajdonságok keresésére szolgálnak, amelyek a forrásadatok, a visszatérő struktúrák és a szöveges tartalom alapján használhatók, amelyek Azure Search alapján teljes szöveges keresési megoldásokban használhatók.

Ebben a rövid útmutatóban létrehozza az első alkoholtartalom-növelési folyamatát a [Azure Portalban](https://portal.azure.com) , mielőtt egyetlen sor kódot írna elő:

> [!div class="checklist"]
> * Kezdje az Azure Blob Storage mintaadataival
> * Az [**adatimportálás**](search-import-data-portal.md) varázsló konfigurálása a kognitív indexeléshez és a dúsításhoz 
> * Futtassa a varázslót (egy entitásképesség észleli a személyeket, a tartózkodási helyet és a szervezeteket)
> * A bővített adatlekérdezés használata a [**Search Explorerben**](search-explorer.md)

Ez a rövid útmutató az ingyenes szolgáltatáson fut, de az ingyenes tranzakciók száma napi 20 dokumentumra korlátozódik. Ha naponta többször szeretné futtatni ezt a rövid útmutatót, használjon kisebb fájlméretet, hogy több futtatással is elférjen.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Azure Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) biztosítja a mesterséges intelligenciát. Ez a rövid útmutató a folyamat megadásához szükséges lépéseket tartalmazza ezen erőforrások vonalbeli hozzáadásához. A fiókokat nem szükséges előre beállítani.

Az Azure-szolgáltatásoknak meg kell adniuk az indexelési folyamat bemeneteit. [Azure Search indexelő](search-indexer-overview.md)által támogatott adatforrások is használhatók. Ez a rövid útmutató az [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) -t használja tárolóként a forrásként szolgáló adatfájlokhoz. 

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Az Azure Blob szolgáltatás beállítása és a mintaadatok betöltése

1. [Töltsön le mintaadatokat](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), amelyek különböző típusú fájlok kis készletéből állnak. 

1. [Regisztráljon az Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)-hoz, hozzon létre egy Storage-fiókot, nyissa meg a blob Services-lapokat, és hozzon létre egy tárolót.  Hozza létre a Storage-fiókot ugyanabban a régióban, mint Azure Search.

1. A létrehozott tárolóban kattintson a **feltöltés** gombra az előző lépésben letöltött mintaképek feltöltéséhez.

   ![Forrásfájlok az Azure Blob Storage-ban](./media/cognitive-search-quickstart-blob/sample-data.png)

## <a name="create-the-enrichment-pipeline"></a>A bővítési folyamat létrehozása

Térjen vissza a Azure Search szolgáltatás irányítópultja lapra, és kattintson az **adatimportálás** parancsra a parancssávban, és állítsa be a kognitív dúsítást négy lépésben.

  ![Adatok importálása parancs](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1\. lépés: Adatforrás létrehozása

A **Kapcsolódás az adataihoz**lapon válassza az **Azure Blob Storage**lehetőséget, majd válassza ki a létrehozott fiókot és tárolót. Adjon nevet az adatforrásnak, a többi beállításnál pedig használja az alapértelmezett értékeket. 

  ![Azure Blob-konfiguráció](./media/cognitive-search-quickstart-blob/blob-datasource.png)

Folytassa a következő oldallal.

  ![A következő oldal gomb a kognitív kereséshez](media/cognitive-search-quickstart-blob/next-button-add-cog-search.png)

### <a name="step-2-add-cognitive-skills"></a>2\. lépés: Kognitív képességek hozzáadása

Ezután adja hozzá a bővítési lépéseket az indexelési folyamathoz. Ha nem rendelkezik Cognitive Services erőforrással, regisztrálhat egy ingyenes verzióra, amely naponta 20 tranzakciót biztosít. A mintaadatok 14 fájlból állnak, így a napi kiosztása többnyire a varázsló futtatása után lesz felhasználva.

1. Bontsa ki a **csatolás Cognitive Services** elemet a Cognitive Services API-k újraforrására vonatkozó beállítások megtekintéséhez. Ebben az oktatóanyagban használhatja az **ingyenes** erőforrást.

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/cog-search-attach.png)

2. Bontsa ki a bővítések **hozzáadása** lehetőséget, és válassza ki a természetes nyelvi feldolgozást végző szaktudást. Ebben a rövid útmutatóban válassza az entitások felismerése lehetőséget a személyekhez, szervezetekhez és helyekhez.

   ![A Cognitive Services csatolása](media/cognitive-search-quickstart-blob/skillset.png)

   A portál beépített képességeket kínál az OCR-feldolgozáshoz és a szöveges elemzésekhez. A portálon egy képességcsoport egyetlen forrásmezőn végez műveleteket. Ez kis célnak tűnhet, de az Azure-blobok esetében a blobdokumentum (például egy Word-dokumentum vagy egy PowerPoint-bemutató) legnagyobb részét a `content` mező tartalmazza. Ez a mező tehát nem ideális bemenet, mert egy blob összes tartalma szerepel benne.

3. Folytassa a következő oldallal.

   ![Következő oldal testreszabása index](media/cognitive-search-quickstart-blob/next-button-customize-index.png)

> [!NOTE]
> A természetes nyelvi feldolgozási képességek a mintaadatkészletbeli szöveges tartalmakon végeznek műveleteket. Mivel nem választottuk ki az OCR beállítást, a rendszer nem dolgozza fel a minta adatkészletben található JPEG-és PNG-fájlokat ebben a rövid útmutatóban. 

### <a name="step-3-configure-the-index"></a>3\. lépés: Az index konfigurálása

A varázsló általában egy alapértelmezett indexet tud kikövetkeztetni. Ebben a lépésben megtekintheti a generált index sémát, és esetleg módosíthatja a beállításokat. Alább látható a bemutató blob adatkészletéhez létrehozott alapértelmezett index.

Ebben a rövid útmutatóban a varázsló észszerű alapértelmezett beállításokat határoz meg: 

+ Az alapértelmezett név a *azureblob-index* az adatforrás típusa alapján. 

+ Az alapértelmezett mezők az eredeti`content`forrásoldali adatmezőn () alapulnak, valamint a kognitív folyamat által létrehozott kimeneti mezők (`people`, `organizations`és `locations`). Az alapértelmezett adattípusok a metaadatok és az adatok mintavételezésére utalnak.

+ Az alapértelmezett kulcs a *metadata_storage_path* (ez a mező egyedi értékeket tartalmaz).

+ Az alapértelmezett attribútumok lekérhető és **kereshetők** ezekhez a mezőkhöz. **Kereshető** azt jelzi, hogy egy mező kereshető. A lekérdezhető érték **azt jelenti,** hogy visszaadható az eredmények között. A varázsló feltételezi, hogy ezek a mezők lekérhető és kereshetők, mert egy készségkészlet keresztül hozta létre őket.

  ![Indexmezők](media/cognitive-search-quickstart-blob/index-fields.png)

Figyelje meg a **lekéréses** attribútum `content` áthúzott és kérdőjel értékét a mező alapján. Szöveg – nagy méretű blob-dokumentumok esetén `content` a mező a fájl nagy részét tartalmazza, amely akár több ezer sorra is futhat. Ha meg kell adnia a fájl tartalmát az ügyfél kódjához, győződjön meg arról, hogy a **beolvasható** maradok lehetőség van kiválasztva. Ellenkező esetben érdemes lehet törölni ezt az `content` attribútumot, ha a`people`kinyert `locations`elemek (, `organizations`és) elegendőek a célra.

A mezők **beolvasható** való megjelölése nem jelenti azt, hogy a mezőnek jelen *kell lennie* a keresési eredmények között. A keresési eredmények összetételét pontosan vezérelheti a **$Select** lekérdezési paraméterrel, hogy megadja, hogy mely mezők szerepeljenek hozzá. A Text-nagy méretű mezőkhöz hasonlóan `content`a **$Select** paraméter a felügyelhető keresési eredmények az alkalmazás emberi felhasználói számára való biztosítására szolgáló megoldás, miközben az ügyfél kódjának hozzáférése van az összes szükséges információhoz a  **Beolvasható** attribútum.
  
Folytassa a következő oldallal.

  ![Következő oldal létrehozása indexelő](media/cognitive-search-quickstart-blob/next-button-create-indexer.png)

### <a name="step-4-configure-the-indexer"></a>4\. lépés: Az indexelő konfigurálása

Az indexelő az indexelési folyamatot irányító magas szintű erőforrás. Meghatározza az adatforrás nevét, a célként megadott indexet és a végrehajtás gyakoriságát. Az **Adatok importálása** varázsló mindig egy indexelőt eredményez, amely ismétlődő jelleggel futtatható.

Az **Indexelő** lapon fogadja el az alapértelmezett nevet, és a **Futtatás egyszeres** futtatása lehetőséget használva azonnal futtathatja azt. 

  ![Az indexelő definíciója](media/cognitive-search-quickstart-blob/indexer-def.png)

Kattintson a **Submit (Küldés** ) gombra az indexelő létrehozásához és egyidejű futtatásához.

## <a name="monitor-indexing"></a>Indexelés figyelése

A dúsítási lépések végrehajtása hosszabb időt is igénybe véve, mint a szokásos szöveges indexelés. A varázslónak meg kell nyitnia az indexelő listát az Áttekintés oldalon, hogy nyomon követhesse a folyamat előrehaladását. Az önnavigációhoz lépjen az Áttekintés lapra, és kattintson az **Indexelő**elemre.

A figyelmeztetés azért fordul elő, mert a JPG-és PNG-fájlok képfájlok, és kihagyták az OCR-képességet ebből a folyamatból. Emellett csonkolt értesítéseket is talál. A Azure Search korlátozza az ingyenes szinten 32 000 karakter kinyerését.

  ![Azure Search-értesítés](./media/cognitive-search-quickstart-blob/indexer-notification.png)

Az indexelés és a bővítés sok időt vehet igénybe, ezért a funkció kipróbálásához kisebb adatkészletek használatát javasoljuk. 

## <a name="query-in-search-explorer"></a>Lekérdezés a Keresési ablakban

Miután az index létrejött, lekérdezéseket küldhet be, amelyek dokumentumokat adnak vissza az indexből. A portálon használja a **Keresési ablakot** a lekérdezések futtatásához és az eredmények megtekintéséhez. 

1. A keresési szolgáltatás Irányítópult lapján kattintson a parancssávon található **Keresési ablak** elemre.

1. A létrehozott index kiválasztásához kattintson az **Index módosítása** lehetőségre az oldal tetején.

1. Adja meg a keresési karakterláncot az index lekérdezéséhez, `search=Microsoft&searchFields=organizations`például:.

A rendszer JSON-formátumban adja vissza az eredményeket, amely különösen az Azure-blobokból származó nagyméretű dokumentumok esetén részletes és nehezen olvasható lehet. Ha az eredményeket nem lehet egyszerűen áttekinteni, a CTRL-F billentyűkombinációval kereshet a dokumentumokban. Ehhez a lekérdezéshez a JSON-ban megadott feltételek alapján kereshet. 

A CTRL-F billentyűkombináció abban is segítségére lehet, hogy meghatározza az egy adott eredményhalmazban szereplő dokumentumok számát. Az Azure-blobok esetében a portál a „metadata_storage_path” kulcsot választja, mert annak minden értéke egyedi a dokumentumban. A CTRL-F billentyűkombinációval keressen a „metadata_storage_path” kifejezésre, amely visszaadja a dokumentumok számát. 

  ![Keresési ablak – példa](./media/cognitive-search-quickstart-blob/search-explorer.png)

## <a name="takeaways"></a>Legfontosabb ismeretek

Ezzel befejezte az első kognitív-gazdagított indexelési gyakorlatát. Ennek a rövid útmutatónak a célja az volt, hogy bemutassa a legfontosabb alapelveket, és végigvezesse a varázsló lépésein, hogy saját adatait felhasználva gyorsan elkészíthesse egy kognitív keresési megoldás prototípusát.

Az általunk közvetíteni kívánt legfontosabb alapelvek egyike az Azure-adatforrásoktól való függőség. A kognitív keresési bővítés az indexelőkhöz kötődik, az indexelők pedig Azure- és forrásspecifikusak. Bár ez a rövid útmutató az Azure Blob Storage-ot használja, más Azure-adatforrások használata is lehetséges. További információkért lásd az [indexelők az Azure Search szolgáltatásban történő használatának](search-indexer-overview.md) ismertetését.

Egy másik fontos alapelv szerint a képességek a bemeneti mezőkön végeznek műveleteket. A portálon minden képességhez egyetlen forrásmezőt kell választania. A kódban a bemenetek lehetnek más mezők, vagy egy felsőbb rétegbeli képesség kimenetei.

 Egy képesség bemenetei egy index kimeneti mezőjéhez vannak rendelve. Belsőleg a portál [jegyzeteket](cognitive-search-concept-annotations-syntax.md) állít be és definiál egy [képességcsoportot](cognitive-search-defining-skillset.md), amellyel megalapozza a műveletek sorrendjét és az általános folyamatot. Ezeket a lépéseket a rendszer elrejti a portálon, ám az alapelvek ismerete fontos, amikor hozzálát a kód írásához.

Végezetül megtanulta, hogy az eredmények az index lekérdezésével tekinthetők meg. Végeredményben az Azure Search egy kereshető indexet biztosít, amelynek lekérdezését az [egyszerű](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vagy a [teljes lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) használatával végezheti el. A bővített mezőket tartalmazó index ugyanúgy működik, akár az összes többi. Ha standard vagy [egyéni elemzőket](search-analyzers.md), [pontozási profilokat](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [szinonimákat](search-synonyms.md), [jellemzőalapú szűrőket](search-filters-facets.md), földrajzihely-alapú keresést vagy egyéb Azure Search-szolgáltatásokat szeretne használni, természetesen azt is megteheti.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha saját előfizetésében dolgozik, a projekt végén érdemes megállapítani, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A már futó erőforrások pénzbe kerülnek. Az erőforrásokat egyenként is törölheti, vagy az erőforráscsoport törlésével törölheti a teljes erőforrás-készletet.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

A Cognitive Services erőforrás kiépített módjától függően a varázsló újrafuttatásával különböző képességekkel és forrásadatok mezőivel kísérletezheti az indexeléssel és a dúsítással. A lépések megismétléséhez törölje az indexelőt, majd hozza létre újra az indexelőt más választott beállításokkal.

+ Az **Áttekintés** > **Indexelők** résznél válassza ki a létrehozott indexet, majd kattintson a **Törlés** lehetőségre.

+ Az **Áttekintés** résznél kattintson duplán az **Indexelők** csempére. Keresse meg a létrehozott indexelőt, és törölje azt.

Vagy a létrehozott mintaadatokat és szolgáltatásokat újból felhasználva a következő oktatóanyagból megtanulhatja, hogyan végezheti el ugyanazokat a feladatokat programozott módon. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Ismerje meg a kognitív keresési REST API-kat](cognitive-search-tutorial-blob.md)
