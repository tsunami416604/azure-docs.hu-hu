---
title: Batch tesztelés használatával javíthatja a LUIS-előrejelzés |} A Microsoft Docs
titleSuffix: Azure
description: Batch terhelésteszt eredmények áttekintése és módosítása a LUIS-előrejelzések javítása.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/16/2018
ms.author: diberry
ms.openlocfilehash: 0e1f5d29917ba381d4767faffb65847cd2ff210f
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237808"
---
# <a name="improve-app-with-batch-test"></a>A batch-teszt alkalmazás fejlesztéséhez

Ez az oktatóanyag bemutatja, hogyan utterance (kifejezés) előrejelzési problémák kereséséhez használja a batch-tesztelés.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

<!-- green checkmark -->
> [!div class="checklist"]
* Hozzon létre egy kötegfájlt teszt 
* Egy batch-teszt futtatása
* Vizsgálati eredmények áttekintése
* Hibák javítása 
* A batch ellenőrzése hosszadalmas

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md#luis-website)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha nem rendelkezik az emberi erőforrások alkalmazásból a [tekintse át a végpont utterances](luis-tutorial-review-endpoint-utterances.md) az oktatóanyagban [importálása](luis-how-to-start-new-app.md#import-new-app) a JSON-kódot egy új alkalmazást a [LUIS](luis-reference-regions.md#luis-website) webhely. Az importálandó alkalmazás a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json) GitHub-adattárban található.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `batchtest`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

Az alkalmazás betanításához.

## <a name="purpose-of-batch-testing"></a>A batch tesztelési célra
Batch tesztelés lehetővé teszi, hogy ellenőrizze az aktív, betanított modell állapotának ismert vannak beállítva címkézett utterances és entitásokat. A JSON-formátumú parancsfájlba a beszédmódok hozzáadása, és állítsa be az entitás címkék van szüksége, előre meghatározott az utterance (kifejezés) belül. 

