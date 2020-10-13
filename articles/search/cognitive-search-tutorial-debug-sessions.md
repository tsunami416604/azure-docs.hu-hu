---
title: 'Oktatóanyag: hibakeresési munkamenetek használata a készségkészlet változásainak diagnosztizálására, javítására és elvégzésére'
titleSuffix: Azure Cognitive Search
description: A hibakeresési munkamenetek (előzetes verzió) egy portálon alapuló felületet biztosítanak a szakértelmével problémák/hibák kiértékeléséhez és javításához
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8bbd0b1979da69e5d4d18009100a7caee5a3d722
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397402"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Oktatóanyag: a készségkészlet változásainak diagnosztizálása, javítása és elutasítása

Ebben a cikkben a Azure Portal segítségével érheti el a hibakeresési munkameneteket a megadott készségkészlet kapcsolatos hibák kijavításához. A készségkészlet néhány hibát tartalmaz, amelyeket meg kell oldani. Ez az oktatóanyag végigvezeti egy hibakeresési munkameneten, amellyel azonosíthatja és megoldhatja a szaktudás bemeneteit és kimeneteit.

> [!Important]
> A hibakeresési munkamenetek az előzetes verziójú szolgáltatások, amelyek szolgáltatói szerződés nélkül érhetők el, és nem ajánlottak éles számítási feladatokhoz. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , vagy használja a jelenlegi előfizetését
> * Egy Azure Cognitive Search Service-példány
> * Azure Storage-fiók
> * [Postman asztali alkalmazás](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és az adatterhelés

Ez az oktatóanyag az Azure Cognitive Search és az Azure Storage Services szolgáltatást használja.

* 19 fájlból álló [mintaadatok letöltése](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) .

* [Hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Válassza ki ugyanazt a régiót, mint az Azure Cognitive Search a sávszélességgel kapcsolatos költségek elkerülése érdekében.
   
   Válassza ki a StorageV2 (általános célú v2) fiók típusát.

* Nyissa meg a tárolási szolgáltatások lapjait, és hozzon létre egy tárolót. Az ajánlott eljárás a "Private" hozzáférési szint megadására szolgál. Nevezze el a tárolót `clinicaltrialdataset` .

* A tárolóban kattintson a **feltöltés** gombra, és töltse fel az első lépésben a letöltött és kibontott fájlokat.

* [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

:::image type="content" source="media/search-get-started-postman/get-url-key.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" border="false":::

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="create-data-source-skillset-index-and-indexer"></a>Adatforrás, készségkészlet, index és indexelő létrehozása

Ebben a szakaszban a Poster és egy megadott gyűjtemény használatával hozható létre a keresési szolgáltatás adatforrása, a készségkészlet, az index és az indexelő.

1. Ha nem rendelkezik Poster-fiókkal, akkor [itt töltheti le a Poster Desktop alkalmazást](https://www.getpostman.com/).
1. [A Debug Sessions Poster-gyűjtemény letöltése](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Poster elindítása
1. A **fájlok**  >  **új**területen válassza ki az importálni kívánt gyűjteményt.
1. A gyűjtemény importálása után bontsa ki a műveletek listáját (...).
1. Kattintson a **Szerkesztés** gombra.
1. Adja meg a searchService nevét (például ha a végpont `https://mydemo.search.windows.net` , a szolgáltatás neve: " `mydemo` ").
1. Adja meg a keresési szolgáltatás elsődleges vagy másodlagos kulcsát tartalmazó apiKey.
1. Adja meg a storageConnectionString az Azure Storage-fiók kulcsok oldaláról.
1. Adja meg a Storage-fiókban létrehozott tároló containerName.

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="HTTP-végpont és elérési kulcs beszerzése":::

A gyűjtemény négy különböző REST-hívást tartalmaz, amelyek a szakasz végrehajtásához használatosak.

Az első hívás létrehozza az adatforrást. `clinical-trials-ds`. A második hívás létrehozza a készségkészlet `clinical-trials-ss` . A harmadik hívás létrehozza az indexet `clinical-trials` . A negyedik és a végső hívás létrehozza az indexelő `clinical-trials-idxr` . Miután a gyűjtemény összes hívása befejeződött, zárjuk be a postát, és térjen vissza a Azure Portal.

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="HTTP-végpont és elérési kulcs beszerzése":::

## <a name="check-the-results"></a>Az eredmények keresése

A készségkészlet néhány gyakori hibát tartalmaz. Ebben a szakaszban az összes dokumentum visszaküldésére szolgáló üres lekérdezést futtatva több hiba jelenik meg. A következő lépésekben a problémák hibakeresési munkamenet használatával lesznek feloldva.

1. Keresse meg a keresési szolgáltatást a Azure Portal. 
1. Válassza ki az **indexek** lapot. 
1. Az `clinical-trials` index kiválasztása
1. Ha üres lekérdezést szeretne futtatni, kattintson a **Keresés** gombra. 

A keresés befejezése után két, a felsorolt adat nélküli mező az ablakban a "szervezetek" és a "helyszínek" szerepelnek. Kövesse a lépéseket a készségkészlet által létrehozott összes probléma felderítéséhez.

1. Térjen vissza a keresési szolgáltatás áttekintés lapjára.
1. Válassza ki az **Indexelő** fület. 
1. Kattintson `clinical-trials-idxr` és válassza ki a figyelmeztetések értesítését. 

Számos probléma van a kivetítési kimeneti mezők leképezésével és a harmadik oldalon, mert egy vagy több képzettségi bemenet érvénytelen.

Térjen vissza a keresési szolgáltatás áttekintő képernyőjére.

## <a name="start-your-debug-session"></a>A hibakeresési munkamenet elindítása

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="HTTP-végpont és elérési kulcs beszerzése":::

1. Kattintson a hibakeresési munkamenetek (előzetes verzió) fülre.
1. Válassza a + NewDebugSession
1. Adja meg a munkamenet nevét. 
1. Kapcsolja össze a munkamenetet a Storage-fiókkal. 
1. Adja meg az indexelő nevét. Az indexelő az adatforrásra, a készségkészlet és az indexre mutató hivatkozásokat tartalmaz.
1. Fogadja el a gyűjtemény első dokumentumának alapértelmezett választható dokumentumát. 
1. **Mentse** a munkamenetet. A munkamenet mentése a készségkészlet által meghatározott mesterséges intelligencia-bővítési folyamat elindítását végzi.

> [!Important]
> Egy hibakeresési munkamenet csak egyetlen dokumentummal működik. Az adatkészletben található adott dokumentum > kiválasztható, vagy a munkamenet alapértelmezés szerint az első dokumentum lesz.

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="HTTP-végpont és elérési kulcs beszerzése":::

Ha a hibakeresési munkamenet befejezte a végrehajtást, a munkamenet alapértelmezett értéke a mesterséges intelligenciák lap, amely kiemeli a skill Graphot.

+ A skill Graph vizuális hierarchiát biztosít a készségkészlet és a végrehajtásuk sorrendjét felülről lefelé. A gráf egymás melletti képességeit párhuzamosan hajtják végre. A gráfban található készségek színkódolása a készségkészlet végrehajtott képességek típusait jelzi. A példában a zöld képességek szöveg, a vörös képesség pedig jövőkép. Ha a gráfban egy egyéni képességre kattint, a munkamenet ablakának jobb oldali ablaktábláján megjeleníti a szakértelem adott példányának részleteit. A szaktudás beállításai, a JSON-szerkesztő, a végrehajtás részletei és a hibák/figyelmeztetések mind elérhetők véleményezésre és szerkesztésre.
+ A dúsított adatstruktúra részletesen részletezi a forrás dokumentum tartalmából származó készségek által generált dúsítási fa csomópontjait.

A hibák/figyelmeztetések lap sokkal kisebb listát ad meg, mint a korábban megjelenő lista, mivel ez a lista csak egyetlen dokumentum hibáit részletezi. Az indexelő által megjelenített listához hasonlóan a figyelmeztető üzenetre is kattinthat, és megtekintheti a figyelmeztetés részleteit.

## <a name="fix-missing-skill-input-value"></a>Hiányzó szaktudás bemeneti értékének javítása

A hibák/figyelmeztetések lapon hiba található a címkével ellátott műveletnél `Enrichment.NerSkillV2.#1` . A hiba részletei azt ismertetik, hogy probléma merült fel a (z) "/document/languageCode" képzettségi értékkel. 

1. Térjen vissza az AI-Dúsítások lapra.
1. Kattintson a **skill gráfra**.
1. A jobb oldali ablaktáblán kattintson a #1ra jelölt képességre, hogy megjelenjen a részletei.
1. Keresse meg a "languageCode" bemenetét.
1. Válassza ki a **</>** vonal elején található szimbólumot, és nyissa meg a kifejezés kiértékelését.
1. A **kiértékelés** gombra kattintva erősítse meg, hogy a kifejezés hibát eredményez. A rendszer megerősíti, hogy a "languageCode" tulajdonság nem érvényes bemenet.

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" tulajdonságnak megfelelő leképezési útvonal.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" és "/Document/merged_content/Locations".

## <a name="fix-missing-skill-output-values"></a>Hiányzó szaktudás-kimeneti értékek javítása

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" kifejezés jelenik meg.

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" szövegben.

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" kimenetekhez.

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" entitás kifejezés-értékelőjét.

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" kifejezésre.
1. Kattintson a **Save (Mentés** ) gombra a jobb oldalon, a szakértelem részletei panelen.
1. A munkamenetek ablak menüjében kattintson a **Futtatás** elemre. Ez elindít egy másik végrehajtást a készségkészlet a dokumentum használatával.

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="HTTP-végpont és elérési kulcs beszerzése":::

Az összes hiba megoldódott.

## <a name="commit-changes-to-the-skillset"></a>Módosítások elvégzése a készségkészlet

A hibakeresési munkamenet elindítását követően a keresési szolgáltatás létrehozta a készségkészlet egy példányát. Ez azért történt, mert az elvégzett módosítások nem érintik az éles rendszert. Most, hogy befejezte a készségkészlet hibakeresését, a javításokat véglegesítheti (felülírhatja az eredeti készségkészlet) az éles rendszerbe. Ha továbbra is módosítani kívánja a készségkészlet az éles rendszer hatása nélkül, akkor a hibakeresési munkamenet később menthető és újra megnyitható.

1. Kattintson a **módosítások véglegesítve** gombra a fő hibakeresési munkamenetek menüben.
1. Az **OK** gombra kattintva erősítse meg, hogy szeretné frissíteni a készségkészlet.
1. Zárjuk be a hibakeresési munkamenetet, és válassza ki az **Indexelő** fület.
1. Nyissa meg a "klinikai kísérletek – idxr".
1. Kattintson az **Alaphelyzetbe állítás**gombra.
1. Kattintson a **Futtatás** elemre. A megerősítéshez kattintson **az OK** gombra.

Ha az indexelő befejezte a futást, akkor a futtatási Előzmények lapon a legutóbbi Futtatás időbélyegzője mellett egy zöld pipa és a szó sikeres lesz. A módosítások alkalmazása érdekében:

1. Zárja be az **Indexelő** lapot, és válassza az **index** fület.
1. Nyissa meg a "klinikai kísérletek" indexet, és a keresési Explorer lapon kattintson a **Keresés**gombra.
1. Az eredmény ablaknak be kell mutatnia, hogy a szervezetek és a helyszínek entitások már a várt értékekkel lesznek feltöltve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a szakértelmével](./cognitive-search-working-with-skillsets.md) 
>  [További információ a növekményes bővítésről és a gyorsítótárazásról](./cognitive-search-incremental-indexing-conceptual.md)