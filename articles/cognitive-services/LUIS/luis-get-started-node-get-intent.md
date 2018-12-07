---
title: 'Gyors útmutató: első beszédszándék – Node.js'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ebben a rövid útmutatóban elérhető nyilvános LUIS-alkalmazással határozza meg egy felhasználó szándékát egy beszélgetés szövegéből. Node.js nyelven küldje el szövegként a felhasználó szándékát a nyilvános alkalmazás HTTP-előrejelzési végpontjára. A LUIS a végpontnál a nyilvános alkalmazás modelljét alkalmazza a természetes nyelvű szövegen a jelentés elemzése érdekében, amellyel meghatározza az általános szándékot, valamint kinyeri az alkalmazás témájában releváns adatokat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8a355b59a7674e21eddddbc311d90a9798931f89
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012708"
---
# <a name="quickstart-get-intent-using-nodejs"></a>Rövid útmutató: Szándék lekérése a Node.js használatával

Ebben a rövid útmutatóban átadhat kimondott szövegeket egy LUIS-végpontnak, majd visszakaphatja a szándékot és az entitásokat.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/) programozási nyelv 
* [Visual Studio Code](https://code.visualstudio.com/)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás


> [!NOTE] 
> A teljes Node.js megoldás letölthető a [**LUIS-Samples** GitHub-adattárból](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Szándék lekérése böngészővel

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Node.js használatával hozzáférhet ugyanazokhoz az eredményekhez, amelyeket a böngészőablakban látott az előző lépésben.

1. Másolja ki az alábbi kódrészletet:

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Hozzon létre egy `.env` fájlt a következő szöveggel, vagy állítsa be ezeket a változókat a rendszerkörnyezetben:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Állítsa be a `LUIS_ENDPOINT_KEY` környezeti változót a kulcsára.

4. Telepítse a függőségeket a következő parancs parancssori futtatásával: `npm install`.

5. Futtassa a kódot a következővel: `npm start`. A futtatás ugyanazokat az értékeket jeleníti meg, amelyet korábban a böngészőablakban látott.

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a Node.js fájlt.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Beszédmódok hozzáadása](luis-get-started-node-add-utterance.md)