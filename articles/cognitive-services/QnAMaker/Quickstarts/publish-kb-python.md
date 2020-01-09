---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, Python-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a Python REST-alapú rövid útmutató közzéteszi a tudásbázist, és létrehoz egy olyan végpontot, amely hívható az alkalmazásban vagy a csevegési robotban.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: b93ba53996168d55e45e995a3fc11fdc3b889f7b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447412"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Python használatával

Ez a REST-alapú rövid útmutató végigvezeti a Tudásbázis (KB) programozott közzétételének lépésein. A Publishing leküldi a Tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search indexre, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegési robotban.

Ez a rövid útmutató meghívja a QnA Maker REST API-kat:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.7](https://www.python.org/downloads/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.
* QnA Maker Tudásbázis-azonosító a `kbid` lekérdezési karakterlánc paraméterben az alább látható módon található.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](../how-to/create-knowledge-base.md).

> [!NOTE]
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-Python** GitHub-tárházból](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-knowledge-base-python-file"></a>A tudásbázis Python-fájljának létrehozása

Hozzon létre egy `publish-kb-3x.py` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `publish-kb-3x.py` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is. Cserélje le az értékeket a saját értékeire.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>POST kérelem hozzáadása a Tudásbázis közzétételéhez

A szükséges konstansok után adja hozzá a következő kódot, amely HTTPS-kérést küld a QnA Maker API egy Tudásbázis közzétételéhez, és fogadja a választ:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Írja be a következő parancsot egy parancssorba a program futtatásához. A rendszer elküldi a kérést a QnA Maker APInak a Tudásbázis közzétételéhez, majd kinyomtatja a 204-es hibát a sikeres vagy sikertelen műveletekhez.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

Miután közzétette a tudásbázist, szüksége lesz a [végpont URL-címére a válasz létrehozásához](../Tutorials/create-publish-answer.md#generating-an-answer).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)

[A QnA Maker áttekintése](../Overview/overview.md)
