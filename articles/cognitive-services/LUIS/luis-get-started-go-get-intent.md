---
title: Get szándékkal, a Go
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ez nyissa meg a rövid útmutatóban elérhető nyilvános LUIS alkalmazás használatával határozza meg a felhasználó szándékának természetes nyelvi szövegből.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: e106a9363b4fca768d0705f65a43397805a8b011
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222864"
---
# <a name="quickstart-get-intent-using-go"></a>Gyors útmutató: Get-leképezés Go használatával

Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Go](https://golang.org/) programozási nyelv  
* [Visual Studio Code](https://code.visualstudio.com/)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Góval hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben. 

1. Hozzon létre egy új fájlt `endpoint.go` néven. Adja hozzá a következő kódot:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. A fájl létrehozásához használt könyvtárban egy parancssorba írja be a `go build endpoint.go` parancsot a Go-fájl összeállításához. A parancssor nem adja vissza a sikeres buildek információit.

3. Futtassa a Go-alkalmazást a parancssorból a következő szöveg beírásával: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Cserélje le az `<add-your-key>` értéket a kulcs értékére.  
    
    A parancssori válasz a következő: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```
    
## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Zárja be a Go-fájlt, és távolítsa el a fájlrendszerből. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-go-add-utterance.md)
