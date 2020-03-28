---
title: 'Rövid útmutató: alkalmazás létrehozása - LUIS'
description: Ez a rövid útmutató bemutatja, hogyan hozhat `HomeAutomation` létre egy LUIS-alkalmazást, amely az előre összeállított tartományt használja a fények és berendezések be- és kikapcsolására. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: de6cf5e95ee63fc9500cf1b5edab78597bdb18af
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287798"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rövid útmutató: Előre összeállított otthonautomatizálási alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
A **My Apps** (Saját alkalmazások) lapon hozhatja létre és kezelheti az alkalmazásokat.

1. A Saját alkalmazások listában válassza a **+ Új alkalmazás a beszélgetéshez**lehetőséget.

1. A párbeszédpanelen nevezze el `Home Automation`az alkalmazást . Válassza az **angol nyelvet** kultúraként. A leírás nem kötelező, és nem használható a szerzői vagy előrejelzési. Az előrejelzési erőforrás is nem kötelező, ha egy LUIS-alkalmazás létrehozásakor. Válassza a **Done** (Kész) lehetőséget.

    A LUIS létrehozza az alkalmazást. Amikor éles környezetben teszi közzé az alkalmazást, hozzá kell rendelnie egy előrejelzési erőforrást, hogy az alkalmazás számos kérést kezelni tudja.

    ![A párbeszédpanelen nevezze el az alkalmazást "Home Automation"](./media/create-new-app-details.png)

    >[!NOTE]
    >A kulturális környezet az alkalmazás létrehozása után nem módosítható.

## <a name="add-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

Válassza **az Előre összeállított tartományok lehetőséget,** majd keresse meg a **HomeAutomation**kifejezést. Válassza a **Tartomány hozzáadása lehetőséget** a HomeAutomation-kártyán.

![Válassza az "Előre összeállított tartományok" lehetőséget, majd keresse meg a "HomeAutomation" kifejezést. Válassza a "Tartomány hozzáadása" lehetőséget a HomeAutomation kártyán.](media/luis-quickstart-new-app/home-automation.png)

A tartomány sikeres hozzáadása után az előre összeállított tartomány mezőben megjelenik egy **Remove domain** (Tartomány eltávolítása) gomb.

## <a name="intents-and-entities"></a>Szándékok és entitások

Válassza **a Leképezések** lehetőséget a HomeAutomation tartományi leképezések áttekintéséhez. Az előre összeállított tartományi leképezések minta utterances rendelkezik.

![Képernyőkép a HomeAutomation leképezések listájáról](media/luis-quickstart-new-app/home-automation-intents.png "Képernyőkép a HomeAutomation leképezések listájáról")

> [!NOTE]
> A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak.

Válassza a **HomeAutomation.TurnOff** szándékot. Láthatja, hogy a szándék olyan kimondott szövegek listáját tartalmazza, amelyek entitásokkal vannak feliratozva.

[![A HomeAutomation.TurnOff szándék képernyőképe](media/luis-quickstart-new-app/home-automation-turnoff.png "A HomeAutomation.TurnOff szándék képernyőképe")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is.

1. Válassza a jobb felső navigációs sáv **Teszt elemének** kiválasztását. 1. Írjon be egy `Turn off the lights` tesztutteranceance like az interaktív tesztablaktáblába, és nyomja le az Enter billentyűt.

    ```
    Turn off the lights
    ```

    Ellenőrizze, hogy a legmagasabb pontszámú leképezés megfelel-e az egyes kimondott tesztszövegek várt leképezésének.

    Ebben a `Turn off the lights` példában helyesen azonosítja a **HomeAutomation.TurnOff**legmagasabb pontozási szándékát.

    ![A tesztelési ablaktábla képernyőképe a kiemelt kimondott szöveggel](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Válassza **a Vizsgálat** lehetőséget az előrejelzéssel kapcsolatos további információk áttekintéséhez.

    ![Képernyőkép a Teszt panelről az ellenőrzési információkkal](media/luis-quickstart-new-app/test.png)

1. Zárja be a tesztablakot.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>A V3 API előrejelzési végpontjának lekérdezése

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. A böngésző címsorában a lekérdezési karakterláncesetében győződjön meg arról, hogy a következő név- és értéksávok találhatók az URL-címben. Ha nem szerepelnek a lekérdezési karakterláncban, adja hozzá őket:

    |Név/érték pár|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. A böngésző címsorában lépjen az URL végére, és írja be `turn off the living room light` a _lekérdezési_ értéket, majd nyomja le az Enter billentyűt.

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

    További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Behívhatja a végpontot kódból:

> [!div class="nextstepaction"]
> [LUIS-végpont hívása kóddal](luis-get-started-cs-get-intent.md)
