---
title: A REST-hívással megkezdheti a leképezéstC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838546"
---
## <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio Community 2017-es kiadása](https://visualstudio.microsoft.com/vs/community/)
* C# programozási nyelv (a VS Community 2017 része)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

Ha C# nyelven lekérdezi a GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) előrejelzési végpontot, ugyanazokat az eredményeket kapja, mint amelyeket az előző szakaszban látott a böngészőablakban. 

1. Hozzon létre egy új konzolalkalmazást a Visual Studióban. 

    ![Új Console-alkalmazás létrehozása a Visual Studióban](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. A Visual Studio projekt Megoldáskezelő ablakában válassza az **Add reference** (Referencia hozzáadása) elemet, majd válassza a **System.Web** lehetőséget az Assemblies (Szerelvények) lapon.

    ![Válassza a hivatkozás hozzáadása lehetőséget, majd válassza a System. Web elemet a szerelvények lapon.](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Írja felül a Program.cs fájlt a következő kóddal:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Cserélje le a `YOUR_KEY` értéket a LUIS-kulcsra.

5. Buildelje és futtassa a konzolalkalmazást. Megjelenik a korábban a böngészőablakban látott JSON.

    ![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett ezzel a rövid útmutatóval, zárja be a Visual Studio-projektet, és távolítsa el a projekt könyvtárát a fájlrendszerből. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kimondott szövegek hozzáadása és betanítás a C# használatával](../luis-get-started-cs-add-utterance.md)