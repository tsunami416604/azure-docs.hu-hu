---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, go-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Nyissa meg REST-alapú rövid útmutató végigvezeti közzététele, amely egy dedikált Azure Search-index a közzétett Tudásbázis jelölő leküldi a tesztelt Tudásbázis legújabb verzióját. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: c752a0ef4a908ba9a3771a0e0fb4f5f034f97707
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562951"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Gyors útmutató: Tudásbázis közzététele QnA Maker a go használatával

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [Go 1.10.1](https://golang.org/dl/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 

* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy, az ehhez a rövid útmutatóhoz használandó mintát: [Hozzon létre egy új tudásbázist](create-new-kb-csharp.md).

> [!NOTE] 
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-go** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)-tárházból érhetők el.

## <a name="create-a-go-file"></a>Go-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy új fájlt `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `publish-kb.go` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>A fő függvény létrehozása

A szükséges függőségek után adja hozzá a következő osztályok:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

Belül a **fő**


 függvény, adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a saját.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>POST-kérés hozzáadása a KB közzétételére

A szükséges állandókat után adja hozzá a következő kódra, amely egy HTTPS-kérést küld a QnA Maker API Tudásbázis közzététele, és megkapja a választ:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Írja be a következő parancsot a fájl lefordításához. A parancssor nem adja vissza a sikeres buildek információit.

```bash
go build publish-kb.go
```

Írja be a következő parancsot egy parancssorba a program futtatásához. A kérelem a QnA Maker API közzététele a KB, majd nyomtassa ki az 204 siker vagy hibákat küld.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

A Tudásbázis közzététele után kell a [végponti URL-cím létrehozásához választ](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
