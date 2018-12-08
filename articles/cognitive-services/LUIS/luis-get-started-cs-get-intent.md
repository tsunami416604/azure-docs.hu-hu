---
title: Első szándékkal,C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: A jelen C# a rövid útmutatóban elérhető nyilvános LUIS alkalmazás segítségével meghatározhatja, a felhasználó szándékának természetes nyelvi szövegből.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 22b2faa91be9b4a2504bff542deb559d33b9fa4f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101041"
---
# <a name="quickstart-get-intent-using-c"></a>Rövid útmutató: Szándék lekérése a C# használatával

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio Community 2017-es kiadása](https://visualstudio.microsoft.com/vs/community/)
* C# programozási nyelv (a VS Community 2017 része)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

Ha C# nyelven lekérdezi a GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) előrejelzési végpontot, ugyanazokat az eredményeket kapja, mint amelyeket az előző szakaszban látott a böngészőablakban. 

1. Hozzon létre egy új konzolalkalmazást a Visual Studióban. 

    ![A LUIS felhasználói beállítások menü elérése](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. A Visual Studio projekt Megoldáskezelő ablakában válassza az **Add reference** (Referencia hozzáadása) elemet, majd válassza a **System.Web** lehetőséget az Assemblies (Szerelvények) lapon.

    ![A LUIS felhasználói beállítások menü elérése](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Írja felül a Program.cs fájlt a következő kóddal:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Cserélje le a `YOUR_KEY` értéket a LUIS-kulcsra.

5. Buildelje és futtassa a konzolalkalmazást. Megjelenik a korábban a böngészőablakban látott JSON.

    ![A LUIS által visszaadott JSON-eredményt megjelenítő konzolablak](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett ezzel a rövid útmutatóval, zárja be a Visual Studio-projektet, és távolítsa el a projekt könyvtárát a fájlrendszerből. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kimondott szövegek hozzáadása és betanítás a C# használatával](luis-get-started-cs-add-utterance.md)