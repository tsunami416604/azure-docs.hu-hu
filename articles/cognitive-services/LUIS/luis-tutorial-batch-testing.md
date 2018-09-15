---
title: '2. oktatóanyag: A Batch teszt együtt 1000 kimondott szöveg '
titleSuffix: Azure Cognitive Services
description: Ez az oktatóanyag bemutatja, hogyan használja a batch tesztelése az alkalmazásban az előrejelzési problémák utterance (kifejezés) megkeresheti és kijavíthatja azokat.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 1f1055b84a83d71931ebd0ca11b5bcd1bd16ad02
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630984"
---
# <a name="tutorial--2-batch-test-data-sets"></a>Oktatóanyag: 2. Batch-teszt adatkészletek

Ez az oktatóanyag bemutatja, hogyan használja a batch tesztelése az alkalmazásban az előrejelzési problémák utterance (kifejezés) megkeresheti és kijavíthatja azokat.  

Batch tesztelés lehetővé teszi, hogy ellenőrizze az aktív, betanított modell állapotának ismert vannak beállítva címkézett utterances és entitásokat. A JSON-formátumú parancsfájlba a beszédmódok hozzáadása, és állítsa be az entitás címkék van szüksége, előre meghatározott az utterance (kifejezés) belül. 

Batch-tesztelés vonatkozó követelmények:

* Legfeljebb 1000 utterances tesztenként. 
* Nincsenek ismétlődések. 
* Engedélyezett entitástípusra: egyszerű, hierarchikus csak megmunkált megismert entitások (szülő csak), és összetett. Batch-tesztelés hasznos csak megmunkált megtanult szándékokat és entitásokat.

Amikor egy alkalmazást, ez az oktatóanyag nem használ, *nem* már hozzá van adva egy leképezés példa megcímkézzen használja. 

**Ebből az oktatóanyagból megtudhatja, hogyan lehet:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Használja meglévő oktatóanyag alkalmazása
> * Hozzon létre egy kötegfájlt teszt 
> * Egy batch-teszt futtatása
> * Vizsgálati eredmények áttekintése
> * Hibák javítása 
> * A batch ellenőrzése hosszadalmas

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata

Folytassa az alkalmazás nevű az előző oktatóanyagban létrehozott **emberi**. 

Ha az előző oktatóanyagban az emberi alkalmazás nem rendelkezik, használja az alábbi lépéseket:

1.  Töltse le és mentse [alkalmazás JSON-fájlt](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. A JSON importálja egy új alkalmazást.

3. Az a **kezelés** részben, a a **verziók** lapon klónozza a verziót, és adja neki `batchtest`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. A verzió nevét az URL-útvonal részeként használja, mert a név nem tartalmazhat, amelyek nem érvényes URL-karaktereket. 

4. Az alkalmazás betanításához.

## <a name="batch-file"></a>Batch-fájl

1. Hozzon létre `HumanResources-jobs-batch.json` egy szövegszerkesztő vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) azt. 

2. A JSON-formátumú parancsfájlba, az beszédmódok hozzáadása a **szándékot** azt szeretné, a teszt előre jelzett. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Futtassa a köteget

1. Válassza ki **teszt** a felső navigációs sávban. 

