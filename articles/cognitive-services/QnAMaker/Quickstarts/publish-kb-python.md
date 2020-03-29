---
title: 'Rövid útmutató: Tudásbázis, REST, Python közzététele – QnA-készítő'
description: Ez a Python REST-alapú gyorsindítás közzéteszi a tudásbázist, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobotban.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 6f053ecbd58d3c2527c1b904437dcc4715c76af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851651"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Python használatával

Ez a REST-alapú rövid útmutató végigvezeti a tudásbázis (KB) programozott közzétételén. A közzététel leküldéses a tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search index, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobot.

Ez a rövid útmutató meghívja a QnA Maker REST API-jait:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.7](https://www.python.org/downloads/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs és a végpont (amely tartalmazza az erőforrás nevét) lekéréséhez válassza az erőforrás **gyorsindítását** az Azure Portalon.
* A QnA Maker tudásbázisának (KB) azonosítója megtalálható az URL-címben a lekérdezési karakterlánc paraméterben az `kbid` alábbiak szerint.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](../how-to/create-knowledge-base.md).

> [!NOTE]
> A teljes megoldásfájl(ok) az [ **Azure-Samples/cognitive-services-qnamaker-python** GitHub repository-ból](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-knowledge-base-python-file"></a>A tudásbázis Python-fájljának létrehozása

Hozzon létre egy `publish-kb-3x.py` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `publish-kb-3x.py` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is. Cserélje le az értékeket a sajátjára.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Post-kérelem hozzáadása a tudásbázis közzétételéhez

A szükséges állandók után adja hozzá a következő kódot, amely HTTPS-kérelmet küld a QnA Maker API-hoz egy tudásbázis közzétételéhez, és megkapja a választ:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Írja be a következő parancsot egy parancssorba a program futtatásához. Elküldi a kérelmet a QnA Maker API-nak a tudásbázis közzétételére, majd kinyomtatja a 204-et a siker vagy a hibák érdekében.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

A tudásbázis közzététele után a [végpont URL-címére](./get-answer-from-knowledge-base-python.md)van szükség a válasz létrehozásához.

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)

[A QnA Maker áttekintése](../Overview/overview.md)