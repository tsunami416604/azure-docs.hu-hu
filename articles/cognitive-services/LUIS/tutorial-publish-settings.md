---
title: 'Oktatóanyag: Közzétételi beállítások – LUIS'
description: Ebben az oktatóanyagban módosítsa a közzétételi beállításokat az előrejelzések javításához.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545769"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Oktatóanyag: Hangulatelemzés hozzáadása közzétételi beállításként

Ebben az oktatóanyagban módosítsa a közzétételi beállításokat a hangulatelemzés kinyeréséhez, majd a LUIS-végpont lekérdezésével tekintse meg a felhasználói utterance (kifejezés) visszaadott hangulatát.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Hangulatelemzés hozzáadása közzétételi beállításként
> * Kimondott szöveg sentimentbelátása a közzétett végpontból

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>A hangulatelemzés közzétételi beállítás

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Hangulat|Pontszám|Kimondott szöveg|
|:--|:--|:--|
|negatív|0.01 |A pizza szörnyű volt.|
|pozitív|0.97 |A sajtpizza csodálatos volt.|

A hangulatelemzés egy olyan közzétételi beállítás, amely minden kimondott szövegre vonatkozik. Miután beállította, az alkalmazás egy utterance (kifejezés) érzést ad vissza anélkül, hogy az adatokat címkéznie kellene.

Mivel közzétételi beállításról van szó, nem jelenik meg a szándékok vagy entitások lapjain. az [interaktív teszt](luis-interactive-test.md#view-sentiment-results) panelen vagy a végpont URL-címén való teszteléskor látható.

## <a name="import-example-json-to-begin-app"></a>Példát importálhat.json alkalmazás kezdéséhez

1.  Töltse le és mentse az [alkalmazás JSON fájlt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Alkalmazás konfigurálása hangulatelemzés belefoglalásához

1. Válassza a felső menü **Közzététel** parancsát. A hangulatelemzés közzétételi beállítás.

1. Válassza **a Termelési hely helyet,** majd a Beállítások **módosítása**lehetőséget.
1. Állítsa a Hangulatelemzés beállítást **Be**értékre.

    ![Hangulatelemzés bekapcsolása közzétételi beállításként](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Az utterance (kifejezés) véleményének betartása a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le _YOUR_QUERY_HERE_ a következőre:

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

    A hangulatelemzés 86%-os pontszámmal pozitív.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* A hangulatelemzést a Cognitive Service [Text Analytics](../Text-Analytics/index.yml)biztosítja. A szolgáltatás a Text Analytics [által támogatott nyelvekre](luis-language-support.md#languages-supported)korlátozódik.
* [Hogyan kell a vonat](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag közzétételi beállításként hozzáadja a hangulatelemzést, amellyel átfogó hangulatértékek nyerhetők ki a kimondott szövegből.

> [!div class="nextstepaction"]
> [Végponti kimondott szövegek áttekintése az Emberi erőforrás alkalmazásban](luis-tutorial-review-endpoint-utterances.md)

