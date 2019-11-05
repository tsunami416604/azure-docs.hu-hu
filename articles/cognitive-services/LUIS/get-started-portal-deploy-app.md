---
title: 'Gyors útmutató: alkalmazás üzembe helyezése a LUIS-portálon'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezheti üzembe a LUIS-alkalmazást az előrejelzési végponton, miután az alkalmazás készen áll arra, hogy visszaadja a teljes előrejelzéseket egy ügyfélalkalmazás, például egy csevegési robot számára. Ez a rövid útmutató végigvezeti az alkalmazások üzembe helyezésének lépésein az előrejelzési végpontok erőforrásának létrehozásán, az erőforrás az alkalmazáshoz való hozzárendelésének, az alkalmazás betanításának és az alkalmazás közzétételének lépésein.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: ecae5c7db02436fe34fec19989f174504fd1e03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488724"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Gyors útmutató: alkalmazás üzembe helyezése a LUIS portálon

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]


Ha a LUIS-alkalmazás készen áll arra, hogy egy ügyfélalkalmazás (például egy csevegési robot) teljes beolvasását adja vissza, az alkalmazást az előrejelzési végpontra kell telepítenie.

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy alkalmazást. Létrehoz egy előrejelzési végpont-erőforrást, hozzárendeli az erőforrást az alkalmazáshoz, betanítja az alkalmazást, és közzéteszi az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Azure- [előfizetés](https://azure.microsoft.com/free)beszerzése.
* Fejezze be az [előző portál](get-started-portal-build-app.md) rövid útmutatóját [, vagy töltse le és importálja az alkalmazást](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>A végpont erőforrásának létrehozása

Az előrejelzési végpont erőforrását a Azure Portal hozza létre. Ez az erőforrás csak végpont-előrejelző lekérdezésekhez használható. Ne használja ezt az erőforrást az alkalmazás módosításainak létrehozásához.

1. Bejelentkezés az [Azure Portalra](https://ms.portal.azure.com/).

1. Válassza ki a zöld **+** a bal felső panelen. Keresse meg `Cognitive Services` a piactéren, és válassza ki.

1. Konfigurálja az előfizetést a következő beállításokkal:

   |Beállítás|Érték|Cél|
   |--|--|--|
   |Name (Név)|`my-cognitive-service-resource`|Az Azure-Erőforrás neve. Erre a névre akkor van szükség, amikor az erőforrást az alkalmazáshoz rendeli a LUIS portálon.|
   |Előfizetés|Az Ön előfizetése|Válassza ki a fiókjához társított előfizetések egyikét.|
   |Földrajzi egység|**USA nyugati régiója**|Az erőforráshoz tartozó Azure-régió.|
   |Tarifacsomag|**S0**|Az erőforrás alapértelmezett díjszabási szintje.|
   |Erőforráscsoport|`my-cognitive-service-resource-group`|Hozzon létre egy új erőforráscsoportot az összes kognitív szolgáltatás erőforrásaihoz. Ha elkészült az erőforrásokkal, törölheti az erőforráscsoportot az előfizetés tisztításához. |
   | | | |

   ![Azure API választása](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Válassza a **Létrehozás** lehetőséget az Azure-erőforrás létrehozásához.

   A következő szakaszban megtudhatja, hogyan csatlakoztatható az új erőforrás egy LUIS-alkalmazáshoz a LUIS-portálon.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Az erőforrás-kulcs kiosztása a LUIS-alkalmazáshoz a LUIS-portálon

Minden alkalommal, amikor új-erőforrást hoz létre a LUIS számára, hozzá kell rendelnie az erőforrást a LUIS alkalmazáshoz. A hozzárendelés után ezt a lépést csak akkor kell végrehajtania, ha új erőforrást hoz létre. Létrehozhat egy új erőforrást az alkalmazás régiói kibontásához, vagy nagyobb számú előrejelzési lekérdezés támogatásához.

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai) , és válassza ki a **myEnglishApp** alkalmazást az alkalmazások listából.

1. Válassza a **kezelés** lehetőséget a jobb felső menüben, majd válassza az **Azure-erőforrások**lehetőséget.

1. A LUIS hozzáadásához válassza az **előrejelzési erőforrás hozzáadása**lehetőséget.

    <!-- TBD: get screenshot-->

1. Válassza ki a bérlőt, az előfizetést és az erőforrás nevét. Válassza az **erőforrás kiosztása**elemet.

   ![Erőforrás kiosztása az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource.png)

1. Keresse meg a táblázat új sorát, és másolja a végpont URL-címét. A rendszer megfelelően van kiépítve, hogy egy előrejelzéshez `HTTP GET` kérelmet hozzon létre a LUIS API-végpontnak.

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele

Az alkalmazás betanítása, amikor készen áll a tesztelésre. Tegye közzé az alkalmazást, ha azt szeretné, hogy a jelenleg betanított verzió elérhető legyen az ügyfélalkalmazások számára a lekérdezés-előrejelzési végpont futtatókörnyezetből.

1. Ha az alkalmazás nincs betanítva, a jobb felső menüben válassza a **betanítás** lehetőséget.

1. A felső menüben válassza a **Közzététel** lehetőséget. Válassza ki az üzemi tárolóhelyet, és tegye közzé.

1. Amikor megjelenik az értesítési sáv, a közzététel befejeződött.

1. A kezelés szakasz Azure- **erőforrások** lapján keresse meg a hozzárendelt erőforrások listáját és a hozzá tartozó végpont URL-címeket.

1. Másolja a példában szereplő lekérdezést egy böngészőablakba, és adja hozzá a felhasználói kifejezését `query` paraméterként.

## <a name="prediction-endpoint-request"></a>Előrejelzési végpont kérése

Az URL-cím végén található `query=` rövid a **lekérdezéshez** , és az a hely, ahol a felhasználóhoz tartozó kifejezés a Get kérelemhez van hozzáfűzve. A `query=`után adja meg az előző rövid útmutató végén használt felhasználói kiírást:

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

Ha ugyanezt az információt szeretné megtekinteni a teszt ablaktáblán, közzé kell tennie az alkalmazást. Az alkalmazás közzététele után válassza az **összehasonlítás a közzétételsel** a teszt ablaktáblán. A közzétett teszt ablaktáblán a **JSON megjelenítése nézet** használatával megtekintheti az előző lépéssel MEGegyező JSON-t. Így összehasonlíthatja a jelenleg használt alkalmazást a végponton közzétett alkalmazással.

[![a jelenleg szerkesztés és az alkalmazás közzétett verziójának összehasonlítása](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, válassza a **saját alkalmazások** lehetőséget a felső navigációs menüből. Jelölje be az alkalmazás jelölőnégyzetét a listából, majd válassza a **Törlés** lehetőséget a lista fölötti helyi eszköztáron.

[alkalmazás törlése a saját alkalmazások listájáról ![](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közös szándékok és entitások azonosítása](luis-tutorial-prebuilt-intents-entities.md)
