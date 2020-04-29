---
title: 'Oktatóanyag: közzétételi beállítások – LUIS'
description: Ebben az oktatóanyagban a közzétételi beállítások módosításával növelheti az előrejelzéseket.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545769"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Oktatóanyag: hangulati elemzés hozzáadása közzétételi beállításként

Ebben az oktatóanyagban módosítsa a közzétételi beállításokat, hogy kinyerje a hangulati elemzést, majd lekérdezi a LUIS-végpontot, hogy megtekintse a felhasználó teljes visszatérési hangulatát.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Hangulati elemzés hozzáadása közzétételi beállításként
> * A közzétett végpontból való Kimondás érzésének beolvasása

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Az érzelmek elemzése egy közzétételi beállítás

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Hangulat|Pontszám|Kimondott szöveg|
|:--|:--|:--|
|negatív|0,01 |A pizza borzasztó volt.|
|pozitív|0,97 |A sajtos pizza csodálatos volt.|

A hangulatelemzés egy olyan közzétételi beállítás, amely minden kimondott szövegre vonatkozik. A beállítás után az alkalmazás a Kimondás érzését adja vissza anélkül, hogy meg kellene címkéznie az adatok címkéjét.

Mivel ez egy közzétételi beállítás, nem jelenik meg a szándékok vagy entitások lapjain. az [interaktív teszt](luis-interactive-test.md#view-sentiment-results) panelen vagy a végpont URL-címén való teszteléskor látható.

## <a name="import-example-json-to-begin-app"></a>Importálja például a. JSON fájlt az alkalmazás megkezdéséhez

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Alkalmazás konfigurálása hangulatelemzés belefoglalásához

1. A felső menüben válassza a **Közzététel** lehetőséget. A hangulat elemzése egy közzétételi beállítás.

1. Válassza ki a **termelési tárolóhely** elemet, majd válassza a **beállítások módosítása**lehetőséget.
1. Állítsa be a Hangulatelemzés beállítást **a következőre:.**

    ![Hangulatelemzés bekapcsolása közzétételi beállításként](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>A végpontból való Kimondás érzésének beolvasása

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le a _YOUR_QUERY_HERE_ a következőre:

    `Deliver 2 of the best cheese pizzas ever!!!`

    A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia kinyert hangulatelemzéssel: `OrderPizza`.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    A hangulat elemzése pozitív eredménnyel, 86%-os pontszámmal.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* A hangulat elemzését a kognitív szolgáltatás [text Analytics](../Text-Analytics/index.yml). A funkció Text Analytics [támogatott nyelvekre](luis-language-support.md#languages-supported)korlátozódik.
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag közzétételi beállításként hozzáadja a hangulatelemzést, amellyel átfogó hangulatértékek nyerhetők ki a kimondott szövegből.

> [!div class="nextstepaction"]
> [Végponti kimondott szövegek áttekintése az Emberi erőforrás alkalmazásban](luis-tutorial-review-endpoint-utterances.md)

