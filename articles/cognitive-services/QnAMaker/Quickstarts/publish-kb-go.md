---
title: 'Rövid útmutató: Tudásbázis közzététele, REST, Go - QnA Maker'
description: Ez a Go REST-alapú gyorsindítás közzéteszi a tudásbázist, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobotban.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 4ce655bdc7a913ecb281ce8a75e7ec4f2009a2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851695"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Go használatával

Ez a REST-alapú rövid útmutató végigvezeti a tudásbázis (KB) programozott közzétételén. A közzététel leküldéses a tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search index, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [Go 1.10.1](https://golang.org/dl/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs és a végpont (amely tartalmazza az erőforrás nevét) lekéréséhez válassza az erőforrás **gyorsindítását** az Azure Portalon.

* A QnA Maker tudásbázisának (KB) azonosítója megtalálható az URL-címben a lekérdezési karakterlánc paraméterben az `kbid` alábbiak szerint.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE]
> A teljes megoldásfájl(ok) az [ **Azure-Samples/cognitive-services-qnamaker-go** GitHub-tárházból](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-go-file"></a>Go-fájl létrehozása

Nyissa meg a VSCode `publish-kb.go`programot, és hozzon létre egy új fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `publish-kb.go` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>A fő funkció létrehozása

A szükséges függőségek után adja hozzá a következő osztályt:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **fő** épületen belül


 funkciót, adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a sajátjára.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>POST-kérés hozzáadása a KB közzétételére

A szükséges állandók után adja hozzá a következő kódot, amely HTTPS-kérelmet küld a QnA Maker API-hoz egy tudásbázis közzétételéhez, és megkapja a választ:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Írja be a következő parancsot a fájl lefordításához. A parancssor nem adja vissza a sikeres buildek információit.

```bash
go build publish-kb.go
```

Írja be a következő parancsot egy parancssorba a program futtatásához. Elküldi a kérelmet a QnA Maker API-nak a KB közzétételére, majd kinyomtatja a 204-et a siker vagy a hibák érdekében.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

A tudásbázis közzététele után a [végpont URL-címére](./get-answer-from-knowledge-base-go.md)van szükség a válasz létrehozásához.

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)