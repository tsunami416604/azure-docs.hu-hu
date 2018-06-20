---
title: Kötegelt tesztelési célra használható LUIS előrejelzéseket javítására |} Microsoft Docs
titleSuffix: Azure
description: Kötegelt terheléstesztet, tekintse át az eredményeket, és javíthatja a LUIS előrejelzéseket módosításokkal.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266396"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Kötegelt tesztelési célra használható található előrejelzés pontosságát problémák

Ez az oktatóanyag bemutatja, hogyan kell utterance előrejelzés problémák keresése a kötegelt tesztelési célra használható.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
* Hozzon létre egy kötegfájlt tesztelése 
* A kötegelt teszt futtatása
* Ellenőrizze a teszteredmények
* Javítsa a hibákat a leképezések
* A kötegelt ellenőrzése hosszadalmas

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Ebből a cikkből is van szüksége egy [LUIS][LUIS] fiók ahhoz, hogy a LUIS alkalmazás létrehozásához.

> [!Tip]
> Ha még nem rendelkezik előfizetéssel, akkor a regisztrálhatja a [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Új alkalmazás létrehozása
Ez a cikk az előre elkészített tartományt HomeAutomation használja. Az előre elkészített tartomány leképezések, entitásokat és HomeAutomation eszközök, például fény szabályozásának utterances rendelkezik. A tartomány, az alkalmazás létrehozását, betanítását és közzététele.

1. Az a [LUIS] webhelyet, hozzon létre egy új alkalmazást kiválasztásával **hozzon létre új alkalmazás** a a **MyApps** lap. 

    ![Új alkalmazás létrehozása](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Adja meg a nevét `Batchtest-HomeAutomation` párbeszédpanelen.

    ![Adja meg az alkalmazás neve](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Válassza ki **előre elkészített tartományok** bal alsó sarokban. 

    ![Előre elkészített tartomány kiválasztása](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Válassza ki **tartomány hozzáadása** HomeAutomation számára.

    ![HomeAutomation tartomány hozzáadása](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Válassza ki **vonat** a jobb felső navigációs sávban.

    ![Válassza ki a vonat gomb](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Kötegelt teszt feltételek
Kötegelt tesztelése, tesztelheti egyszerre legfeljebb 1000 utterances. A köteg nem lehetnek duplikált elemek. [Exportálás](create-new-app.md#export-app) az alkalmazás aktuális utterances listájának megjelenítéséhez.  

LUIS teszt stratégiája használ az adatok három különálló további: a modell utterances, a kötegelt teszt utterances és a végpont utterances. Ebben az oktatóanyagban győződjön meg arról, hogy a utterances vagy modell utterances (megjelölésű hozzá) származó, vagy a végpont utterances nem használ. 

Ne használja a utterances bármelyike már az alkalmazásban a kötegelt vizsgálathoz:

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

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Hozzon létre egy kötegelt leképezési előrejelzés pontosságát tesztelése
1. Hozzon létre `homeauto-batch-1.json` egy szövegszerkesztőben, például [VSCode](https://code.visualstudio.com/). 

2. Adja hozzá a utterances a **leképezés** azt szeretné, a teszt előre jelzett. Ebben az oktatóanyagban abba, hogy egyszerű, végezze el utterances szereplő a `HomeAutomation.TurnOn` és `HomeAutomation.TurnOff` és egy kapcsolót a `on` és `off` a utterances szöveget. Az a `None` leképezés, adja hozzá, amelyek nincsenek utterances néhány része a [tartomány](luis-glossary.md#domain) (tulajdonos) területen. 

    Ahhoz, hogy megértse, hogyan a kötegelt teszteredmények gyűjtött a kötegelt JSON, vegye fel a csak hat leképezések.

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

## <a name="run-the-batch"></a>A kötegelt futtatása
1. Válassza ki **teszt** a felső navigációs sávban. 

    ![Válassza ki a teszt a navigációs sáv](./media/luis-tutorial-batch-testing/test-1.png)

2. Válassza ki **tesztelési panel kötegelt** a jobb oldali panelen. 

    ![Válassza ki a kötegelt teszt panel](./media/luis-tutorial-batch-testing/test-2.png)

3. Válassza ki **importálási dataset**.

    ![Importálás adatkészlet kiválasztása](./media/luis-tutorial-batch-testing/test-3.png)

4. Válassza ki a fájl rendszer helyét a `homeauto-batch-1.json` fájlt.

5. A DataSet adatkészlet neve `set 1`.

    ![Fájl kiválasztása](./media/luis-tutorial-batch-testing/test-4.png)

6. Kattintson a **Futtatás** gombra. Várjon, amíg a vizsgálat történik.

    ![Válassza a Futtatás](./media/luis-tutorial-batch-testing/test-5.png)

7. Válassza ki **eredményeket**.

    ![Eredmények megtekintése](./media/luis-tutorial-batch-testing/test-6.png)

8. Tekintse át a grafikon és jelmagyarázat eredményez.

    ![Kötegelt eredmények](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Kötegelt eredményeinek áttekintése
A kötegelt eredményei két szakaszban. A felső rész tartalmazza a diagram és jelmagyarázat. Az alsó részén utterances jeleníti meg, amikor kiválaszt egy gráf diagramterületnek a neve.

A vörös színt ki a hibákat jelzik. A diagramhoz négy részből áll, és két pirossal részeiből van. **Ezek a szakaszok összpontosíthat a a**. 

Jobb felső sarokban a szakasz azt jelzi, a teszt megfelelően előre jelezni, hogy létezik-e egy leképezés vagy entitás. A bal alsó részén azt jelzi, hogy a teszt helytelenül egy leképezés vagy entitás hiányában előre jelezni.

### <a name="homeautomationturnoff-test-results"></a>HomeAutomation.TurnOff teszt eredményei
A jelmagyarázat, válassza ki a `HomeAutomation.TurnOff` leképezés. A jelmagyarázatban rendelkezik egy zöld sikeres ikonra a nevétől balra található. Ez a leképezés a hibák nem találhatók. 

![Kötegelt eredmények](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn és "nincs" leképezések rendelkezik hibák
A többi két leképezések hibák, ami azt jelenti, a teszt-előrejelzés nem egyeztek a kötegelt fájl verziójával kapcsolatos elvárások rendelkezik. Válassza ki a `None` leképezési, hogy tekintse át az első hiba a jelmagyarázatban. 

![Nincs leképezési](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Hibák jelennek meg a diagram a piros szakaszokban: **téves pozitív** és **hamis negatív**. Válassza ki a **hamis negatív** szakasznév a diagram a diagram alá sikertelen utterances megjelenítéséhez. 

![Negatív a hibákra](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

A hibás utterance `help` várta a `None` leképezés, de a teszt előre jelezni `HomeAutomation.TurnOn` leképezés.  

Nincsenek két hibák, HomeAutomation.TurnOn egyet-egyet, és egy-egy None. Mind a utterance által okozott `help` , mert nem felel meg a várt eredmény sincs, és egy nem várt egyezik a HomeAutomation.TurnOn célt a volt. 

Hogy miért a `None` utterances sikertelen, tekintse át a jelenleg a utterances `None`. 

## <a name="review-none-intents-utterances"></a>Felülvizsgálati nincs leképezési utterances végpontjára

1. Zárja be a **teszt** kiválasztásával panel a **teszt** a felső navigációs sáv gombjára. 

2. Válassza ki **Build** a felső navigációs panelen. 

3. Válassza ki **nincs** leképezési leképezések listája.

4. Válassza ki a vezérlő + E a utterances token megtekintéséhez 
    
    |Nincs leképezési utterances végpontjára|Előrejelzés pontszám|
    |--|--|
    |"csökkentése hőmérséklet számomra adjon"|0,44|
    |"25 konyhai fény dim."|0.43|
    |"a kötet alacsonyabb"|0.46|
    |"a szobája az internet bekapcsolása"|0.28|

## <a name="fix-none-intents-utterances"></a>Javítsa ki nincs leképezési utterances végpontjára
    
Az összes utterances `None` kívül az alkalmazástartomány elvileg. Ezek utterances vannak viszonyítva HomeAutomation, így a nem megfelelő leképezés. 

LUIS is biztosít a utterances, amely kisebb, mint 50 % (<.50) előrejelzés pontszámot. A többi két leképezések a utterances tekinti meg, ha sokkal nagyobb előrejelzés pontszámok láthatja. Ha LUIS példa utterances, amely jól jelzi az alacsony pontszámait a utterances között az aktuális szándékot és egyéb leképezések LUIS zavaró. 

Az alkalmazást, és jelenleg a utterances javításához a `None` leképezés kell helyezhetők át a megfelelő cél és a `None` leképezés van szüksége, új, megfelelő leképezések. 

Három a utterances a `None` leképezés van kialakítva, hogy az automatizálási eszköz beállításait. Szavak használják, mint `dim`, `lower`, vagy `decrease`. A negyedik utterance megkérdezi, kapcsolja be az interneten. Mivel minden négy utterances bekapcsolásával vagy power fokának módosítása a egy eszközt, akkor kell áthelyezni a `HomeAutomation.TurnOn` leképezés. 

Ez az egyetlen megoldás. Is létrehozhat egy új szándékával `ChangeSetting` helyezze át a utterances használatával dim, alsó, és csökkenti az, hogy új leképezés. 

## <a name="fix-the-app-based-on-batch-results"></a>Javítsa ki az alkalmazás kötegelt eredmények alapján
A négy utterances történő áthelyezése a `HomeAutomation.TurnOn` leképezés. 

1. A utterance lista fölött jelölőnégyzet bejelölésével, így minden utterances van kiválasztva. 

2. Az a **ismételt hozzárendelése leképezés** legördülő listából válassza `HomeAutomation.TurnOn`. 

    ![Helyezze át a utterances](./media/luis-tutorial-batch-testing/move-utterances.png)

    A négy utterances hozzárendelését, miután a utterance listázása a `None` célja üres.

3. Négy új leképezések hozzáadása a nincs leképezési:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Ezek utterances mindenképpen HomeAutomation tartományán kívül vannak. Minden utterance ad meg, tekintse meg a pontszám hozzá. A pontszám alacsony, vagy akár nagyon alacsony (a vörös téglalappal köré) lehet. Az alkalmazást, és 8. lépésben a vonat a pontszám sokkal nagyobb lesz. 

7. Távolítsa el a címkéket a utterance, és válasszon a kék címke kiválasztásával **Remove label**.

8. Válassza ki **vonat** a jobb felső navigációs sávban. Az egyes utterance pontszám, sokkal nagyobb. Az összes pontszámait a `None` leképezés fölött van.80 most. 

## <a name="verify-the-fix-worked"></a>Ellenőrizze, hogy a javítás dolgozott
Annak ellenőrzéséhez, hogy a batch-teszt utterances megfelelően előre a a **nincs** leképezés, futtassa újra a kötegelt vizsgálat.

1. Válassza ki **teszt** a felső navigációs sávban. 

2. Válassza ki **tesztelési panel kötegelt** a jobb oldali panelen. 

3. Válassza ki a három pont (…) jobb oldalán a neve, majd **adatkészlet futtatásához**. Várjon, amíg a kötegelt teszt történik.

    ![A dataset futtatása](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Válassza ki **eredményeket**. A leképezések összes kell zöld ikonok bal oldalán található a leképezési nevét. A jobb oldali szűrővel beállítása a `HomeAutomation.Turnoff` leképezés, jelölje be a zöld pont a felső legközelebbi közepén levő a diagram jobb oldali panelen. A diagram az alábbi táblázatban a utterance neve jelenik meg. A pontszám `breezeway off please` nagyon alacsony. Egy nem kötelező tevékenysége több utterances hozzáadása a célt a pontszám növelése érdekében. 

    ![A dataset futtatása](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

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

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók példa utterances](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions