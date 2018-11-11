---
title: Rövid Python-útmutató – szándék előrejelzése – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan hívhat meg egy LUIS-alkalmazást a Python használatával.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8fe39a2876d37e4897c03a5654e500e2e2d3cb9f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51276950"
---
# <a name="quickstart-get-intent-using-python"></a>Rövid útmutató: Szándék lekérése Python használatával
Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

[!INCLUDE[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.6](https://www.python.org/downloads/) vagy újabb.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Szándék lekérése programozott módon

A Pythonnal hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben.

1. Másolja az alábbi kódrészletek egyikét a `quickstart-call-endpoint.py` nevű fájlba:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Cserélje le az `Ocp-Apim-Subscription-Key` mező értéket a LUIS-végpont kulcsára.

3. Telepítsen függőségeket a következővel: `pip install requests`.

4. Futtassa a szkriptet a következővel: `python ./quickstart-call-endpoint.py`. Megjelenik a korábban a böngészőablakban látott JSON.

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Törölje a Python-fájlt. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-python-add-utterance.md)