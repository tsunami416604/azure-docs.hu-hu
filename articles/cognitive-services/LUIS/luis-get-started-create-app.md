---
title: 'Gyors útmutató: alkalmazás létrehozása – LUIS'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre olyan LUIS-alkalmazást, amely az előre elkészített tartományi `HomeAutomation` használja a fények és a készülékek be-és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 302321a36a6ce7526ad5e3144f87b88edbfaaec7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393715"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rövid útmutató: Előre összeállított otthonautomatizálási alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
A **My Apps** (Saját alkalmazások) lapon hozhatja létre és kezelheti az alkalmazásokat.

1. A LUIS portál saját alkalmazások listájában válassza a **+ Létrehozás**elemet.

    ![A LUIS portál saját alkalmazások listájában válassza a + létrehozás elemet.](./media/create-app-in-portal.png)

1. A párbeszédpanelen nevezze el az alkalmazást `Home Automation` majd válassza a **kész**lehetőséget. LUIS létrehozza az alkalmazást. A leírás nem kötelező, és nem használható szerzői vagy előrejelzési műveletekhez. LUIS-alkalmazás létrehozásakor az előrejelzési erőforrás is opcionális. Ha az alkalmazást éles környezetben teszi közzé, egy előrejelzési erőforrást kell rendelnie, hogy az alkalmazás képes legyen sok kérést kezelni.

    ![A párbeszédpanelen nevezze el az alkalmazás "Home Automation"](./media/create-new-app-details.png)

    >[!NOTE]
    >A kulturális környezet az alkalmazás létrehozása után nem módosítható.

## <a name="add-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

Válassza az **előre elkészített tartományok** lehetőséget, majd keressen rá a **HomeAutomation**kifejezésre. Válassza a **tartomány hozzáadása** elemet a HomeAutomation kártyán.

![Válassza az "előre elkészített tartományok" lehetőséget, majd keressen rá a "HomeAutomation" kifejezésre. Válassza a "tartomány hozzáadása" lehetőséget a HomeAutomation kártyán.](media/luis-quickstart-new-app/home-automation.png)

A tartomány sikeres hozzáadása után az előre összeállított tartomány mezőben megjelenik egy **Remove domain** (Tartomány eltávolítása) gomb.

## <a name="intents-and-entities"></a>Szándékok és entitások

Válassza a **leképezések** lehetőséget a HomeAutomation-tartomány szándékának áttekintéséhez. Az előre elkészített tartomány-leképezések minta hosszúságú kimondott szöveg rendelkeznek.

![Képernyőkép a HomeAutomation-leképezések listájáról](media/luis-quickstart-new-app/home-automation-intents.png "Képernyőkép a HomeAutomation-leképezések listájáról")

> [!NOTE]
> A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak.

Válassza a **HomeAutomation.TurnOff** szándékot. Láthatja, hogy a szándék olyan kimondott szövegek listáját tartalmazza, amelyek entitásokkal vannak feliratozva.

[![Képernyőkép a HomeAutomation. kanyart szándékáról](media/luis-quickstart-new-app/home-automation-turnoff.png "Képernyőkép a HomeAutomation. kanyart szándékáról")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is. Válassza a **teszt**lehetőséget. Írjon be egy tesztet, például `Turn off the lights` az interaktív teszt ablaktáblába, majd nyomja le az ENTER billentyűt.

```
Turn off the lights
```

Ellenőrizze, hogy a legmagasabb pontszámú leképezés megfelel-e az egyes kimondott tesztszövegek várt leképezésének.

Ebben a példában a `Turn off the lights` helyesen van azonosítva, mint a **HomeAutomation. kanyart**felső pontozási szándéka.

![Képernyőkép a teszt panelről a kiemeléssel kiemelve](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Az előrejelzéssel kapcsolatos további információk áttekintéséhez válassza a **vizsgálat** lehetőséget.

![A teszt panel képernyőképe ellenőrzési információkkal](media/luis-quickstart-new-app/test.png)

Válassza ismét a **Test** (Tesztelés) gombot a tesztpanel összecsukásához.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>A V3 API előrejelzési végpontjának lekérdezése

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

1. A böngésző címsorába a lekérdezési karakterlánchoz ellenőrizze, hogy a következő név és érték sávok szerepelnek-e az URL-címben. Ha nem szerepelnek a lekérdezési karakterláncban, adja hozzá őket:

    |Név/érték párok|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. A böngésző címsorába lépjen az URL-cím végére, és írja be `turn off the living room light` értéket a _lekérdezési_ értékhez, majd nyomja le az ENTER billentyűt.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.907323956,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Behívhatja a végpontot kódból:

> [!div class="nextstepaction"]
> [LUIS-végpont hívása kóddal](luis-get-started-cs-get-intent.md)