2. Válassza ki **Batch-tesztelési panel** a jobb oldali panelen. 

    [![Kiemelt Batch teszt panel képernyőképe a LUIS-alkalmazás](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Válassza ki **importálás adatkészlet**.

    [![Képernyőkép a LUIS alkalmazás importálása adatkészlettel kiemelésével](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Válassza ki a fájl helyét a `HumanResources-jobs-batch.json` fájlt.

5. Nevezze el az adatkészlet `intents only` válassza **kész**.

    ![Fájl kiválasztása](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Kattintson a **Futtatás** gombra. 

7. Válassza ki **eredmények megtekintéséhez**.

8. Tekintse át az eredményeket a graph és jelmagyarázat.

    [![Képernyőkép a LUIS-alkalmazás és a batch terhelésiteszt-eredményei](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

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

## <a name="fix-the-app"></a>Javítsa ki az alkalmazást

Ez a szakasz célja, hogy megcímkézzen megfelelően az előre jelzett összes **GetJobInformation** azzal, hogy az alkalmazást. 

A látszólag gyors javítás ezek batch fájl beszédmódok hozzáadása a megfelelő leképezés lenne. Nincs mit kíván tenni, ha ez. Azt szeretné, hogy a LUIS megfelelően előre, anélkül, hogy hozzáadják őket a példaként a kimondott szöveg. 

A kimondott szöveg eltávolításával kapcsolatos is vezetőnév **ApplyForJob** mindaddig, amíg az utterance (kifejezés) mennyiség megegyezik a **GetJobInformation**. Amely előfordulhat, hogy javítsa ki a vizsgálati eredmények, de a pontos előrejelzésére adott szándékot legközelebb akadályozná az intelligens HANGFELISMERÉSI. 

Az első javítást, hogy a további beszédmódok hozzáadása **GetJobInformation**. A második javítás szavakkal is, mint a súlyozást csökkentése érdekében egy `resume` és `apply` felé a **ApplyForJob** szándékot. 

### <a name="add-more-utterances"></a>További beszédmódok hozzáadása

1. Zárja be a batch-teszt panelen válassza a **tesztelése** gombot a felső navigációs panelen. 

2. Válassza ki **GetJobInformation** leképezések listájából. 

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

## <a name="verify-the-new-model"></a>Ellenőrizze az új modell

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

1. Hozzon létre `HumanResources-entities-batch.json` egy szövegszerkesztőben, például [VSCode](https://code.visualstudio.com/) vagy [letöltése](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) azt.


2. A JSON-formátumú batch fájlban adja hozzá a kimondott szöveg objektumok egy tömbjét a **szándékot** szeretné a tesztelési, valamint a helyek az utterance (kifejezés) bármely entitáslistájában előre jelzett. Mivel egy entitás jogkivonat-alapú, ügyeljen arra, hogy elindíthatja és leállíthatja a minden entitás az a karakter. Ne kezdő vagy záró szóközt az utterance (kifejezés). Ez hibát okoz a batch-fájl importálása során.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Futtassa a köteget az entitások

1. Válassza ki **teszt** a felső navigációs sávban. 

2. Válassza ki **Batch-tesztelési panel** a jobb oldali panelen. 

3. Válassza ki **importálás adatkészlet**.

4. Válassza ki a fájl rendszer helyét a `HumanResources-entities-batch.json` fájlt.

5. Nevezze el az adatkészlet `entities` válassza **kész**.

6. Kattintson a **Futtatás** gombra. Várjon, amíg a teszt történik.

7. Válassza ki **eredmények megtekintéséhez**.

## <a name="review-entity-batch-results"></a>Entity batch eredmények áttekintése

A diagram megnyílik a megfelelő előrejelzett szándék fog vonatkozni. Görgessen le a jobb oldali szűrő keresse meg a szövegértékekké entitás előrejelzéseket. 

1. Válassza ki a **feladat** szűrő entitás.

    ![A szűrő szövegértékekké entitás előrejelzések](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    A diagram változik az entitás előrejelzések megjelenítéséhez. 

2. Válassza ki **téves negatív** alsó, bal oldali quadrant a diagram. A billentyűzet kombináció CTRL + E majd használja a jogkivonat nézetre. 

    [![Token nézet és az entitás előrejelzések](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    A diagram alatt megcímkézzen megtekintésével tárja fel egy egységes hiba, amikor a feladat neve tartalmazza a `SQL`. A példa kimondott szöveg és a feladatlistában kifejezés áttekintése, SQL van csak egyszer használt, és a egy nagyobb méretű feladat neve csak részeként `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Javítsa ki az alkalmazást, entity batch eredményei alapján

Az alkalmazás kompatibilitását meg megfelelően az SQL-feladatok változata, a LUIS van szükség. Többféle módon is, hogy a javítást. 

* Explicit módon fel további példa kimondott szöveg, amely SQL használata, és azokat a szavakat címkézését feladat egységként. 
* Explicit módon további SQL-feladatok hozzáadása a kifejezéslista

Ezek a feladatok elvégzéséhez van hátra.

Hozzáadás egy [minta](luis-concept-patterns.md) előtt az entitás megfelelően előre jelzett, nem történik a probléma megoldása érdekében. Ez azért, mert a minta nem egyeznek, amíg a rendszer észleli a minta összes entitást. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Az oktatóanyag egy batch-teszt segítségével keresse meg a problémákat a jelenlegi modellel. A modell volt rögzített, és ellenőrizze a módosítás megfelelő volt-e a batch-fájllal vizsgálni.

> [!div class="nextstepaction"]
> [További információ a minták](luis-tutorial-pattern.md)

