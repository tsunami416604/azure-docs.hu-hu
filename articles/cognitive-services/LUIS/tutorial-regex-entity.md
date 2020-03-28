---
title: 'Oktatóanyag: Reguláris kifejezés entitás - LUIS'
titleSuffix: Azure Cognitive Services
description: Konzisztensen formázott adatok kinyerése egy utterance (kifejezés) kifejezés a reguláris entitás használatával.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 0ca6f2a67e01e4c604c2dcc8f8eaa9ffe8bad045
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75381512"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Oktatóanyag: Jól formázott adatok beszereznie az utterance (kifejezés)
Ebben az oktatóanyagban hozzon létre egy reguláris kifejezés entitás következetesen formázott adatokat egy utterance (kifejezés) kinyerése.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása
> * Szándék hozzáadása
> * Reguláriskifejezés-entitás hozzáadása
> * Kinyert adatok betanítása, közzététele és lekérdezése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Reguláriskifejezés-entitások

A reguláris kifejezés entitás segítségével húzza ki a jól formázott szöveget egy utterance (kifejezés). Bár a kimondott szöveg szándékát mindig gépi tanulás határozza meg, ez az entitástípus nem gép által tanult. A reguláris kifejezés entitás hoz megfelelően használható minden olyan szöveg, amelyet következetesen [reguláris kifejezés](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)sel ábrázolhat .

`Send pizza delivery time to x123432`

Ez a példa _egy rövid kódot_ használ a szöveges üzenetek küldéséhez. Ez a rövid kód egy 5 vagy 6 jegyű numerikus kód, amely `x\d{5,6}`x előtaggal van ellátva, és a reguláris kifejezéssel írható le.

Amikor egy reguláris kifejezés entitást ad hozzá egy LUIS-alkalmazáshoz, nem kell [a](label-entity-example-utterance.md) szöveget a normál expressz entitással címkézni. Minden szándékban minden kimondott szövegre vonatkozik.

## <a name="import-example-json-to-begin-app"></a>Példát importálhat.json alkalmazás kezdéséhez

1.  Töltse le és mentse az [alkalmazás JSON fájlt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Szándék létrehozása megerősítő szöveges üzenetek küldéséhez

1. Válassza a **+ Create** lehetőséget, ha új szándékot szeretne létrehozni egy megerősítési szöveg küldésére vonatkozó utterance (kifejezés) szándékának osztályozásához.

1. Az előugró párbeszédpanelen írja be a `ConfirmationText` karakterláncot, majd válassza a **Kész** elemet.

1. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |Pizza szállítási idő küldése az x123432-re|
    |Txt x234567 az idő|
    |x23987 a hirdetmény|

    Gépben tanult entitások kinyeréséhez meg kell adnia példákat, amelyek tartalmazzák az entitás tav-ban a különböző kimondott szöveg, de ezzel a nem gép-megtanult entitás, a változás nem fontos. Mindaddig, amíg a szöveg megegyezik a reguláris kifejezéssel, a program kinyeri.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>A reguláris kifejezés entitás használata jól formázott adatokhoz
Hozzon létre egy reguláris kifejezés entitást, amely megfelel a szövegszámnak. Ez a reguláris kifejezés megfelel a szövegnek, de figyelmen kívül hagyja a kis- és nagybetűket és a kulturális környezet változatait.

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

1. Válassza a **+ Create** lehetőséget az Entitások listalapon.

1. Az előugró párbeszédpanelen adja meg `ConfirmationTextRegEx`az új entitás nevét, válassza a **RegEx** entitástípust, majd kattintson a **Tovább gombra.**

    > [!div class="mx-imgBorder"]
    > ![Entitáslétrehozási lépések indítása reguláris kifejezésentitáshoz](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. A **Regex létrehozása entitáson**adja meg `x\d{5,6}` a **Regex** értéket, majd válassza a **Létrehozás lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Reguláris kifejezés megadása a példa utterance (kifejezés) kinyeréséhez.](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Válassza **leképezések** a bal oldali menüből, majd a **ConfirmationText** szándék, hogy a reguláris kifejezés feliratú.

    > [!div class="mx-imgBorder"]
    > ![Példakimondott szövegben megjelölt reguláris kifejezés megtekintése](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Mivel az entitás nem egy gép által megtanult entitás, az entitás a kimondott szövegre lesz alkalmazva, és a LUIS-portálon jelenik meg, amint létrejön.

## <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanítása tesztelés vagy közzététel előtt

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele lekérdezéshez a végpontról

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és entitás-előrejelzés beszerezni a végponttól

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Lépjen az URL-cím végére a címben, és adja meg a következő utterance (kifejezés) értéket:

    `Text my pizza delivery to x23456 x234567 x12345`

    Az utolsó querystring `query`paraméter a ( utterance ( kifejezés ) **lekérdezés.**

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

* [Koncepció - entitások](luis-concept-entity-types.md)
* [A reguláris kifejezés entitás JSON-hivatkozása](reference-entity-regular-expression.md?tabs=V3)
* [Entitások hozzáadása adatok kinyeréséhez](luis-how-to-add-entities.md)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag létrehozott egy új szándékot, példaszándékokat adott hozzá, majd létrehozott egy reguláriskifejezés-entitást a helyesen formázott adatok kimondott szövegből történő kinyerésének céljából. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

> [!div class="nextstepaction"]
> [Tudnivalók a listaentitásról](tutorial-list-entity.md)

