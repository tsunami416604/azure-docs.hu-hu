---
title: 'Oktatóanyag: hibakeresési munkamenetek használata a készségkészlet változásainak diagnosztizálására, javítására és elvégzésére'
titleSuffix: Azure Cognitive Search
description: A hibakeresési munkamenetek (előzetes verzió) egy portálon alapuló felületet biztosítanak a szakértelmével problémák/hibák kiértékeléséhez és javításához
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 35590fd8aefb8b6a90adeef52fabe773663403c2
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85510992"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Oktatóanyag: a készségkészlet változásainak diagnosztizálása, javítása és elutasítása

Ebben a cikkben a Azure Portal segítségével érheti el a hibakeresési munkameneteket a megadott készségkészlet kapcsolatos hibák kijavításához. A készségkészlet néhány hibát tartalmaz, amelyeket meg kell oldani. Ez az oktatóanyag végigvezeti egy hibakeresési munkameneten, amellyel azonosíthatja és megoldhatja a szaktudás bemeneteit és kimeneteit.

> [!Important]
> A hibakeresési munkamenetek az előzetes verziójú szolgáltatások, amelyek szolgáltatói szerződés nélkül érhetők el, és nem ajánlottak éles számítási feladatokhoz. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Azure-előfizetés. Hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , vagy használja a jelenlegi előfizetését
> * Egy Azure Cognitive Search Service-példány
> * Azure Storage-fiók
> * [Postman asztali alkalmazás](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és az adatterhelés

Ez az oktatóanyag az Azure Cognitive Search és az Azure Storage Services szolgáltatást használja.

* 19 fájlból álló [mintaadatok letöltése](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) .

* [Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Válassza ki ugyanazt a régiót, mint az Azure Cognitive Search a sávszélességgel kapcsolatos költségek elkerülése érdekében.
   
   Válassza ki a StorageV2 (általános célú v2) fiók típusát.

* Nyissa meg a tárolási szolgáltatások lapjait, és hozzon létre egy tárolót. Az ajánlott eljárás a "Private" hozzáférési szint megadására szolgál. Nevezze el a tárolót `clinicaltrialdataset` .

* A tárolóban kattintson a **feltöltés** gombra, és töltse fel az első lépésben a letöltött és kibontott fájlokat.

* [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban**kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-postman/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="create-data-source-skillset-index-and-indexer"></a>Adatforrás, készségkészlet, index és indexelő létrehozása

Ebben a szakaszban a Poster és egy megadott gyűjtemény használatával hozható létre a keresési szolgáltatás adatforrása, a készségkészlet, az index és az indexelő.

1. Ha nem rendelkezik Poster-fiókkal, akkor [itt töltheti le a Poster Desktop alkalmazást](https://www.getpostman.com/).
1. [A Debug Sessions Poster-gyűjtemény letöltése](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Poster elindítása
1. A **fájlok**  >  **új**területen válassza ki az importálni kívánt gyűjteményt.
1. A gyűjtemény importálása után bontsa ki a műveletek listáját (...).
1. Kattintson a **Szerkesztés** gombra.
1. Adja meg a searchService nevét (például ha a végpont https://mydemo.search.windows.net , akkor a szolgáltatás neve "mydemo").
1. Adja meg a keresési szolgáltatás elsődleges vagy másodlagos kulcsát tartalmazó apiKey.
1. Adja meg a storageConnectionString az Azure Storage-fiók kulcsok oldaláról.
1. Adja meg a Storage-fiókban létrehozott tároló containerName.

> [!div class="mx-imgBorder"]
> ![a Poster változóinak szerkesztése](media/cognitive-search-debug/postman-enter-variables.png)

A gyűjtemény négy különböző REST-hívást tartalmaz, amelyek a szakasz végrehajtásához használatosak.

Az első hívás létrehozza az adatforrást. `clinical-trials-ds`. A második hívás létrehozza a készségkészlet `clinical-trials-ss` . A harmadik hívás létrehozza az indexet `clinical-trials` . A negyedik és a végső hívás létrehozza az indexelő `clinical-trials-idxr` . Miután a gyűjtemény összes hívása befejeződött, zárjuk be a postát, és térjen vissza a Azure Portal.

> [!div class="mx-imgBorder"]
> ![az adatforrások létrehozása a Poster használatával](media/cognitive-search-debug/postman-create-data-source.png)

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

> [!div class="mx-imgBorder"]
> ![új hibakeresési munkamenet elindítása](media/cognitive-search-debug/new-debug-session-screen-required.png)

1. Kattintson a hibakeresési munkamenetek (előzetes verzió) fülre.
1. Válassza a + NewDebugSession
1. Adja meg a munkamenet nevét. 
1. Kapcsolja össze a munkamenetet a Storage-fiókkal. 
1. Adja meg az indexelő nevét. Az indexelő az adatforrásra, a készségkészlet és az indexre mutató hivatkozásokat tartalmaz.
1. Fogadja el a gyűjtemény első dokumentumának alapértelmezett választható dokumentumát. 
1. **Mentse** a munkamenetet. A munkamenet mentése a készségkészlet által meghatározott mesterséges intelligencia-bővítési folyamat elindítását végzi.

> [!Important]
> Egy hibakeresési munkamenet csak egyetlen dokumentummal működik. Az adatkészletben található adott dokumentum > kiválasztható, vagy a munkamenet alapértelmezés szerint az első dokumentum lesz.

> [!div class="mx-imgBorder"]
> ![Az új hibakeresési munkamenet elindult](media/cognitive-search-debug/debug-execution-complete1.png)

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

> [!div class="mx-imgBorder"]
> ![Kifejezés kiértékelése](media/cognitive-search-debug/expression-evaluator-language.png)

Ezt a hibát kétféleképpen lehet megkeresni a munkamenetben. Az első az a hely, ahol a bemenet származik – milyen képességgel kellene előállítani ezt az eredményt? A szakértelem részleteit tartalmazó ablaktábla végrehajtások lapján meg kell jelennie a bemenet forrásának. Ha nincs forrás, ez egy mező-hozzárendelési hibát jelez.

1. Kattintson a **végrehajtások** lapra.
1. Tekintse meg a BEMENETeket, és keresse meg a "languageCode" kifejezést. A megadott bemenethez nem található forrás. 
1. Váltson a bal oldali panelre a dúsított adatstruktúra megjelenítéséhez. Nincs a "languageCode" tulajdonságnak megfelelő leképezési útvonal.

> [!div class="mx-imgBorder"]
> ![Dúsított adatstruktúra](media/cognitive-search-debug/enriched-data-structure-language.png)

Van egy leképezett elérési út a "Language" kifejezéshez. Szóval, van egy elírás a képességek beállításaiban. A "/Document/Language" kifejezéssel rendelkező #1-szakértelemben lévő kifejezés kijavításához frissíteni kell a kifejezést.

1. Nyissa meg a kifejezés kiértékelését **</>** a "Language" elérési útra.
1. Másolja a kifejezést. Zárja be az ablakot.
1. Nyissa meg az #1 szakértelmet, és nyissa meg a kifejezés kiértékelését **</>** a "languageCode" bemenethez.
1. Illessze be a "/Document/Language" nevű új értéket a kifejezés mezőbe, és kattintson az **értékelés**gombra.
1. Meg kell jelennie az "en" helyes bemenetnek. A kifejezés frissítéséhez kattintson az Apply (alkalmaz) gombra.
1. Kattintson a **Save (Mentés** ) gombra a jobb oldalon, a szakértelem részletei panelen.
1. A munkamenet ablak menüjében kattintson a **Futtatás** elemre. Ez elindít egy másik végrehajtást a készségkészlet a dokumentum használatával. 

A hibakeresési munkamenet végrehajtásának befejeződése után kattintson a hibák/figyelmeztetések lapra, és megjeleníti, hogy a "dúsítás. NerSkillV2. #1" címkével ellátott hiba megszűnt. Vannak azonban még két figyelmeztetés arról, hogy a szolgáltatás nem tudja leképezni a szervezetekhez és helyekhez tartozó kimeneti mezőket a keresési indexhez. Hiányoznak értékek: "/Document/merged_content/Organizations" és "/Document/merged_content/Locations".

## <a name="fix-missing-skill-output-values"></a>Hiányzó szaktudás-kimeneti értékek javítása

> [!div class="mx-imgBorder"]
> ![Hibák és figyelmeztetések](media/cognitive-search-debug/warnings-missing-value-locations-organizations.png)

A szakértelemből hiányzó kimeneti értékek találhatók. Ha azonosítani szeretné a hibát, ugorjon a dúsított adatstruktúrára, keresse meg az érték nevét, és tekintse meg az eredeti forrását. A hiányzó szervezetek és helyszínek értéke esetén a rendszer a skill #1ból származó kimeneteket jelenít meg. Az egyes elérési utakhoz tartozó Expression értékelő </> megnyitásakor a "/Document/Content/Organizations" és a "/Document/Content/Locations" kifejezés jelenik meg.

> [!div class="mx-imgBorder"]
> ![Expression értékelő szervezetek entitása](media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png)

Az entitások kimenete üres, és nem lehet üres. Milyen bemenetek jönnek létre ez az eredmény?

1. Nyissa meg a **skill Graphot** , és válassza a skill #1 elemet.
1. Válassza a **végrehajtások** fület a jobb képességek részletei panelen.
1. Nyissa meg a kifejezés kiértékelését **</>** a "text" szövegben.

> [!div class="mx-imgBorder"]
> ![Szöveg-képzettség bevitele](media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png)

A bemenet megjelenített eredménye nem hasonlít a szövegbeviteli adatokhoz. Úgy néz ki, mint egy új vonallal körülvett rendszerkép. A szöveg hiánya azt jelenti, hogy egyetlen entitás sem azonosítható. A készségkészlet hierarchiájának megtekintésekor a rendszer először a #6 (OCR) által dolgozza fel a tartalmat, majd átadja a #5 (Merge) szaktudásnak. 

1. Válassza ki a #5 (egyesítés) képességet a **skill Graphban**.
1. Válassza a **végrehajtás** fület a jobb képességek részletei panelen, és nyissa meg a kifejezés kiértékelését **</>** a "mergedText" kimenetekhez.

> [!div class="mx-imgBorder"]
> ![Kimenet egyesítési képességhez](media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png)

Itt a szöveg párosítva van a képpel. A "/Document/merged_content" kifejezésben a "szervezetek" és a "Locations" elérési utakon található hiba látható a #1 skill számára. A "/Document/Content" helyett "/Document/merged_content"-t kell használnia a "text" bemenetekhez.

1. Másolja ki a "mergedText" kimenethez tartozó kifejezést, és zárjuk be a kifejezés értékelési ablakát.
1. Válassza a szakértelem #1 lehetőséget a **skill gráfban**.
1. Válassza ki a **készségfejlesztés** lapot a megfelelő képzettség részletei panelen.
1. Nyissa meg a kifejezés kiértékelését **</>** a "text" bemenethez.
1. Illessze be az új kifejezést a mezőbe. Kattintson a **kiértékelés**lehetőségre.
1. A hozzáadott szöveggel megjelenő helyes bemenetnek szerepelnie kell. Kattintson az **alkalmaz** gombra a képzettségi beállítások frissítéséhez.
1. Kattintson a **Save (Mentés** ) gombra a jobb oldalon, a szakértelem részletei panelen.
1. A munkamenetek ablak menüjében kattintson a **Futtatás** elemre. Ez elindít egy másik végrehajtást a készségkészlet a dokumentum használatával.

Az indexelő futásának befejeződése után a hibák még mindig vannak. Lépjen vissza a skill #1 és vizsgálja meg. A képességhez megadott bemenet a tartalomból "merged_content" értékre lett javítva. Mik az entitások kimenetei a szakértelemben?

1. Válassza az **AI-gazdagítók** fület.
1. Válassza a **skill Graph** lehetőséget, majd kattintson a skill #1 lehetőségre.
1. Keresse meg a **képességek beállításait** a "kimenetek" kereséséhez.
1. Nyissa meg a **</>** "szervezetek" entitás kifejezés-értékelőjét.

> [!div class="mx-imgBorder"]
> ![A szervezetek entitásának kimenete](media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png)

A kifejezés eredményének kiértékelése a megfelelő eredményt adja. A szakértelem feladata az entitás, a "szervezetek" helyes értékének azonosítása. Az entitás elérési útjának kimeneti leképezése azonban továbbra is hibát jelez. A szakértelem kimeneti elérési útjának és a hiba kimeneti elérési útjának összevetésével a/Document/Content csomópontban lévő kimeneteket, szervezeteket és helyeket szülő képességgel. Míg a kimeneti mező leképezése az eredmények szülővé tételét várja a/Document/merged_content csomópont alatt. Az előző lépésben a bevitel "/Document/Content" értékről "/Document/merged_content" értékre változott. A szaktudás beállításainak környezetét módosítani kell annak érdekében, hogy a kimenet a megfelelő kontextussal legyen létrehozva.

1. Válassza az **AI-gazdagítók** fület.
1. Válassza a **skill Graph** lehetőséget, majd kattintson a skill #1 lehetőségre.
1. A "kontextus" kifejezésre navigálva keresse meg a **képességek beállításait** .
1. Kattintson duplán a "Context" beállításra, és szerkessze a "/Document/merged_content" kifejezésre.
1. Kattintson a **Save (Mentés** ) gombra a jobb oldalon, a szakértelem részletei panelen.
1. A munkamenetek ablak menüjében kattintson a **Futtatás** elemre. Ez elindít egy másik végrehajtást a készségkészlet a dokumentum használatával.

> [!div class="mx-imgBorder"]
> ![Környezeti javítás a szaktudás beállításakor](media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png)

Az összes hiba megoldódott.

## <a name="commit-changes-to-the-skillset"></a>Módosítások elvégzése a készségkészlet

A hibakeresési munkamenet elindítását követően a keresési szolgáltatás létrehozta a készségkészlet egy példányát. Ez azért történt, mert az elvégzett módosítások nem érintik az éles rendszert. Most, hogy befejezte a készségkészlet hibakeresését, a javításokat véglegesítheti (felülírhatja az eredeti készségkészlet) az éles rendszerbe. Ha továbbra is módosítani kívánja a készségkészlet az éles rendszer hatása nélkül, akkor a hibakeresési munkamenet később menthető és újra megnyitható.

1. Kattintson a **módosítások véglegesítve** gombra a fő hibakeresési munkamenetek menüben.
1. Az **OK** gombra kattintva erősítse meg, hogy szeretné frissíteni a készségkészlet.
1. Zárjuk be a hibakeresési munkamenetet, és válassza ki az **Indexelő** fület.
1. Nyissa meg a "klinikai kísérletek – idxr".
1. Kattintson az **Alaphelyzetbe állítás**gombra.
1. Kattintson a **Futtatás**gombra. A megerősítéshez kattintson **az OK** gombra.

Ha az indexelő befejezte a futást, akkor a futtatási Előzmények lapon a legutóbbi Futtatás időbélyegzője mellett egy zöld pipa és a szó sikeres lesz. A módosítások alkalmazása érdekében:

1. Zárja be az **Indexelő** lapot, és válassza az **index** fület.
1. Nyissa meg a "klinikai kísérletek" indexet, és a keresési Explorer lapon kattintson a **Keresés**gombra.
1. Az eredmény ablaknak be kell mutatnia, hogy a szervezetek és a helyszínek entitások már a várt értékekkel lesznek feltöltve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrások egyesével is törölhetők, de az erőforráscsoport törlésével egyszerre eltávolítható az összes erőforrás is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a szakértelmével](https://docs.microsoft.com/azure/search/cognitive-search-working-with-skillsets) 
>  [További információ a növekményes bővítésről és a gyorsítótárazásról](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)