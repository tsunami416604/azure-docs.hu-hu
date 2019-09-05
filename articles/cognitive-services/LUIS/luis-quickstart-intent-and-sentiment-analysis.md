---
title: 'Oktatóanyag: Hangulat elemzése – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban hozzon létre egy alkalmazást, amely bemutatja, hogyan szerezhet be pozitív, negatív és semleges érzést a hosszúságú kimondott szöveg. A hangulat meghatározása a teljes kimondott szövegből történik.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: ed403e3d761b32c6837eb8e72edef3f3e6380217
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307575"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Oktatóanyag:  A Kimondás érzésének beolvasása

Ebben az oktatóanyagban egy olyan alkalmazást hoz létre, amely bemutatja, hogyan határozható meg a hosszúságú kimondott szöveg pozitív, negatív és semleges hangulata. A hangulat meghatározása a teljes kimondott szövegből történik.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Új alkalmazás létrehozása
> * Hangulatelemzés hozzáadása közzétételi beállításként
> * Alkalmazás betanítása
> * Alkalmazás közzététele
> * Kimondott szöveg hangulatának lekérése végpontból

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Az érzelmek elemzése egy közzétételi beállítás

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Hangulat|Pontszám|Kimondott szöveg|
|:--|:--|:--|
|pozitív|0,91 |John W. Smith remek prezentációt tartott Párizsban.|
|pozitív|0,84 |A Seattle-mérnökök mesés munkát hajtottak a Parker Sales pályán.|

A hangulatelemzés egy olyan közzétételi beállítás, amely minden kimondott szövegre vonatkozik. Nem kell megkeresnie azokat a szavakat, amelyek a véleményeket jelzik, és megjelölik őket. 

Mivel ez közzétételi beállítás, a szándékok vagy az entitások oldalán nem jelenik meg; az [interaktív teszt](luis-interactive-test.md#view-sentiment-results) panelen vagy a végpont URL-címén való teszteléskor látható. 


## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>PersonName előre összeépített entitás hozzáadása 

1. Válassza a **Létrehozás** lehetőséget a navigációs menüből.

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben.

1. Válassza az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) gombot.

1. Válassza ki a következő entitást az előre elkészített entitások listájából, majd válassza a **kész**lehetőséget:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Hozzon létre egy szándékot az alkalmazottak visszajelzésének meghatározásához

Adjon hozzá egy új szándékot a vállalat tagjaitól származó alkalmazotti visszajelzések rögzítéséhez. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

1. Adja meg az új szándék nevét: `EmployeeFeedback`.

    ![Hozzon lére egy új, EmployeeFeedback nevű szándék párbeszédpanelt](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adja hozzá több kimondott szöveget, amelyek azt jelzik, hogy az alkalmazott valamit jól végzett el, vagy amelyek olyan területet jelölnek, ahol fejlődni kell:

    |Beszédmódok|
    |--|
    |John Smith szép feladatot tett, hogy üdvözölje a munkatársat a szülési szabadságból|
    |Jill Jones nagyszerű feladatot tett a munkatársa kényelmében a gyász idején.|
    |Bob Barnes nem rendelkezett a papírmunkához szükséges összes számlával.|
    |Todd Thomas megfordult a szükséges űrlapokon egy hónapig, aláírás nélkül|
    |Katherine Kelly nem tette meg a fontos marketing-helyek közötti értekezletet.|
    |Denise Dillard elmulasztotta a találkozót a júniusi felülvizsgálatokhoz.|
    |A Harvardon található értékesítési szurok megjelölése|
    |Walter Williams nagyszerű feladatot tett a bemutatóban a Stanfordnál|

    Válassza ki a **megtekintési beállításokat**, válassza az **entitás értékeinek megjelenítése** lehetőséget a nevek megtekintéséhez.

    [![Képernyőkép a LUIS-alkalmazásról a EmployeeFeedback szándékban található példa hosszúságú kimondott szöveg](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Alkalmazás konfigurálása hangulatelemzés belefoglalásához

1. Válassza a **Manage** (Kezelés) lehetőséget a jobb felső navigációs területen, majd a **Publish settings** (Közzétételi beállítások) elemet a bal oldali menüben.

1. Válassza a **hangulat elemzése lehetőséget annak megállapításához, hogy a felhasználó abszolút értéke pozitív, negatív vagy semleges.** a beállítás engedélyezéséhez. 

    ![Hangulatelemzés bekapcsolása közzétételi beállításai](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>A végpontból való Kimondás érzésének beolvasása

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Nyissa meg a címet az URL-cím végére, és adja meg a következő értéket:

    `Jill Jones work with the media team on the public portal was amazing` 

    Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia kinyert hangulatelemzéssel: `EmployeeFeedback`.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    A sentimentAnalysis pozitív, 86%-os pontszámmal. 

    Próbáljon ki egy másikat, ha `q` eltávolítja a böngésző címsorában található értéket: `William Jones did a terrible job presenting his ideas.`Az érzelmek pontszáma negatív eredményt jelez, ha alacsony pontszámot `0.18597582`ad vissza.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* A hangulat elemzését a kognitív szolgáltatás [text Analytics](../Text-Analytics/index.yml). A funkció Text Analytics [támogatott nyelvekre](luis-language-support.md##languages-supported)korlátozódik.
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag közzétételi beállításként hozzáadja a hangulatelemzést, amellyel átfogó hangulatértékek nyerhetők ki a kimondott szövegből.

> [!div class="nextstepaction"] 
> [Végponti kimondott szövegek áttekintése az Emberi erőforrás alkalmazásban](luis-tutorial-review-endpoint-utterances.md) 

