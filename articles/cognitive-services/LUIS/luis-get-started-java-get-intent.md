---
title: Cél, Java-LUIS
titleSuffix: Azure Cognitive Services
description: Ez a Java a rövid útmutatóban elérhető nyilvános LUIS alkalmazás használatával határozza meg a felhasználó szándékának természetes nyelvi szövegből.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7a80aefd7a88727cf3a2261115c076853bd45b3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563814"
---
# <a name="quickstart-get-intent-using-java"></a>Gyors útmutató: Szándék beszerzése a Javával

Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* A [Visual Studio Code](https://code.visualstudio.com/) vagy a kedvenc ide
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Java használatával hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben. Ügyeljen arra, hogy hozzáadja az Apache-kódtárakat a projekthez.

1. Másolja ki az alábbi kódot egy `LuisGetRequest.java` nevű osztály létrehozásához:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Cserélje le a `YOUR-KEY` változó értéket a LUIS-kulcsra.

3. Cserélje le a fájlt a fájl elérési útjára, és fordítsa le a Java `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`-programot egy parancssorból:.

4. Cserélje le a fájlt a fájl elérési útjára, és futtassa az `java -cp .;<FILE_PATH>\* LuisGetRequest.java`alkalmazást a parancssorból:. Megjelenik a korábban a böngészőablakban látott JSON.

    ![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a Java-fájl/projekt mappát.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-java-add-utterance.md)
