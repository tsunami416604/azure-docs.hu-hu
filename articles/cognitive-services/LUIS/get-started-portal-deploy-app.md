---
title: 'Gyors útmutató: Alkalmazás üzembe helyezése a LUIS-portálon'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezheti üzembe a LUIS-alkalmazást az előrejelzési végponton, miután az alkalmazás készen áll arra, hogy visszaadja a teljes előrejelzéseket egy ügyfélalkalmazás, például egy csevegési robot számára. Ez a rövid útmutató végigvezeti az alkalmazások üzembe helyezésének lépésein az előrejelzési végpontok erőforrásának létrehozásán, az erőforrás az alkalmazáshoz való hozzárendelésének, az alkalmazás betanításának és az alkalmazás közzétételének lépésein.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f640921e6f48559db3f1414551d6ed974df15e4f
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703218"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Gyors útmutató: Alkalmazás üzembe helyezése a LUIS-portálon

Ha a LUIS-alkalmazás készen áll arra, hogy egy ügyfélalkalmazás (például egy csevegési robot) teljes beolvasását adja vissza, az alkalmazást az előrejelzési végpontra kell telepítenie.

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy alkalmazást. Létrehoz egy előrejelzési végpont-erőforrást, hozzárendeli az erőforrást az alkalmazáshoz, betanítja az alkalmazást, és közzéteszi az alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

* Azure- [előfizetés](https://azure.microsoft.com/free)beszerzése.
* Fejezze be az [előző portál](get-started-portal-build-app.md) rövid útmutatóját [, vagy töltse le és importálja az alkalmazást](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>A végpont erőforrásának létrehozása

Az előrejelzési végpont erőforrását a Azure Portal hozza létre. Ez az erőforrás csak végpont-előrejelző lekérdezésekhez használható. Ne használja ezt az erőforrást az alkalmazás módosításainak létrehozásához.

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).

1. Válassza ki a zöld **+** jelet a bal felső panelen. Keresse meg `Cognitive Services` értéket a piactéren, és válassza ki.

1. Konfigurálja az előfizetést a következő beállításokkal:

   |Beállítás|Value|Cél|
   |--|--|--|
   |Name (Név)|`my-cognitive-service-resource`|Az Azure-Erőforrás neve. Erre a névre akkor van szükség, amikor az erőforrást az alkalmazáshoz rendeli a LUIS portálon.|
   |Subscription|Az Ön előfizetése|Válassza ki a fiókjához társított előfizetések egyikét.|
   |Location|**USA nyugati régiója**|Az erőforráshoz tartozó Azure-régió.|
   |Tarifacsomag|**S0**|Az erőforrás alapértelmezett díjszabási szintje.|
   |Resource group|`my-cognitive-service-resource-group`|Hozzon létre egy új erőforráscsoportot az összes kognitív szolgáltatás erőforrásaihoz. Ha elkészült az erőforrásokkal, törölheti az erőforráscsoportot az előfizetés tisztításához. |
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

1. Válassza ki a bérlőt, az előfizetést és az erőforrás nevét. Válassza ki **erőforrás hozzárendelése**.

   ![Rendelje hozzá egy erőforrást az alkalmazáshoz](./media/get-started-portal-deploy-app/assign-resource.png)

1. Keresse meg az új sort a táblázatban, és másolja a végpont URL-címe. A rendszer megfelelően van kialakítva, hogy a LUIS API-végpontra `HTTP GET` kérést hozzon létre egy előrejelzéshez.

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele

Az alkalmazás betanítása, amikor készen áll a tesztelésre. Tegye közzé az alkalmazást, ha azt szeretné, hogy a jelenleg betanított verzió elérhető legyen az ügyfélalkalmazások számára a lekérdezés-előrejelzési végpont futtatókörnyezetből.

1. Ha az alkalmazás nincs betanítva, a jobb felső menüben válassza a **betanítás** lehetőséget.

1. A felső menüben válassza a **Közzététel** lehetőséget. Fogadja el az alapértelmezett környezeti beállításokat, majd válassza a **Közzététel**lehetőséget.

1. Amikor megjelenik a zöld siker értesítési sáv a böngészőablak tetején, válassza a **hivatkozás a végpontok listájára**lehetőséget.

   ![Az alkalmazás értesítési sávjának közzététele sikeresen megtörtént a böngészőben](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. A **kulcsok és végpont beállításai** lapon keresse meg a hozzárendelt erőforrások listáját és a hozzá tartozó végpont URL-címeit az alján.

1. Válassza ki az új erőforrás nevéhez társított végpont URL-címét. Ez a művelet egy megfelelően kialakított URL-címmel nyit meg egy webböngészőt, amely `GET` kérést hoz létre az előrejelzési végpont futtatókörnyezetének.

## <a name="prediction-endpoint-request"></a>Előrejelzési végpont kérése

<!-- V3FIX -->

Az URL-cím végén található `q=` a **lekérdezéshez** rövid, az pedig az, ahol a felhasználóhoz tartozó kifejezés a Get kérelemhez van hozzáfűzve. A `q=` után adja meg ugyanazt a felhasználói kiírást, amelyet az előző rövid útmutató végén használ:

```Is there a form named hrf-234098```

A böngésző megjeleníti a választ, amely ugyanaz a JSON, amelyet az ügyfélalkalmazás fog kapni:

```JSON
{
"query": "Is there a form named hrf-234098",
"topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9768753
},
"intents": [
    {
    "intent": "FindForm",
    "score": 0.9768753
    },
    {
    "intent": "None",
    "score": 0.0216071066
    }
],
"entities": [
    {
    "entity": "hrf-234098",
    "type": "Human Resources Form Number",
    "startIndex": 22,
    "endIndex": 31
    }
    ]
}
```

Ez a válasz több információt nyújt, mint az előző oktatóanyag alapértelmezett teszt panelje. Ha ugyanezt az információt szeretné megtekinteni a teszt ablaktáblán, közzé kell tennie az alkalmazást. Az alkalmazás közzététele után válassza az **összehasonlítás a közzétételsel** a teszt ablaktáblán. A közzétett teszt ablaktáblán a **JSON megjelenítése nézet** használatával megtekintheti az előző lépéssel MEGegyező JSON-t. Így összehasonlíthatja a jelenleg használt alkalmazást a végponton közzétett alkalmazással.

[@no__t – az 1Compare jelenleg szerkesztés és az alkalmazás közzétett verziója](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)




## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, válassza a **saját alkalmazások** lehetőséget a felső navigációs menüből. Jelölje be az alkalmazás jelölőnégyzetét a listából, majd válassza a **Törlés** lehetőséget a lista fölötti helyi eszköztáron.

[@no__t 1Delete alkalmazás a saját alkalmazások listájából](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közös szándékok és entitások azonosítása](luis-tutorial-prebuilt-intents-entities.md)
