---
title: Szándékkal, a Java beolvasása
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ez a Java a rövid útmutatóban elérhető nyilvános LUIS alkalmazás használatával határozza meg a felhasználó szándékának természetes nyelvi szövegből.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 5e5a186be19c0ecc9730bc79bddff41fb6bbb571
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401659"
---
# <a name="quickstart-get-intent-using-java"></a>Gyors útmutató: Get-leképezés Java használatával

Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* [A Visual Studio Code](https://code.visualstudio.com/) vagy a kedvenc ide-je
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Java használatával hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben. Ügyeljen arra, hogy az Apache-kódtárak hozzáadása a projekthez.

1. Másolja ki az alábbi kódot egy `LuisGetRequest.java` nevű osztály létrehozásához:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Cserélje le a `YOUR-KEY` változó értéket a LUIS-kulcsra.

3. Cserélje le a fájl elérési útja és a egy parancssorból a java-program fordítása: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Cserélje le a fájl elérési útja, és futtathatja az alkalmazást parancssori paranccsal futtathatja: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Megjelenik a korábban a böngészőablakban látott JSON.

    ![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a Java-fájl vagy projekt mappát.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-java-add-utterance.md)
