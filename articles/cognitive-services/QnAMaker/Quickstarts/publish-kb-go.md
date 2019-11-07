---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, go-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a go REST-alapú rövid útmutató közzéteszi a tudásbázist, és létrehoz egy olyan végpontot, amely hívható az alkalmazásban vagy a csevegési robotban.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: e6a38392b6f83479db9036eab944dc498382f5c4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720972"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Go használatával

Ez a REST-alapú rövid útmutató végigvezeti a Tudásbázis (KB) programozott közzétételének lépésein. A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [Go 1.10.1](https://golang.org/dl/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.

* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE] 
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-go** GitHub-tárházból](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-go-file"></a>Go-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy `publish-kb.go`nevű új fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `publish-kb.go` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>A fő függvény létrehozása

A szükséges függőségek után adja hozzá a következő osztályt:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **mainon** belül


 függvényt, adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a saját értékeire.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>POST-kérés hozzáadása a KB közzétételére

A szükséges konstansok után adja hozzá a következő kódot, amely HTTPS-kérést küld a QnA Maker API egy Tudásbázis közzétételéhez, és fogadja a választ:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Írja be a következő parancsot a fájl lefordításához. A parancssor nem adja vissza a sikeres buildek információit.

```bash
go build publish-kb.go
```

Írja be a következő parancsot egy parancssorba a program futtatásához. A kérést a QnA Maker API küldi el a TUDÁSBÁZIS közzétételéhez, majd kinyomtatja a 204-as frissítést a sikerhez vagy a hibákhoz.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

Miután közzétette a tudásbázist, szüksége lesz a [végpont URL-címére a válasz létrehozásához](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
