---
title: Szándék beolvasása a REST-hívással a Pythonban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e86d1e16e7c61f851a75ad97d2744b0daa009617
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838507"
---
## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6](https://www.python.org/downloads/) vagy újabb.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent--programmatically"></a>Szándék lekérése programozott módon

A Pythonnal hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben.

1. Másolja az alábbi kódrészletek egyikét a `quickstart-call-endpoint.py` nevű fájlba:

    #### <a name="python-27tabp2"></a>[Python 2.7](#tab/P2)

    [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]    

    #### <a name="python-36tabp3"></a>[Python 3,6](#tab/P3)

    [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

    * * *

1. Cserélje le az `Ocp-Apim-Subscription-Key` mező értéket a LUIS-végpont kulcsára.

1. Telepítsen függőségeket a következővel: `pip install requests`.

1. Futtassa a szkriptet a következővel: `python ./quickstart-call-endpoint.py`. Megjelenik a korábban a böngészőablakban látott JSON.

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett ezzel a rövid útmutatóval, zárja be a Visual Studio-projektet, és távolítsa el a projekt könyvtárát a fájlrendszerből. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hosszúságú kimondott szöveg és-képzés hozzáadása a Pythonhoz](../luis-get-started-python-add-utterance.md)