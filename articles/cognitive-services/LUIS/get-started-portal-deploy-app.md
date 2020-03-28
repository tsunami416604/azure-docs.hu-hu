---
title: 'Rövid útmutató: Alkalmazás üzembe helyezése a LUIS-portállal'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan telepíthet egy alkalmazást előrejelzési végpont-erőforrás létrehozásával, az erőforrás hozzárendelésével, a képzéssel és az alkalmazás közzétételével.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 0ee2b33aa3388b3cb99aa42c338ded800c9679a4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79241779"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Rövid útmutató: Alkalmazás üzembe helyezése a LUIS-portálon

Amikor a LUIS-alkalmazás készen áll az utterance (nedv) egy ügyfélalkalmazás (például egy csevegőrobot) visszaadására, telepítenie kell az alkalmazást az előrejelzési végpontra.

Ebben a rövid útmutatóban megtudhatja, hogy egy alkalmazás üzembe helyezése. Hozzon létre egy előrejelzési végpont erőforrást, rendelje hozzá az erőforrást az alkalmazáshoz, tanítsa be az alkalmazást, és tegye közzé az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Szerezzen be egy [Azure-előfizetést.](https://azure.microsoft.com/free)
* Töltse ki az [előző portál gyorsindítását,](get-started-portal-build-app.md) illetve [töltse le és importálja az alkalmazást.](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json)
* Ha olyan alkalmazásokkal rendelkezik, amelyek megelőzik az Azure-erőforrások hitelesítését, [migráljon egy Azure-erőforrásra.](luis-migration-authoring.md) Egyes portáloldalak máshogy néznek ki, ha az e-mail-hitelesítés érvényben van.

## <a name="create-the-endpoint-resource"></a>Végponterőforrás létrehozása

Az előrejelzési végpont erőforrást az Azure Portalon hozza létre. Ezt az erőforrást csak végpont-előrejelzési lekérdezésekhez szabad használni. Ne használja ezt az erőforrást az alkalmazás módosításaihoz.

1. Jelentkezzen be, és hozzon létre egy erőforrást az [Azure Portalon.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)

1. Konfigurálja az előfizetést a következő beállításokkal:

   |Beállítás|Érték|Cél|
   |--|--|--|
   |Név|`my-luis-resource`|Az Azure-erőforrás neve. Erre a névre akkor van szüksége, amikor hozzárendeli az erőforrást az alkalmazáshoz a LUIS-portálon.|
   |Előfizetés|Az Ön előfizetése|Válassza ki a fiókjához társított előfizetések egyikét.|
   |Erőforráscsoport|`my-resource-group`|Hozzon létre egy új erőforráscsoportot az összes kognitív szolgáltatási erőforráshoz. Ha végzett az erőforrásokkal, törölheti az erőforráscsoportot az előfizetés karbantartásához. |
   |Szerzői hely|**USA nyugati régiója**|Az Azure-régió a szerzői.|
   |Szerzői tarifacsomag|**F0**|A szerzői jog alapértelmezett tarifacsomagja.|
   |Futásidejű hely|**USA nyugati régiója**|Az Azure-régió előrejelzési végpontlekérdezések.|
   |Futásidejű tarifacsomag|**S0**|Ez az árképzési szint nagy forgalmú webhelyeket biztosít.|
   | | | |


   ![Az Azure API-választás](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

1. Válassza a **Létrehozás** lehetőséget az Azure-erőforrás létrehozásához.

   A következő szakaszban megtudhatja, hogyan kapcsolhatja össze ezt az új erőforrást egy LUIS-alkalmazással a LUIS-portálon.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Az erőforráskulcs hozzárendelése a LUIS-alkalmazáshoz a LUIS-portálon

Minden alkalommal, amikor új erőforrást hoz létre a LUIS számára, hozzá kell rendelnie az erőforrást a LUIS alkalmazáshoz. Miután hozzávan rendelve, nem kell újra megtennie ezt a lépést, hacsak nem hoz létre egy új erőforrást. Előfordulhat, hogy hozzon létre egy új erőforrást az alkalmazás régióinak bővítéséhez vagy az előrejelzési lekérdezések nagyobb számának támogatásához.

1. Jelentkezzen be az [előzetes LUIS portálra,](https://preview.luis.ai) és válassza ki a **myEnglishApp** alkalmazást az alkalmazások listájából.

1. Válassza a jobb felső menü **Kezelés parancsát,** majd az **Azure Resources parancsot.**

1. A LUIS hozzáadásához válassza az **Előrejelzési erőforrás hozzáadása**lehetőséget.

    ![A LUIS-előrejelzési erőforrás hozzáadásához válassza az Előrejelzési erőforrás hozzáadása lehetőséget.](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Válassza ki a bérlő, az előfizetés és az erőforrás nevét. Válassza **az Erőforrás hozzárendelése**lehetőséget.

   ![Erőforrás hozzárendelése az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource.png)

1. Hajtsa végre ugyanezeket a lépéseket a szerzői kulcs alkalmazáshoz való hozzáadásához.

1. Keresse meg az új előrejelzési erőforrás táblájának új sorát, és másolja a végpont URL-címét. Megfelelően van kialakítva, `HTTP GET` hogy egy kérelmet a LUIS API-végpont egy előrejelzést.

> [!TIP]
> Ha az Aktív tanulás továbbfejlesztett használatával szeretné javítani a LUIS alkalmazást, válassza **a Lekérdezési paraméterek módosítása** lehetőséget, és válassza a Naplók **mentése**lehetőséget. Ez a művelet módosítja `log=true` a példa URL-címét a querystring paraméter hozzáadásával. Másolja és használja a módosított példa lekérdezési URL-cím, amikor előrejelzési lekérdezéseket a futásidejű végpontra.

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Az alkalmazás közzététele az előrejelzési végponton

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Előrejelzési végpont-kérelem

Az előzetes verziós `query=` portálon az URL végén, ahol a felhasználó utterance (kifejezés) hozzáfűzi a GET kérés. A `query=`után a , adja meg ugyanazt a felhasználói utterances használt végén az előző rövid útmutató:

```Is there a form named hrf-234098```

Győződjön meg arról, hogy a lekérdezési karakterlánc a következő párokat tartalmazza:

* `show-all-intents=true`
* `verbose=true`

A böngésző a következő választ jeleníti meg:

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

Ha ugyanilyen szintű információkat szeretne látni a tesztablakban, közzé kell tennie az alkalmazást. Az alkalmazás közzététele után válassza az **Összehasonlítás a közzétetttel** lehetőséget a tesztablaktáblán. Használja **a JSON-nézet megjelenítése** a közzétett tesztablakban, hogy ugyanazt a JSON-t láthassa, mint az előző lépés. Ily módon összehasonlíthatja az aktuális alkalmazás módosításait, amelyen dolgozik egy olyan alkalmazással, amely a végponton közzétett.

[![Az alkalmazás jelenleg szerkesztése és közzétett verziójának összehasonlítása](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a rövid útmutatóval, válassza a Felső navigációs menü **Saját alkalmazások** parancsát. Jelölje be az alkalmazás jelölőnégyzetét a listából, majd válassza a **Törlés** lehetőséget a lista feletti környezeti eszköztáron.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közös szándékok és entitások azonosítása](luis-tutorial-prebuilt-intents-entities.md)
