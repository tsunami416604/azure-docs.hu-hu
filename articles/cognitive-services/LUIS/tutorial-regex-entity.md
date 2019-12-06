---
title: 'Oktatóanyag: reguláris kifejezésű entitás – LUIS'
titleSuffix: Azure Cognitive Services
description: Következetesen formázott adatok kinyerése a reguláris kifejezés entitás használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 8da47899e2d3d2c1d04e9cf4768a968e7893ce96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851868"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Oktatóanyag: jól formázott adatok beolvasása a teljes területről
Ebben az oktatóanyagban hozzon létre egy reguláris kifejezés entitást, amely Kinyeri a következetesen formázott adatok részleteit.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása
> * Szándék hozzáadása
> * Reguláriskifejezés-entitás hozzáadása
> * Betanítás, közzététel és lekérdezés alkalmazás a kinyert adatgyűjtéshez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Reguláris kifejezések entitásai

A reguláris kifejezés entitásával jól formázott szöveget lehet kihúzni a teljes szövegből. Bár a kimondott szöveg szándékát mindig gépi tanulás határozza meg, ez az entitástípus nem gép által tanult. A reguláris kifejezéssel rendelkező entitások megfelelő használata bármely olyan szöveg, amely következetesen egy [reguláris kifejezés](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)által megjeleníthető.

`Send pizza delivery time to x123432`

Ez a példa egy _rövid kódot_ használ a szöveges üzenetek küldéséhez. Ez a rövid kód egy 5 vagy 6 számjegyből álló numerikus kód, amely x előtaggal van ellátva, és a reguláris kifejezéssel `x\d{5,6}`is leírható.

Amikor egy reguláris kifejezés entitást ad hozzá egy LUIS-alkalmazáshoz, nem kell [felcímkézni](label-entity-example-utterance.md) a szöveget a normál expressz entitással. A rendszer az összes cél összes hosszúságú kimondott szöveg alkalmazza.

## <a name="import-example-json-to-begin-app"></a>Importálja például a. JSON fájlt az alkalmazás megkezdéséhez

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Szándék létrehozása a megerősítő szöveges üzenetek küldéséhez

1. Válassza a **+ Létrehozás** lehetőséget, hogy új szándékot hozzon létre egy megerősítő szöveg küldésére vonatkozó szándék besorolásához.

1. Az előugró párbeszédpanelen írja be a `ConfirmationText` karakterláncot, majd válassza a **Kész** elemet.

1. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Pizzás kézbesítési idő küldése a x123432|
    |Txt-x234567 idő|
    |a hirdetmény x23987|

    A géppel megtanult entitások kinyeréséhez olyan példákat kell megadnia, amelyek tartalmazzák az entitást számos hosszúságú kimondott szöveg, de ezzel a nem géppel megismert entitással, a variáció nem fontos. Ha a szöveg megfelel a reguláris kifejezésnek, akkor a rendszer kinyeri.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>A reguláris kifejezés entitás használata jól formázott adathoz
Hozzon létre egy reguláris kifejezés entitást a szöveges számnak megfelelően. Ez a reguláris kifejezés megfelel a szövegnek, de figyelmen kívül hagyja a Case és a kulturális változatot.

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

1. Válassza a **+ Létrehozás** lehetőséget az entitások listája lapon.

1. Az előugró ablakban adja meg az új entitás nevét `ConfirmationTextRegEx`, válassza a **regex** elemet az entitás típusaként, majd kattintson a **tovább**gombra.

    > [!div class="mx-imgBorder"]
    > ![megkezdeni az entitások létrehozásának lépéseit a reguláris kifejezés entitásához](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. A **regex létrehozása entitásban**adja meg a `x\d{5,6}` a **regex** értékeként, majd válassza a **Létrehozás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![adjon meg reguláris kifejezést az adatok kinyerésére példaként](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Válassza a bal oldali menüben a **leképezések** lehetőséget, majd a **ConfirmationText** szándékot, hogy megtekintse a hosszúságú kimondott szöveg címkével ellátott reguláris kifejezést.

    > [!div class="mx-imgBorder"]
    > ![például a hosszúságú kimondott szöveg található reguláris kifejezést](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Mivel az entitás nem egy géppel megtanult entitás, az entitás a hosszúságú kimondott szöveg lesz alkalmazva, és a LUIS-portálon jelenik meg, amint létrejött.

## <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanítása tesztelés vagy közzététel előtt

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele a végpontról történő lekérdezéshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Nyissa meg a címet az URL-cím végére, és adja meg a következő értéket:

    `Text my pizza delivery to x23456 x234567 x12345`

    Az utolsó lekérdezésisztring-paraméter `query`, a kimondott szöveg pedig a **query**.

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Ha reguláriskifejezés-entitást használ, a LUIS kinyeri a megnevezett adatokat, ami programozás szempontjából nagyobb segítséget nyújt a JSON-választ fogadó ügyfélalkalmazásnak.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Koncepció – entitások](luis-concept-entity-types.md)
* [A reguláris kifejezés entitás JSON-hivatkozása](reference-entity-regular-expression.md?tabs=V3)
* [Entitások hozzáadása adatok kinyeréséhez](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag létrehozott egy új szándékot, példaszándékokat adott hozzá, majd létrehozott egy reguláriskifejezés-entitást a helyesen formázott adatok kimondott szövegből történő kinyerésének céljából. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

> [!div class="nextstepaction"]
> [Tudnivalók a listaentitásról](tutorial-list-entity.md)

