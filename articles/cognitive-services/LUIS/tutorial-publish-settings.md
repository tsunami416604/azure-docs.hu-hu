---
title: 'Oktatóanyag: közzétételi beállítások – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban a közzétételi beállítások módosításával növelheti az előrejelzéseket.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: diberry
ms.openlocfilehash: 2df32c20bebf4243f383a0cccd8f037721533602
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890387"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Oktatóanyag: hangulati elemzés hozzáadása közzétételi beállításként

Ebben az oktatóanyagban módosítsa a közzétételi beállításokat, hogy kinyerje a hangulati elemzést, majd lekérdezi a LUIS-végpontot, hogy megtekintse a felhasználó teljes visszatérési hangulatát.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Hangulati elemzés hozzáadása közzétételi beállításként
> * A közzétett végpontból való Kimondás érzésének beolvasása

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Az érzelmek elemzése egy közzétételi beállítás

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Vélemény|Pontszám|Kimondott szöveg|
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

1. Nyissa meg a címet az URL-cím végére, és adja meg a következő értéket:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Az utolsó lekérdezésisztring-paraméter `query`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia kinyert hangulatelemzéssel: `OrderPizza`.

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* A hangulat elemzését a kognitív szolgáltatás [text Analytics](../Text-Analytics/index.yml). A funkció Text Analytics [támogatott nyelvekre](luis-language-support.md#languages-supported)korlátozódik.
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag közzétételi beállításként hozzáadja a hangulatelemzést, amellyel átfogó hangulatértékek nyerhetők ki a kimondott szövegből.

> [!div class="nextstepaction"]
> [Végponti kimondott szövegek áttekintése az Emberi erőforrás alkalmazásban](luis-tutorial-review-endpoint-utterances.md)

