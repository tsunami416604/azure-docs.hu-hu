---
title: A REST-hívás beszerzése a Go-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: b36e967e960ec6a2b39409ea7be5f3f98f69b9c8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838485"
---
## <a name="prerequisites"></a>Előfeltételek

* [Go](https://golang.org/) programozási nyelv  
* [Visual Studio Code](https://code.visualstudio.com/)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

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

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett ezzel a rövid útmutatóval, zárja be a Visual Studio-projektet, és távolítsa el a projekt könyvtárát a fájlrendszerből. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hosszúságú kimondott szöveg és-képzés felvétele a go-val](../luis-get-started-go-add-utterance.md)