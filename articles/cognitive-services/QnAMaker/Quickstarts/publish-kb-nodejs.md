---
title: Tudásbázis következő, a REST, a Node.js közzététele
titleSuffix: QnA Maker - Azure Cognitive Services
description: A Node.js rövid útmutató végigvezeti programozott módon közzététele (KB). A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 4b6f3e6913801de9259c106469bf8ca986f5b7b6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083801"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Node.js használatával

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js 6+](https://nodejs.org/en/download/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 
* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-nodejs.md).


> [!NOTE] 
> A teljes megoldásfájl(ok) az [**Azure-Samples/cognitive-services-qnamaker-nodejs** GitHub-adattárban](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short) érhető(k) el.

## <a name="create-a-knowledge-base-nodejs-file"></a>Node.js-tudásbázisfájl létrehozása

Hozzon létre egy `publish-knowledge-base.js` nevű fájlt.

## <a name="add-required-dependencies"></a>Szükséges függőségek hozzáadása

A `publish-knowledge-base.js` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is. Cserélje le az értékeket a saját.

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adja hozzá a POST-kérés Tudásbázis közzététele

A szükséges állandókat után adja hozzá a következő kódra, amely egy HTTPS-kérést küld a QnA Maker API Tudásbázis közzététele, és megkapja a választ:

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="run-the-program"></a>A program futtatása

Hozza létre és futtassa a programot. A kérelem automatikusan elküldi a QnA Maker API a Tudásbázis közzététele, majd a konzolablakban a választ ki a program.

Miután a tudásbázis közzé lett téve, lekérdezheti a tartalmát a végpontról egy ügyfélalkalmazás vagy csevegőrobot használatával. 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

A Tudásbázis közzététele után kell a [végponti URL-cím létrehozásához választ](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)