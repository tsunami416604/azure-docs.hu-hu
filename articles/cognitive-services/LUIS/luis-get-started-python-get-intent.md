---
title: 'Rövid útmutató: Language Understanding-alkalmazás (LUIS-alkalmazás) meghívása a Python használatával | Microsoft Docs'
description: Ez a rövid útmutató bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a Python használatával.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: bc7ae912d762a98c34b9a1b2d6a82d5630c4794b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43770002"
---
# <a name="quickstart-call-a-luis-endpoint-using-python"></a>Rövid útmutató: LUIS-végpont meghívása Python használatával
Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

<!-- green checkmark -->
<!--
> [!div class="checklist"]
> * Create LUIS subscription and copy key value for later use
> * View LUIS endpoint results from browser to public sample IoT app
> * Create Visual Studio C# console app to make HTTPS call to LUIS endpoint
-->

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md#luis-website)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

<a name="create-luis-subscription-key"></a>
## <a name="create-luis-endpoint-key"></a>LUIS-végpont kulcsának létrehozása
Az ebben a bemutatóban használt LUIS-mintaalkalmazások hívásához egy Cognitive Services API-kulcsra lesz szüksége. 

Az API-kulcs beszerzéséhez kövesse az alábbi lépéseket: 

1. Először létre kell hoznia egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Azure Portalon. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

2. Jelentkezzen be az Azure Portalra a https://portal.azure.com címen. 

3. Kövesse a [végpontkulcsok Azure-ral történő létrehozását](./luis-how-to-azure-subscription.md) ismertető cikkben leírt lépéseket a kulcs beszerzéséhez.

4. Lépjen vissza a [LUIS](luis-reference-regions.md) webhelyére, és jelentkezzen be az Azure-fiókjával. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Alkalmazáslista képernyőképe")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>A LUIS által visszaadott eredmények értelmezése

Annak megismeréséhez, hogy mit ad vissza egy LUIS-alkalmazás, beillesztheti a LUIS-mintaalkalmazás URL-címét egy böngészőablakba. A mintaalkalmazás egy IoT-alkalmazás, amely észleli, hogy a felhasználó fel- vagy lekapcsolni szeretné-e a világítást.

1. A mintaalkalmazás végpontja a következő formátumban van: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Másolja az URL-címet, és cserélje le a végpontkulcsot a `subscription-key` mezőben található értékre.
2. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredmény, amely jelzi, hogy a LUIS észleli a `HomeAutomation.TurnOn` szándékot és a `bedroom` értékű `HomeAutomation.Room` entitást.

    ![A TurnOn szándékot észlelő JSON-eredmény](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Módosítsa az URL-címben található `q=` paraméter értékét `turn off the living room light` értékre, majd nyomja le az Enter billentyűt. Az eredmény most jelzi, hogy a LUIS észleli a `HomeAutomation.TurnOff` szándékot és a `living room` értékű `HomeAutomation.Room` entitást. 

    ![A TurnOff szándékot észlelő JSON-eredmény](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-python"></a>LUIS-eredmény felhasználása a végponti API és Python használatával

A Pythonnal hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben.

1. Másolja az alábbi kódrészletek egyikét a `quickstart-call-endpoint.py` nevű fájlba:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Cserélje le az `Ocp-Apim-Subscription-Key` mező értéket a LUIS-végpont kulcsára.

3. Telepítsen függőségeket a következővel: `pip install requests`.

4. Futtassa a szkriptet a következővel: `python ./quickstart-call-endpoint.py`. Megjelenik a korábban a böngészőablakban látott JSON.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-python-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyagban létrehozott két erőforrás a LUIS-végpont kulcsa és a C#-projekt volt. Törölje a LUIS-végpont kulcsát az Azure Portalról. Zárja be a Visual Studio-projektet, és távolítsa el a könyvtárat a fájlrendszerből. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-python-add-utterance.md)