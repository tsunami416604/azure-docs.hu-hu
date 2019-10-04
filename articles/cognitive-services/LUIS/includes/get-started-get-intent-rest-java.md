---
title: A REST-hívással megkezdheti a javát
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838542"
---
## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* A [Visual Studio Code](https://code.visualstudio.com/) vagy a kedvenc ide
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Java használatával hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben. Ügyeljen arra, hogy hozzáadja az Apache-kódtárakat a projekthez.

1. Másolja ki az alábbi kódot egy `LuisGetRequest.java` nevű osztály létrehozásához:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Cserélje le a `YOUR-KEY` változó értéket a LUIS-kulcsra.

3. Cserélje le a fájlt a fájl elérési útjára, és fordítsa le a Java `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`-programot egy parancssorból:.

4. Cserélje le a fájlt a fájl elérési útjára, és futtassa az `java -cp .;<FILE_PATH>\* LuisGetRequest.java`alkalmazást a parancssorból:. Megjelenik a korábban a böngészőablakban látott JSON.

    ![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett ezzel a rövid útmutatóval, zárja be a Visual Studio-projektet, és távolítsa el a projekt könyvtárát a fájlrendszerből. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hosszúságú kimondott szöveg és-képzés hozzáadása Javával](../luis-get-started-java-add-utterance.md)