<!--The recommended test strategy for LUIS uses three separate sets of data: example utterances provided to the model, batch test utterances, and endpoint utterances. --> Ebben az oktatóanyagban nem alkalmazás használatakor, Ön *nem* példa megcímkézzen már hozzá van adva egy leképezés használatával. Ellenőrizze a kötegelt teszt utterances elleni példa megcímkézzen [exportálása](luis-how-to-start-new-app.md#export-app) az alkalmazást. Hasonlítsa össze az alkalmazás például utterance (kifejezés) a, a batch-teszt kimondott szöveg. 

Batch-tesztelés vonatkozó követelmények:

* Legfeljebb 1000 utterances tesztenként. 
* Nincsenek ismétlődések. 
* Engedélyezett entitástípusra: egyszerű, hierarchikus csak megmunkált megismert entitások (szülő csak), és összetett. Batch-tesztelés hasznos csak megmunkált megtanult szándékokat és entitásokat.

## <a name="create-a-batch-file-with-utterances"></a>Hozzon létre egy kötegfájlt a kimondott szöveg
1. Hozzon létre `HumanResources-jobs-batch.json` egy szövegszerkesztőben, például [VSCode](https://code.visualstudio.com/). 

2. A JSON-formátumú parancsfájlba, az beszédmódok hozzáadása a **szándékot** azt szeretné, a teszt előre jelzett. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Futtassa a köteget

1. Válassza ki **teszt** a felső navigációs sávban. 

    [ ![Képernyőkép a LUIS-alkalmazás és felső, jobb oldali navigációs sávban kiemelt teszt](./media/luis-tutorial-batch-testing/hr-first-image.png)](./media/luis-tutorial-batch-testing/hr-first-image.png#lightbox)

2. Válassza ki **Batch-tesztelési panel** a jobb oldali panelen. 

    [ ![Kiemelt Batch teszt panel képernyőképe a LUIS-alkalmazás](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Válassza ki **importálás adatkészlet**.

    [ ![Képernyőkép a LUIS alkalmazás importálása adatkészlettel kiemelésével](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Válassza ki a fájl rendszer helyét a `HumanResources-jobs-batch.json` fájlt.

5. Nevezze el az adatkészlet `intents only` válassza **kész**.

    ![Fájl kiválasztása](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Kattintson a **Futtatás** gombra. Várjon, amíg a teszt történik.

    [ ![Képernyőkép a LUIS alkalmazás Futtatás kiemelésével](./media/luis-tutorial-batch-testing/hr-run-button.png)](./media/luis-tutorial-batch-testing/hr-run-button.png#lightbox)

7. Válassza ki **eredmények megtekintéséhez**.

8. Tekintse át az eredményeket a graph és jelmagyarázat.

    [ ![Képernyőkép a LUIS-alkalmazás és a batch terhelésiteszt-eredményei](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Batch-eredmények áttekintése
A batch diagram jeleníti meg az eredmények négy elemzésében. A diagram jobb szűrő van. Alapértelmezés szerint a szűrő értéke az első célja a listában. A szűrő tartalmazza, a leképezések és a hierarchikus csak egyszerű (szülő csak), és összetett entitásokat. Amikor kiválaszt egy [szakaszban, a diagram](luis-concept-batch-test.md#batch-test-results) vagy egy pontot a diagramon belül, a társított utterance(s) megjeleníti a diagram alatt. 

A diagram rámutatáskor egérkereket nagyítása vagy csökkentheti a megjelenített a diagramon. Ez akkor hasznos, ha a fürtözött szorosan együtt diagramra hány pontot. 

A diagram van négy elemzésében, és két a szakaszok vörös színnel jelenik meg. **Ezek a szakaszok a fókusz a a**. 

### <a name="getjobinformation-test-results"></a>GetJobInformation terhelésiteszt-eredményei
A **GetJobInformation** terhelésiteszt-eredményei jelennek meg a szűrő megjelenítése, hogy sikeres volt-e 2. a négy előrejelzéseket. Válassza ki a nevét **vakriasztás** megtekintéséhez a diagram alatt megcímkézzen felső megfelelő quadrant felett. 

![A LUIS batch teszt kimondott szöveg](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Miért van két előre meghatározott megcímkézzen **ApplyForJob**, a megfelelő leképezés helyett **GetJobInformation**? A két szándék szorosan kapcsolódó szó választási lehetőség és a word elrendezésének tekintetében. Emellett nincsenek majdnem három alkalommal annyi példák **ApplyForJob** mint **GetJobInformation**. Ez a példa kimondott szöveg egyenetlenség tömege **ApplyForJob** leképezés a hónappal számolunk. 

Figyelje meg, hogy mindkét leképezések rendelkezik-e hibák azonos száma. Az egyik célja egy helytelen előrejelzési más szándéka is hatással van. Mindkettő rendelkezik hibák, mert megcímkézzen is helytelenül előre jelzett egy leképezés a, és a másik szándékot is helytelenül nem jelzett. 

![A LUIS batch tesztelési hibák szűrése](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

A megfelelő felső utterances időponthoz a **vakriasztás** szakaszban vannak `Can I apply for any database jobs with this resume?` és `Can I apply for any database jobs with this resume?`. Az első utterance (kifejezés), a word a `resume` csak a felhasznált **ApplyForJob**. A második utterance (kifejezés), a word a `apply` még csak használták a **ApplyForJob** szándékot.

## <a name="fix-the-app-based-on-batch-results"></a>Javítsa ki az alkalmazást, a batch eredményei alapján
Ez a szakasz célja, hogy megcímkézzen megfelelően az előre jelzett összes **GetJobInformation** azzal, hogy az alkalmazást. 

A látszólag gyors javítás ezek batch fájl beszédmódok hozzáadása a megfelelő leképezés lenne. Nincs mit kíván tenni, ha ez. Azt szeretné, hogy a LUIS megfelelően előre, anélkül, hogy hozzáadják őket a példaként a kimondott szöveg. 

A kimondott szöveg eltávolításával kapcsolatos is vezetőnév **ApplyForJob** mindaddig, amíg az utterance (kifejezés) mennyiség megegyezik a **GetJobInformation**. Amely előfordulhat, hogy javítsa ki a vizsgálati eredmények, de a pontos előrejelzésére adott szándékot legközelebb akadályozná az intelligens HANGFELISMERÉSI. 

Az első javítást, hogy a további beszédmódok hozzáadása **GetJobInformation**. A második javítás szavakkal is, mint a súlyozást csökkentése érdekében egy `resume` és `apply` felé a **ApplyForJob** szándékot. 

### <a name="add-more-utterances-to-getjobinformation"></a>A további beszédmódok hozzáadása **GetJobInformation**
1. Zárja be a batch-teszt panelen válassza a **tesztelése** gombot a felső navigációs panelen. 

    [ ![Képernyőkép a LUIS teszt gomb kiemelésével](./media/luis-tutorial-batch-testing/hr-close-test-panel.png)](./media/luis-tutorial-batch-testing/hr-close-test-panel.png#lightbox)

2. Válassza ki **GetJobInformation** leképezések listájából. 

    [ ![Képernyőkép a LUIS teszt gomb kiemelésével](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png)](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png#lightbox)

3. Több, különböző hossza, a word választási lehetőség és a word-előkészítés, olyan a feltételek feltétlenül beszédmódok hozzáadása `resume`, `c.v.`, és `apply`:

    |A példa utterances **GetJobInformation** leképezés|
    |--|
    |Az új feladat egy stocker a raktári igényel, hogy I egy folytatása a alkalmazni?|
    |Hol találhatók még ma a Tetőfedő feladatok?|
    |Hallottam a folytatási igénylő orvosi kódolási feladat volt.|
    |Szeretném, így azok c.v.s. írási college gyerek feladat |
    |Íme a saját folytatása, a számítógépek használatával főiskolában, új bejegyzés keres.|
    |Milyen pozíciók gyermek és az otthoni ellátás érhetők el?|
    |Van egy belső munkatársa ügyfélszolgálati, a újság?|
    |Saját C.v. látható, hogy jó elemzése a beszerzés, költségvetések és elveszett pénzt vagyok. Van-e ilyen jellegű munka?|
    |Hol vannak a föld részletes elemzések kibontásáról feladatok most?|
    |Az EMS illesztőprogramként 8 évig dolgoztam. Az új feladatokat?|
    |Új élelmiszer-kezelési feladatok programra?|
    |Hány új yard munkahelyi feladatok érhetők el?|
    |Van egy új HR-bejegyzés a munkaerő kapcsolatok és egyeztetést végezni?|
    |A főkiszolgálók erőforrástár és az archív felügyeleti vannak. Minden olyan új helyre?|
    |Milyen babysitting feladatok a 13 éves korosztály az városa ma?|

    Nincs címke a **feladat** megcímkézzen entitás. Az oktatóanyag ezen szakaszában csak a leképezési előrejelzési összpontosít.

4. Az alkalmazás betanításához kiválasztásával **Train** a jobb felső navigációs.

## <a name="verify-the-fix-worked"></a>Ellenőrizze, hogy a javítás működött
Annak érdekében, hogy ellenőrizze, hogy a batch-teszt megcímkézzen megfelelően előre jelzett, futtassa újra a batch-vizsgálat.

1. Válassza ki **teszt** a felső navigációs sávban. Ha a batch-eredmények meg nyitva, válassza ki a **vissza a listához**.  

2. Kattintson a három pontra (***...*** ) gombot a batch nevétől jobbra **futtatása adatkészlet**. Várjon, amíg a batch-teszt történik. Figyelje meg, hogy a **eredmények megtekintéséhez** már zöld gombot. Ez azt jelenti, hogy az egész köteget sikeresen lefutott.

3. Válassza ki **eredmények megtekintéséhez**. A leképezések összes kell zöld ikonok a leképezés neve a bal oldalon. 

    ![Képernyőkép a LUIS batch eredmények gomb kiemelésével](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>Az entitások kötegfájl létrehozása 
Ellenőrizze az entitások egy batch-teszt, az entitások kell az a batch JSON-fájl neve. Csak a gép megismert entitások: egyszerű, hierarchikus (szülő csak), és összetett entitásokat. Ne adjon hozzá nem gép megismert entitások, mert azok mindig találhatók reguláris kifejezések keresztül, vagy explicit szöveg megfelel.

Az entitások teljes Word változata ([jogkivonat](luis-glossary.md#token)) száma befolyásolhatja az előrejelzési minőségét. Ellenőrizze, hogy a betanítási adatok, a célt a címkézett utterances megadott hosszúságú entitás számos tartalmaz. 

Amikor először és a tesztelésre kötegfájlok, érdemes néhány utterances kezdődhet, és entitások, hogy tudja, hogy működik, valamint néhány, amely, áttekinteni helytelenül előrejelzett előfordulhat, hogy lehet. Ez segít összpontosíthat a a problémás területek gyorsan. Tesztelés után a **GetJobInformation** és **ApplyForJob** leképezések számos különböző feladat-neveket használó, nem jelzett, amely a batch tesztfájlt célja a e előrejelzési probléma bizonyos értékekkel **feladat** entitás. 

Az érték egy **feladat** entitás, a teszt utterances megadott, általában egy vagy két szavak, néhány példa alatt további szavakat. Ha _saját_ emberi erőforrások alkalmazáson általában számos szavak feladat nevét, a példában megcímkézzen címkézte **feladat** entitás ebben az alkalmazásban nem működne.

1. Hozzon létre `HumanResources-entities-batch.json` egy szövegszerkesztőben, például [VSCode](https://code.visualstudio.com/). Töltse le vagy [a fájl](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json) a LUIS-minták Github-adattárból.


2. A JSON-formátumú batch fájlban adja hozzá a kimondott szöveg objektumok egy tömbjét a **szándékot** szeretné a tesztelési, valamint a helyek az utterance (kifejezés) bármely entitáslistájában előre jelzett. Mivel egy entitás jogkivonat-alapú, ügyeljen arra, hogy elindíthatja és leállíthatja a minden entitás az a karakter. Ne kezdő vagy záró szóközt az utterance (kifejezés). Ez hibát okoz a batch-fájl importálása során.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]

<!--TBD: when will the patterns fix be in for batch testing? -->
## <a name="run-the-batch-with-entities"></a>Futtassa a köteget az entitások

1. Válassza ki **teszt** a felső navigációs sávban. 

2. Válassza ki **Batch-tesztelési panel** a jobb oldali panelen. 

3. Válassza ki **importálás adatkészlet**.

4. Válassza ki a fájl rendszer helyét a `HumanResources-entities-batch.json` fájlt.

5. Nevezze el az adatkészlet `entities` válassza **kész**.

6. Kattintson a **Futtatás** gombra. Várjon, amíg a teszt történik.

    [ ![Képernyőkép a LUIS alkalmazás Futtatás kiemelésével](./media/luis-tutorial-batch-testing/hr-run-button.png)](./media/luis-tutorial-batch-testing/hr-run-button.png#lightbox)

7. Válassza ki **eredmények megtekintéséhez**.

## <a name="review-entity-batch-results"></a>Entity batch eredmények áttekintése
A diagram megnyílik a megfelelő előrejelzett szándék fog vonatkozni. Görgessen le a jobb oldali szűrő keresse meg a szövegértékekké entitás előrejelzéseket. 

1. Válassza ki a **feladat** szűrő entitás.

    ![A szűrő szövegértékekké entitás előrejelzések](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    A diagram változik az entitás előrejelzések megjelenítéséhez. 

2. Válassza ki **téves negatív** alsó, bal oldali quadrant a diagram. A billentyűzet kombináció CTRL + E majd használja a jogkivonat nézetre. 

    [ ![Token nézet és az entitás előrejelzések](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    A diagram alatt megcímkézzen megtekintésével tárja fel egy egységes hiba, amikor a feladat neve tartalmazza a `SQL`. A példa kimondott szöveg és a feladatlistában kifejezés áttekintése, SQL van csak egyszer használt, és a egy nagyobb méretű feladat neve csak részeként `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Javítsa ki az alkalmazást, entity batch eredményei alapján
Az alkalmazás kompatibilitását meg megfelelően az SQL-feladatok változata, a LUIS van szükség. Többféle módon is, hogy a javítást. 

* Explicit módon fel további példa kimondott szöveg, amely SQL használata, és azokat a szavakat címkézését feladat egységként. 
* Explicit módon további SQL-feladatok hozzáadása a kifejezéslista

Ezek a feladatok elvégzéséhez van hátra.

Hozzáadás egy [minta](luis-concept-patterns.md) előtt az entitás megfelelő összegyűjtése várható nem történik meg a probléma megoldása érdekében. Ez azért, mert a minta nem egyeznek, amíg a rendszer észleli a minta összes entitást. 

## <a name="what-has-this-tutorial-accomplished"></a>Mi van ebben az oktatóanyagban történik?
Hibák keresése a Batch és a modell javításának nőtt az alkalmazás előrejelzés pontosságát. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Válassza a **My apps** (Saját alkalmazások) elemet a bal oldali menüben. Kattintson a három pontra **...**  az alkalmazások listájában, az alkalmazás nevétől jobbra, válassza ki a **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a minták](luis-tutorial-pattern.md)

