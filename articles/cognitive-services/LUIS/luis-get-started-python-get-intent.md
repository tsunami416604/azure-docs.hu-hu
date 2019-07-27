---
title: Cél, Python-LUIS
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: defcb6713516952e56e6cca2d63a99ee61f5b868
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563783"
---
# <a name="quickstart-get-intent-using-python"></a>Gyors útmutató: Szándék beolvasása a Python használatával
Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6](https://www.python.org/downloads/) vagy újabb.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Szándék lekérése programozott módon

A Pythonnal hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben.

1. Másolja az alábbi kódrészletek egyikét a `quickstart-call-endpoint.py` nevű fájlba:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Cserélje le az `Ocp-Apim-Subscription-Key` mező értéket a LUIS-végpont kulcsára.

3. Telepítsen függőségeket a következővel: `pip install requests`.

4. Futtassa a szkriptet a következővel: `python ./quickstart-call-endpoint.py`. Megjelenik a korábban a böngészőablakban látott JSON.

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Törölje a Python-fájlt. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-python-add-utterance.md)
