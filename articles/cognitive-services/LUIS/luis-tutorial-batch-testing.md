---
title: Batch tesztelés használatával javíthatja a LUIS-előrejelzés |} A Microsoft Docs
titleSuffix: Azure
description: Batch terhelésteszt eredmények áttekintése és módosítása a LUIS-előrejelzések javítása.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/06/2018
ms.author: v-geberr
ms.openlocfilehash: b695783c6d68876d39482ed5abec24f45087603d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054862"
---
# <a name="improve-app-with-batch-test"></a>A batch-teszt alkalmazás fejlesztéséhez

Ez az oktatóanyag bemutatja, hogyan utterance (kifejezés) előrejelzési problémák kereséséhez használja a batch-tesztelés.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

<!-- green checkmark -->
> [!div class="checklist"]
* Hozzon létre egy kötegfájlt teszt 
* Egy batch-teszt futtatása
* Vizsgálati eredmények áttekintése
* Javítsa a hibákat a szándék
* A batch ellenőrzése hosszadalmas

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md#luis-website)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha nem rendelkezik az emberi erőforrások alkalmazásból a [tekintse át a végpont utterances](luis-tutorial-review-endpoint-utterances.md) az oktatóanyagban [importálása](luis-how-to-start-new-app.md#import-new-app) a JSON-kódot egy új alkalmazást a [LUIS](luis-reference-regions.md#luis-website) webhely. Az importálandó alkalmazás a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json) GitHub-adattárban található.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `batchtest`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="purpose-of-batch-testing"></a>A batch tesztelési célra
A Batch tesztelése lehetővé teszi, hogy ellenőrizze a modellállapot ismert vizsgálati utterances vannak beállítva, és entitások címkével. A JSON-formátumú parancsfájlba a beszédmódok hozzáadása, és állítsa be az entitás címkék van szüksége, előre meghatározott az utterance (kifejezés) belül. 

A LUIS ajánlott tesztelési stratégiája használja az adatok három különálló csoportok: a modell, a batch teszt kimondott szöveg és a végpont kimondott szöveg a megadott példa kimondott szöveg. A jelen oktatóanyag esetében győződjön meg arról, hogy nem használ a kimondott szöveg vagy példa utterances (megjelölésű hozzá) a, vagy a végpont kimondott szöveg. 

