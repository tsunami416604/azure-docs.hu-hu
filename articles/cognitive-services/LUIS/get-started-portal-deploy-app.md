---
title: 'Gyors útmutató: alkalmazás üzembe helyezése a LUIS-portálon'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy alkalmazást az előrejelzési végpontok erőforrásának létrehozásával, az erőforrás hozzárendelésével, a képzéssel és az alkalmazás közzétételével.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: a67852d333a86c5acf20e8bf69004aa9131e1f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448076"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Gyors útmutató: alkalmazás üzembe helyezése a LUIS portálon

Ha a LUIS-alkalmazás készen áll arra, hogy egy ügyfélalkalmazás (például egy csevegési robot) teljes beolvasását adja vissza, az alkalmazást az előrejelzési végpontra kell telepítenie.

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy alkalmazást. Létrehoz egy előrejelzési végpont-erőforrást, hozzárendeli az erőforrást az alkalmazáshoz, betanítja az alkalmazást, és közzéteszi az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Azure- [előfizetés](https://azure.microsoft.com/free)beszerzése.
* Fejezze be az [előző portál](get-started-portal-build-app.md) rövid útmutatóját [, vagy töltse le és importálja az alkalmazást](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Ha olyan alkalmazásokkal rendelkezik, amelyek előzetes Azure-erőforrás-hitelesítést végeznek, váltson [át egy Azure-erőforrásra](luis-migration-authoring.md). Egyes portál oldalak eltérőek, ha az e-mail-hitelesítés érvényben van.

## <a name="create-the-endpoint-resource"></a>A végpont erőforrásának létrehozása

Az előrejelzési végpont erőforrását a Azure Portal hozza létre. Ez az erőforrás csak végpont-előrejelző lekérdezésekhez használható. Ne használja ezt az erőforrást az alkalmazás módosításainak létrehozásához.

1. Jelentkezzen be, és hozzon létre egy erőforrást a [Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).

1. Konfigurálja az előfizetést a következő beállításokkal:

   |Beállítás|Value (Díj)|Rendeltetés|
   |--|--|--|
   |Név|`my-luis-resource`|Az Azure-Erőforrás neve. Erre a névre akkor van szükség, amikor az erőforrást az alkalmazáshoz rendeli a LUIS portálon.|
   |Előfizetés|Az Ön előfizetése|Válassza ki a fiókjához társított előfizetések egyikét.|
   |Erőforráscsoport|`my-resource-group`|Hozzon létre egy új erőforráscsoportot az összes kognitív szolgáltatás erőforrásaihoz. Ha elkészült az erőforrásokkal, törölheti az erőforráscsoportot az előfizetés tisztításához. |
   |Szerzői hely|**USA nyugati régiója**|Az Azure-régió létrehozásához.|
   |A szerzői díjak szintjei|**F0**|A szerzői műveletek alapértelmezett díjszabási szintje.|
   |Futtatókörnyezet helye|**USA nyugati régiója**|Az Azure-régió az előrejelzési végpontok lekérdezéséhez.|
   |Futtatókörnyezet árképzési szintje|**S0**|Ez az árképzési csomag nagy forgalmú webhelyeket biztosít.|
   | | | |


   ![Azure API választása](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Válassza a **Létrehozás** lehetőséget az Azure-erőforrás létrehozásához.

   A következő szakaszban megtudhatja, hogyan csatlakoztatható az új erőforrás egy LUIS-alkalmazáshoz a LUIS-portálon.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Az erőforrás-kulcs kiosztása a LUIS-alkalmazáshoz a LUIS-portálon

Minden alkalommal, amikor új-erőforrást hoz létre a LUIS számára, hozzá kell rendelnie az erőforrást a LUIS alkalmazáshoz. A hozzárendelés után ezt a lépést csak akkor kell végrehajtania, ha új erőforrást hoz létre. Létrehozhat egy új erőforrást az alkalmazás régiói kibontásához, vagy nagyobb számú előrejelzési lekérdezés támogatásához.

1. Jelentkezzen be a [Luis-portálra](https://preview.luis.ai) , és válassza ki a **myEnglishApp** alkalmazást az alkalmazások listából.

1. Válassza a **kezelés** lehetőséget a jobb felső menüben, majd válassza az **Azure-erőforrások**lehetőséget.

1. A LUIS hozzáadásához válassza az **előrejelzési erőforrás hozzáadása**lehetőséget.

    ![A LUIS-előrejelzési erőforrás hozzáadásához válassza az előrejelzési erőforrás hozzáadása lehetőséget.](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Válassza ki a bérlőt, az előfizetést és az erőforrás nevét. Válassza az **erőforrás kiosztása**elemet.

   ![Erőforrás kiosztása az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource.png)

1. Hajtsa végre ugyanezen lépéseket a szerzői kulcs alkalmazáshoz való hozzáadásához.

1. Keresse meg az új sort a táblában az új előrejelzési erőforráshoz, és másolja a végpont URL-címét. A rendszer megfelelően van kiépítve, hogy egy előrejelzéshez `HTTP GET` kérelmet hozzon létre a LUIS API-végpontnak.

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Az alkalmazás közzététele az előrejelzési végponton

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Előrejelzési végpont kérése

A betekintő portálon `query=` az URL-cím végére, ahol a rendszer hozzáfűzi a felhasználót a GET kérelemhez. A `query=`után adja meg az előző rövid útmutató végén használt felhasználói kiírást:

```Is there a form named hrf-234098```

Győződjön meg arról, hogy a lekérdezési karakterlánc a következő párokat tartalmazza:

* `show-all-intents=true`
* `verbose=true`

A böngésző megjeleníti a választ:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
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

Ha ugyanezt az információt szeretné megtekinteni a teszt ablaktáblán, közzé kell tennie az alkalmazást. Az alkalmazás közzététele után válassza az **összehasonlítás a közzétételsel** a teszt ablaktáblán. A közzétett teszt ablaktáblán a **JSON megjelenítése nézet** használatával megtekintheti az előző lépéssel MEGegyező JSON-t. Így összehasonlíthatja a jelenlegi alkalmazás azon módosításait, amelyeket a végponton közzétett alkalmazással használ.

[![a jelenleg szerkesztés és az alkalmazás közzétett verziójának összehasonlítása](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, válassza a **saját alkalmazások** lehetőséget a felső navigációs menüből. Jelölje be az alkalmazás jelölőnégyzetét a listából, majd válassza a **Törlés** lehetőséget a lista fölötti helyi eszköztáron.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közös szándékok és entitások azonosítása](luis-tutorial-prebuilt-intents-entities.md)
