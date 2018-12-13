---
title: Szándékkal, a Java beolvasása
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ez a Java a rövid útmutatóban elérhető nyilvános LUIS alkalmazás használatával határozza meg a felhasználó szándékának természetes nyelvi szövegből.
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c55721e803b85c536067a90019f75dd5aa0f7845
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085348"
---
# <a name="quickstart-get-intent-using-java"></a>Rövid útmutató: Szándék lekérése a Java használatával

Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon 

A Java használatával hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben. 

1. Másolja ki az alábbi kódot egy `LuisGetRequest.java` nevű osztály létrehozásához:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Cserélje le a `YOUR-KEY` változó értéket a LUIS-kulcsra.

3. Fordítsa le a Java-programot a következővel: `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Futtassa az alkalmazást az `java -cp ":lib/*" LuisGetRequest.java` billentyű lenyomásával. Megjelenik a korábban a böngészőablakban látott JSON.

    ![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a Java-fájlt. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-java-add-utterance.md)