A példa kimondott szöveg és a végpont utterances, szemben a batch teszt kimondott szöveg ellenőrzése [exportálása](luis-how-to-start-new-app.md#export-app) az alkalmazás és [letöltése](luis-how-to-start-new-app.md#export-endpoint-logs) a lekérdezési napló. Hasonlítsa össze az alkalmazás például utterance és batch teszt megcímkézzen lekérdezési napló kimondott szöveg. 

Batch-tesztelés vonatkozó követelmények:

* 1000 tesztenként kimondott szöveg. 
* Nincsenek ismétlődések. 
* Engedélyezett entitástípusra: egyszerű és összetett.

## <a name="create-a-batch-file-with-utterances"></a>Hozzon létre egy kötegfájlt a kimondott szöveg
1. Hozzon létre `HumanResources-jobs-batch.json` egy szövegszerkesztőben, például [VSCode](https://code.visualstudio.com/). 

2. A JSON-formátumú parancsfájlba, az beszédmódok hozzáadása a **szándékot** azt szeretné, a teszt előre jelzett. 

    ```JSON
    [
        {
        "text": "Are there any janitorial jobs currently open?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "I would like a fullstack typescript programming with azure job",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Is there a database position open in Los Colinas?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Can I apply for any database jobs with this resume?",
        "intent": "GetJobInformation",
        "entities": []
        }
    ]
    ```

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
A batch diagram jeleníti meg az eredmények négy elemzésében. A diagram jobb szűrő van. Alapértelmezés szerint a szűrő értéke az első célja a listában. A szűrő tartalmazza, a leképezések és a hierarchikus csak egyszerű (szülő csak), és összetett entitásokat. Amikor kiválaszt egy szakasz a diagram vagy a diagramon belül a pontra, a társított utterance(s) a diagram alatt jelennek meg. 

A diagram rámutatáskor egérkereket nagyítása vagy csökkentheti a megjelenített a diagramon. Ez akkor hasznos, ha a fürtözött szorosan együtt diagramra hány pontot. 

A diagram van négy elemzésében, és két a szakaszok vörös színnel jelenik meg. **Ezek a szakaszok a fókusz a a**. 

### <a name="applyforjob-test-results"></a>ApplyForJob terhelésiteszt-eredményei
A **ApplyForJob** terhelésiteszt-eredményei jelennek meg a szűrő megjelenítése, hogy sikeres volt-e a négy előrejelzéseket az 1. Válassza ki a nevét **vakriasztás** megtekintéséhez a diagram alatt megcímkézzen felső megfelelő quadrant felett. 

![A LUIS batch teszt kimondott szöveg](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Három megcímkézzen kellett egy felső szándéka **ApplyForJob**. A batch-fájlban megadott célja egy alacsonyabb pontszámok rendelkezett. Miért volt történik ez? A két szándék szorosan kapcsolódó szó választási lehetőség és a word elrendezésének tekintetében. Emellett nincsenek majdnem három alkalommal annyi példák **ApplyForJob** mint **GetJobInformation**. Ez a példa kimondott szöveg egyenetlenség tömege **ApplyForJob** leképezés a hónappal számolunk. 

Figyelje meg, hogy mindkét leképezések rendelkezik-e hibák azonos száma: 

![A LUIS batch tesztelési hibák szűrése](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

A felső időponthoz tartozó az utterance (kifejezés) a **vakriasztás** szakasz `Can I apply for any database jobs with this resume?`. A word `resume` csak a felhasznált **ApplyForJob**. 

A másik két pont a diagram sokkal alacsonyabb értékeket a megfelelő szándékkal, ami azt jelenti, azok a megfelelő leképezés közelebb rendelkezett. 

## <a name="fix-the-app-based-on-batch-results"></a>Javítsa ki az alkalmazást, a batch eredményei alapján
Ez a szakasz célja, hogy helytelenül voltak előre jelzett, a három megcímkézzen rendelkezik **ApplyForJob** , megfelelően kell elvégezni, a **GetJobInformation**, az alkalmazás kijavítása után. 

A látszólag gyors javítás ezek batch fájl beszédmódok hozzáadása a megfelelő leképezés lenne. Nincs mit kíván tenni, ha ez. Azt szeretné, hogy a LUIS megfelelően előre, anélkül, hogy hozzáadják őket a példaként a kimondott szöveg. 

A kimondott szöveg eltávolításával kapcsolatos is vezetőnév **ApplyForJob** mindaddig, amíg az utterance (kifejezés) mennyiség megegyezik a **GetJobInformation**. Amely előfordulhat, hogy javítsa ki a vizsgálati eredmények, de a pontos előrejelzésére adott szándékot legközelebb akadályozná az intelligens HANGFELISMERÉSI. 

Az első javítást, hogy a további beszédmódok hozzáadása **GetJobInformation**. A második javítás az, hogy csökkentse a word súlyát `resume` felé a **ApplyForJob** szándékot. 

### <a name="add-more-utterances-to-getjobinformation"></a>A további beszédmódok hozzáadása **GetJobInformation**
1. Zárja be a batch-teszt panelen válassza a **tesztelése** gombot a felső navigációs panelen. 

    [ ![Képernyőkép a LUIS teszt gomb kiemelésével](./media/luis-tutorial-batch-testing/hr-close-test-panel.png)](./media/luis-tutorial-batch-testing/hr-close-test-panel.png#lightbox)

2. Válassza ki **GetJobInformation** leképezések listájából. 

    [ ![Képernyőkép a LUIS teszt gomb kiemelésével](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png)](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png#lightbox)

3. Több, különböző hossza, a word választási lehetőség és a word-előkészítés, olyan a feltételek feltétlenül beszédmódok hozzáadása `resume` és `c.v.`:

    ```JSON
    Is there a new job in the warehouse for a stocker?
    Where are the roofing jobs today?
    I heard there was a medical coding job that requires a resume.
    I would like a job helping college kids write their c.v.s. 
    Here is my resume, looking for a new post at the community college using computers.
    What positions are available in child and home care?
    Is there an intern desk at the newspaper?
    My C.v. shows I'm good at analyzing procurement, budgets, and lost money. Is there anything for this type of work?
    Where are the earth drilling jobs right now?
    I've worked 8 years as an EMS driver. Any new jobs?
    New food handling jobs?
    How many new yard work jobs are available?
    Is there a new HR post for labor relations and negotiations?
    I have a masters in library and archive management. Any new positions?
    Are there any babysitting jobs for 13 year olds in the city today?
    ```

4. Az alkalmazás betanításához kiválasztásával **Train** a jobb felső navigációs.

## <a name="verify-the-fix-worked"></a>Ellenőrizze, hogy a javítás működött
Annak érdekében, hogy ellenőrizze, hogy a batch-teszt megcímkézzen megfelelően előre jelzett, futtassa újra a batch-vizsgálat.

1. Válassza ki **teszt** a felső navigációs sávban. Ha a batch-eredmények meg nyitva, válassza ki a **vissza a listához**.  

2. Kattintson a három pontra (***...*** ) gombot a batch nevétől jobbra **futtatása adatkészlet**. Várjon, amíg a batch-teszt történik. Figyelje meg, hogy a **eredmények megtekintéséhez** már zöld gombot. Ez azt jelenti, hogy az egész köteget sikeresen lefutott.

3. Válassza ki **eredmények megtekintéséhez**. A leképezések összes kell zöld ikonok a leképezés neve a bal oldalon. 

    [ ![Képernyőkép a LUIS batch eredmények gomb kiemelésével](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png#lightbox)


## <a name="what-has-this-tutorial-accomplished"></a>Mi van ebben az oktatóanyagban történik?
Az alkalmazás előrejelzési pontosság hibák keresése a Batch és a modell javításának további példa utterances ad hozzá a megfelelő leképezés és képzési nőtt. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Válassza a **My apps** (Saját alkalmazások) elemet a bal oldali menüben. Kattintson a három pontra **...**  az alkalmazások listájában, az alkalmazás nevétől jobbra, válassza ki a **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a minták](luis-tutorial-pattern.md)

