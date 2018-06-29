---
title: 'Oktatóanyag: Language Understanding- (LUIS-) alkalmazás hívása a Java használatával | Microsoft Docs'
description: Ez az oktatóanyag bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a Java használatával.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 7d27b464c86e979132dd44c0edcf981a475040b3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263720"
---
# <a name="tutorial-call-a-luis-endpoint-using-java"></a>Oktatóanyag: LUIS-végpont hívása a Java használatával
Átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

<!-- green checkmark -->
> [!div class="checklist"]
> * LUIS-előfizetés létrehozása és kulcsérték másolása későbbi használathoz
> * A LUIS-végpont által visszaadott eredmények megtekintése a böngészőben egy IoT-mintaalkalmazás közzétételéhez
> * Visual Studio C#-konzolalkalmazás létrehozása LUIS-végpontok HTTPS-hívásához

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="create-luis-subscription-key"></a>LUIS előfizetési kulcs létrehozása
Az ebben a bemutatóban használt LUIS-mintaalkalmazások hívásához egy Cognitive Services API-kulcsra lesz szüksége. 

Az API-kulcs beszerzéséhez kövesse az alábbi lépéseket: 
1. Először létre kell hoznia egy [Cognitive Services API-fiókot](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Azure Portalon. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

2. Jelentkezzen be az Azure Portalra a https://portal.azure.com címen. 

3. Kövesse az [előfizetési kulcsok Azure-ral történő létrehozását](./luis-how-to-azure-subscription.md) ismertető cikkben leírt lépéseket a kulcs beszerzéséhez.

4. Lépjen vissza a [LUIS](luis-reference-regions.md) webhelyére, és jelentkezzen be az Azure-fiókjával. 

    [![](media/luis-get-started-java-get-intent/app-list.png "Alkalmazáslista képernyőképe")](media/luis-get-started-java-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>A LUIS által visszaadott eredmények értelmezése

Annak megismeréséhez, hogy mit ad vissza egy LUIS-alkalmazás, beillesztheti a LUIS-mintaalkalmazás URL-címét egy böngészőablakba. A mintaalkalmazás egy IoT-alkalmazás, amely észleli, hogy a felhasználó fel- vagy lekapcsolni szeretné-e a világítást.

1. A mintaalkalmazás végpontja a következő formátumban van: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Másolja az URL-címet, és cserélje le az előfizetési kulcsot a `subscription-key` mezőben található értékre.
2. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredmény, amely jelzi, hogy a LUIS észleli a `HomeAutomation.TurnOn` szándékot és a `bedroom` értékű `HomeAutomation.Room` entitást.

    ![A TurnOn szándékot észlelő JSON-eredmény](./media/luis-get-started-java-get-intent/turn-on-bedroom.png)
3. Módosítsa az URL-címben található `q=` paraméter értékét `turn off the living room light` értékre, majd nyomja le az Enter billentyűt. Az eredmény most jelzi, hogy a LUIS észleli a `HomeAutomation.TurnOff` szándékot és a `living room` értékű `HomeAutomation.Room` entitást. 

    ![A TurnOff szándékot észlelő JSON-eredmény](./media/luis-get-started-java-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-java"></a>LUIS-eredmény felhasználása a végponti API és a Java használatával 

A Java használatával hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben. 
1. Másolja ki az alábbi kódot egy osztály létrehozásához az IDE-ben:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/java/call-endpoint.java)]
2. Cserélje le a `SubscriptionKey` változó értéket a LUIS előfizetési kulcsára.

3. Az IDE-ben adjon hozzá a `httpclient` és a `httpcore` kódtárra mutató hivatkozásokat.

4. Futtassa a konzolalkalmazást. Megjelenik a korábban a böngészőablakban látott JSON.

![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](./media/luis-get-started-java-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyagban létrehozott két erőforrás a LUIS előfizetési kulcsa és a C#-projekt volt. Törölje a LUIS előfizetési kulcsot az Azure Portalról. Zárja be a Visual Studio-projektet, és távolítsa el a könyvtárat a fájlrendszerből.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-java-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
