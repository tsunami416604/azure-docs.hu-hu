---
title: 'Gyors útmutató: alkalmazás létrehozása – LUIS'
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy LUIS-alkalmazást, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703245"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Gyors útmutató: Előre elkészített Home Automation-alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez egy ingyenes LUIS-fiókra van szüksége, amely a [https://www.luis.ai](https://www.luis.ai) címen elérhető LUIS portálon hozható létre. 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
A **My Apps** (Saját alkalmazások) lapon hozhatja létre és kezelheti az alkalmazásokat. 

1. Válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.

    [![Képernyőkép az alkalmazások listájának](media/luis-quickstart-new-app/app-list.png "Alkalmazáslista képernyőképe")](media/luis-quickstart-new-app/app-list.png)

1. A párbeszédpanelen adja a „Home Automation” nevet az alkalmazásnak.

    [![Képernyőkép, hozzon létre új alkalmazás felugró párbeszédpanel](media/luis-quickstart-new-app/create-new-app-dialog.png "létrehozása képernyőképe új alkalmazás felugró párbeszédpanel")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Válassza ki az alkalmazás kulturális környezetét. Ehhez az otthonautomatizálási alkalmazáshoz válassza a magyar nyelvet. Ezután válassza a **Done** (Kész) elemet. A LUIS létrehozza az otthonautomatizálási alkalmazást. 

    >[!NOTE]
    >A kulturális környezet az alkalmazás létrehozása után nem módosítható. 

## <a name="add-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

A bal oldali navigációs ablaktáblán válassza a **Prebuilt domains** (Előre összeállított tartományok) elemet. Ezután keressen a „Home” kifejezésre. Válassza az **Add domain** (Tartomány hozzáadása) lehetőséget.

[![Képernyőkép a kezdőlap Automation tartomány feltüntettük az előre összeállított tartomány menü](media/luis-quickstart-new-app/home-automation.png "képernyőképe a kezdőlap Automation tartomány feltüntettük az előre összeállított tartomány menü")](media/luis-quickstart-new-app/home-automation.png)

A tartomány sikeres hozzáadása után az előre összeállított tartomány mezőben megjelenik egy **Remove domain** (Tartomány eltávolítása) gomb.

[![Eltávolítás gomb képernyőképe a kezdőlap Automation-tartomány](media/luis-quickstart-new-app/remove-domain.png "Eltávolítás gomb képernyőképe a kezdőlap Automation-tartomány")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Szándékok és entitások

A bal oldali navigációs ablaktáblán válassza az **Intents** (Szándékok) elemet a Home Automation-tartomány leképezéseinek megtekintéséhez. Minden szándék kimondottszöveg-mintákkal rendelkezik.

![Képernyőkép a HomeAutomation-leképezések listájáról](media/luis-quickstart-new-app/home-automation-intents.png "a HomeAutomation-leképezések listájáról")

> [!NOTE]
> A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak. 

Válassza a **HomeAutomation.TurnOff** szándékot. Láthatja, hogy a szándék olyan kimondott szövegek listáját tartalmazza, amelyek entitásokkal vannak feliratozva.

[![Képernyőkép a HomeAutomation.TurnOff szándékot](media/luis-quickstart-new-app/home-automation-turnoff.png "képernyőképe a HomeAutomation.TurnOff leképezés")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet. Írjon be egy kimondott tesztszöveget (például „A világítás kikapcsolása”) az Interaktív tesztelés ablaktáblán, és nyomja le az Enter billentyűt. 

```
Turn off the lights
```

Ellenőrizze, hogy a legmagasabb pontszámú leképezés megfelel-e az egyes kimondott tesztszövegek várt leképezésének.

Ebben a példában a `Turn off the lights` helyesen van azonosítva, mint a **HomeAutomation. kanyart**felső pontozási szándéka.

[![Képernyőfelvétel: a teszt panel az utterance (kifejezés) kiemelésével](media/luis-quickstart-new-app/test.png "képernyőképe a teszt panel az utterance (kifejezés) kiemelésével")](media/luis-quickstart-new-app/test.png)


Az előrejelzéssel kapcsolatos további információk áttekintéséhez válassza a **vizsgálat** lehetőséget.

![Képernyőkép a teszt panelről a kiemeléssel kiemelve](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Válassza ismét a **Test** (Tesztelés) gombot a tesztpanel összecsukásához. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>A v2 API előrejelzési végpontjának lekérdezése

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Lépjen az URL-cím végéhez, és írja be a következőt: `turn off the living room light`, majd nyomja le az Enter billentyűt. 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 előrejelzési végpont](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    A böngészőben megjelenik a HTTP-végpont JSON-válaszának **v2 API** -verziója.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[V3 előrejelzési végpont](#tab/V3)

    [V3 API-lekérdezés](luis-migration-api-v3.md)esetén a böngészőben módosítsa a Get metódus HTTPS-kérelmét, és módosítsa az értékeket a saját értékeire.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Behívhatja a végpontot kódból:

> [!div class="nextstepaction"]
> [LUIS-végpont hívása kóddal](luis-get-started-cs-get-intent.md)
