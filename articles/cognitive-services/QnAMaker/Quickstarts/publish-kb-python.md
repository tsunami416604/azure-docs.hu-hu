---
title: Közzététel a Tudásbázis következő, a REST, a Python
titleSuffix: QnA Maker - Azure Cognitive Services
description: A Python REST-alapú rövid útmutató végigvezeti közzététele, amely egy dedikált Azure Search-index a közzétett Tudásbázis jelölő leküldi a tesztelt Tudásbázis legújabb verzióját. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 30a29b69fb44c45598841a7571e5d052ee9ad398
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213245"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Gyors útmutató: Tudásbázis közzététele a QnA Maker Python használatával

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.7](https://www.python.org/downloads/)
* Rendelkeznie kell QnA Maker-szolgáltatással is. A kulcs lekéréséhez válassza az irányítópulton az Erőforrás-kezelés területen lévő Kulcsok lehetőséget.
* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha például egy Tudásbázis még nem rendelkezik, a minta használata ebben a rövid útmutatóban egy hozhat létre: [Hozzon létre egy új Tudásbázis](create-new-kb-nodejs.md).

> [!NOTE] 
> A teljes megoldás fájl(ok) érhetők el a [ **Azure-minták/cognitive-services-qnamaker – python** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>A tudásbázis Python-fájljának létrehozása

Hozzon létre egy `publish-kb-3x.py` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `publish-kb-3x.py` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is. Cserélje le az értékeket a saját.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adja hozzá a POST-kérés Tudásbázis közzététele

A szükséges állandókat után adja hozzá a következő kódra, amely egy HTTPS-kérést küld a QnA Maker API Tudásbázis közzététele, és megkapja a választ:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Írja be a következő parancsot egy parancssorba a program futtatásához. Ezt a kérelmet küld a QnA Maker API-t a Tudásbázis közzététele, majd nyomtassa ki a hibák és sikeres 204.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

A Tudásbázis közzététele után kell a [végponti URL-cím létrehozásához választ](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

[A QnA Maker áttekintése](../Overview/overview.md)
