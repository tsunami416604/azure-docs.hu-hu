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
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 27d6bbc628ac3183032a90d8f3ad98998c76a957
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888830"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Előrejelzési pontosság problémák kereséséhez használja a batch tesztelése

Ez az oktatóanyag bemutatja, hogyan utterance (kifejezés) előrejelzési problémák kereséséhez használja a batch-tesztelés.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Hozzon létre egy kötegfájlt teszt 
* Egy batch-teszt futtatása
* Vizsgálati eredmények áttekintése
* Javítsa a hibákat a szándék
* A batch ellenőrzése hosszadalmas

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Ez a cikk is szüksége lesz egy [LUIS](luis-reference-regions.md) fiók annak érdekében, hogy a LUIS-alkalmazás létrehozásához.

> [!Tip]
> Ha Ön még nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Új alkalmazás létrehozása
Ez a cikk az előre összeállított tartomány HomeAutomation használja. Az előre összeállított tartományban van, szándék fog vonatkozni, az entitások és HomeAutomation eszközök, például lámpa való kimondott szöveg. A tartomány az alkalmazás létrehozását, betanítását és közzététele.

1. Az a [LUIS](luis-reference-regions.md) webhely, hozzon létre egy új alkalmazást kiválasztásával **új alkalmazás létrehozása** a a **MyApps** lapot. 

    ![Új alkalmazás létrehozása](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Adja meg a nevét `Batchtest-HomeAutomation` a párbeszédpanelen.

    ![Adja meg az alkalmazás neve](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Válassza ki **előre összeállított tartományok** a bal alsó sarokban. 

    ![Előre összeállított tartomány kiválasztása](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Válassza ki **tartomány hozzáadása** HomeAutomation számára.

    ![HomeAutomation tartomány hozzáadása](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Válassza ki **Train** a jobb oldali navigációs sáv tetején.

    ![Válassza ki a tanítási gomb](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>A Batch tesztkritériumban
Batch tesztelés tesztelheti egyszerre legfeljebb 1000 kimondott szöveg. A batch nem rendelkezhet duplikált elemeket. [Exportálás](create-new-app.md#export-app) az alkalmazást, annak érdekében, hogy a jelenlegi utterances listájának megtekintéséhez.  

A LUIS teszt stratégiát használ három különálló adatkészletek: modell utterances, a batch teszt kimondott szöveg és a végpont kimondott szöveg. Ebben az oktatóanyagban győződjön meg arról, hogy nem használja a kimondott szöveg vagy modell utterances (megjelölésű hozzá) a, vagy a végpont kimondott szöveg. 

Ne használja a kimondott szöveg bármelyike már az alkalmazásban a batch-teszt:

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Hozzon létre egy batch szándék előrejelzési pontosság tesztelése
1. Hozzon létre `homeauto-batch-1.json` egy szövegszerkesztőben, például [VSCode](https://code.visualstudio.com/). 

2. A beszédmódok hozzáadása a **szándékot** azt szeretné, a teszt előre jelzett. A jelen oktatóanyag esetében legyen egyszerű, vesz utterances a `HomeAutomation.TurnOn` és `HomeAutomation.TurnOff` , és váltson a `on` és `off` a kimondott szöveg szövegére. Az a `None` szándékkal, pár, amelyek nem beszédmódok hozzáadása része a [tartomány](luis-glossary.md#domain) (tárgy) területre. 

    Annak érdekében, hogy érti, hogyan a batch-vizsgálati eredmények korrelációját, ha a Batch JSON, legfeljebb hat leképezések hozzáadása.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Futtassa a köteget
1. Válassza ki **teszt** a felső navigációs sávban. 

    ![A navigációs sávban válassza ki a teszt](./media/luis-tutorial-batch-testing/test-1.png)

2. Válassza ki **Batch-tesztelési panel** a jobb oldali panelen. 

    ![Válassza ki a Batch-teszt panel](./media/luis-tutorial-batch-testing/test-2.png)

3. Válassza ki **importálás adatkészlet**.

    ![Importálás adatkészlet kiválasztása](./media/luis-tutorial-batch-testing/test-3.png)

4. Válassza ki a fájl rendszer helyét a `homeauto-batch-1.json` fájlt.

5. Nevezze el az adatkészlet `set 1`.

    ![Fájl kiválasztása](./media/luis-tutorial-batch-testing/test-4.png)

6. Kattintson a **Futtatás** gombra. Várjon, amíg a teszt történik.

    ![Válasszon futtató](./media/luis-tutorial-batch-testing/test-5.png)

7. Válassza ki **eredmények megtekintéséhez**.

    ![Eredmények megtekintése](./media/luis-tutorial-batch-testing/test-6.png)

8. Tekintse át az eredményeket a graph és jelmagyarázat.

    ![Batch-eredmények](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Batch-eredmények áttekintése
A batch-eredmények két szakaszokban találhatók. A felső rész tartalmazza a graph és a jelmagyarázat. Az alsó szakaszt utterances jeleníti meg, amikor kiválaszt egy a gráf neve.

Hibák piros szín jelzi. A gráf van négy részből áll, és két a szakaszok vörös színnel jelenik meg. **Ezek a szakaszok a fókusz a a**. 

A szakasz azt jelzi, hogy a teszt nem megfelelően jobb felső sarokban előre jelzett-leképezés vagy entitás létezik-e. A bal alsó részén azt jelzi, hogy a teszt nem megfelelően előre jelzett-leképezés vagy entitás hiányában.

### <a name="homeautomationturnoff-test-results"></a>HomeAutomation.TurnOff terhelésiteszt-eredményei
A jelmagyarázat, válassza ki a `HomeAutomation.TurnOff` szándékot. Egy zöld, sikeres ikonra a bal oldalon a név tartalmaz a jelmagyarázatban. Nincsenek a leképezés a hibák. 

![Batch-eredmények](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn, és nincs leképezések hibásak
A két szándék más hibák, ami azt jelenti, a teszt előrejelzéseket nem egyeztek a batch fájl verziójával kapcsolatos elvárások rendelkezik. Válassza ki a `None` szándék, tekintse át az első hiba a jelmagyarázatban. 

![Nincs leképezés](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Hibák jelennek meg a diagram a piros szakaszokban: **hamis pozitív** és **téves negatív**. Válassza ki a **téves negatív** szakasz nevét a diagram alatt sikertelen megcímkézzen megtekintéséhez a diagramban. 

![Téves negatív hibák](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

A sikertelen utterance (kifejezés), `help` várta, mint egy `None` szándékot, de az előre jelzett teszt `HomeAutomation.TurnOn` szándékot.  

Nincsenek két hibák, egy HomeAutomation.TurnOn a, és egy sem található. Mindkettő az utterance (kifejezés) által okozott `help` , mert nem felel meg az elvárás a None, és a egy váratlan pontosan megegyezik az HomeAutomation.TurnOn célja volt. 

Ezért meghatározni a `None` utterances nem működik, tekintse át a jelenleg megcímkézzen `None`. 

## <a name="review-none-intents-utterances"></a>Felülvizsgálat nincs szándék a kimondott szöveg

1. Zárja be a **teszt** panelen válassza a **teszt** gombot a felső navigációs sávon. 

2. Válassza ki **összeállítása** a felső navigációs panelen. 

3. Válassza ki **nincs** szándék listájában leképezéseket.

4. Válassza ki a Control + E megcímkézzen token megtekintéséhez 
    
    |Nincs leképezés a kimondott szöveg|Előrejelzési pontszám|
    |--|--|
    |"csökkentése hőmérséklet számomra."|0,44|
    |"(dimenzió) megjelenítő kijelzőket a konyhai lámpa 25."|0.43|
    |"a kötet alsó"|0.46|
    |"az interneten, a saját szobája kérjük bekapcsolása"|0,28|

## <a name="fix-none-intents-utterances"></a>Javítsa ki nincs szándék a kimondott szöveg
    
A megszólalásokat `None` alkalmazástartomány-en kívül kell maradniuk. Ezek a kimondott szöveg olyan viszonyított HomeAutomation, így azokat a nem megfelelő leképezés. 

A LUIS is biztosít a kimondott szöveg, amely kisebb, mint 50 %-os (<.50) előrejelzési pontszámot. Ha a többi a két szándék megcímkézzen tekinti meg, sokkal nagyobb előrejelzési pontszámok jelenik meg. Ha LUIS alacsony értékeket, hogy jól jelzi példa megcímkézzen megcímkézzen zavaró, LUIS, a jelenlegi leképezés és más leképezések között. 

Az alkalmazás jelenleg megcímkézzen javításához a `None` szándékot helyezhetők át a helyes leképezést kell és a `None` leképezést kell új, a megfelelő leképezések. 

Három a megcímkézzen a `None` szándékot úgy van kialakítva, hogy az automation-eszközbeállítások csökkentheti. Például szavak használata `dim`, `lower`, vagy `decrease`. A negyedik utterance (kifejezés) megkérdezi, hogy kapcsolja be az interneten. Mivel az összes négy utterances bekapcsolása vagy erejének módosítása egy eszközhöz, azok kell áthelyezni a `HomeAutomation.TurnOn` szándékot. 

Ez az egyetlen megoldás. Akkor is létrehozhat egy új szándéka `ChangeSetting` és helyezze át a kimondott szöveg használatával (dimenzió), csökkentheti, és be, hogy új szándékot csökkentéséhez. 

## <a name="fix-the-app-based-on-batch-results"></a>Javítsa ki az alkalmazást, a batch eredményei alapján
Helyezze át a négy kimondott szöveg a `HomeAutomation.TurnOn` szándékot. 

1. Jelölje be a jelölőnégyzetet az utterance (kifejezés) lista fölött, így minden kimondott szöveg van kijelölve. 

2. Az a **újbóli hozzárendelése a leképezés** legördülő menüben válassza `HomeAutomation.TurnOn`. 

    ![Helyezze át a kimondott szöveg](./media/luis-tutorial-batch-testing/move-utterances.png)

    Négy megcímkézzen hozzárendelését, miután az utterance (kifejezés) listázása a `None` célja a üres.

3. Négy új leképezések hozzáadása a nincs szándék számára:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Ezek a kimondott szöveg mindenképp HomeAutomation tartományán kívül van. Minden kimondásakor meg, tekintse meg a pontszám. A pontszám alacsony vagy még nagyon alacsony (és a piros Keretes azt) lehet. Miután az alkalmazást, a 8. lépésében a vonat a pontszám sokkal magasabb lesz. 

7. Távolítsa el a címkék az utterance (kifejezés), és válassza a kék címke kiválasztásával **Remove label**.

8. Válassza ki **Train** a jobb oldali navigációs sáv tetején. A pontszám minden kimondásakor, sokkal magasabb. Az összes értékeket a `None` szándékot fölött van.80 most. 

## <a name="verify-the-fix-worked"></a>Ellenőrizze, hogy a javítás működött
Annak érdekében, hogy ellenőrizze, hogy a batch-teszt megcímkézzen megfelelően előrejelzett számára a **nincs** szándékkal, futtassa újra a batch-vizsgálat.

1. Válassza ki **teszt** a felső navigációs sávban. 

2. Válassza ki **Batch-tesztelési panel** a jobb oldali panelen. 

3. Kattintson a három pontra (***...*** ) gombot a batch nevétől jobbra **futtatása adatkészlet**. Várjon, amíg a batch-teszt történik.

    ![Futtassa az adatkészlet](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Válassza ki **eredmények megtekintéséhez**. A leképezések összes kell zöld ikonok a leképezés neve a bal oldalon. A jobb oldali szűrővel, állítsa a `HomeAutomation.Turnoff` szándékkal, válassza ki a zöld pont a legközelebbi a diagram közepét felső jobb oldali panelen. Az utterance (kifejezés) nevét a táblázatban a diagram alatt jelenik meg. A pontszám `breezeway off please` nagyon alacsony. Egy nem kötelező tevékenységet, további beszédmódok hozzáadása célja, hogy növelni ezt az értéket. 

    ![Futtassa az adatkészlet](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the ellipsis (***...***) button at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a példa kimondott szöveg](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions