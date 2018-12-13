---
title: REST, a Tudásbázis közzétételeC#
titleSuffix: QnA Maker- Azure Cognitive Services
description: Ez C# REST-alapú a rövid útmutató végigvezeti a közzététele, amely egy dedikált Azure Search-index a közzétett Tudásbázis jelölő leküldi a tesztelt Tudásbázis legújabb verzióját. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/18/2018
ms.author: diberry
ms.openlocfilehash: 0fef4a22179cafbce40868c86e3bceabcebe26a9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094372"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a C# használatával

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 
* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE] 
> A teljes megoldásfájlok az [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub-adattárból](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base) érhetők el.

## <a name="create-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

1. Nyissa meg a Visual Studio 2017 Community Editiont.
1. Hozzon létre egy új **konzolalkalmazás- (.NET Core-)** projektet, és adja neki a `QnaMakerQuickstart` nevet. Fogadja el az alapértelmezett értékeket a többi beállításnál.

## <a name="add-required-dependencies"></a>Szükséges függőségek hozzáadása

A Program.cs tetején cserélje le az önálló using utasítást a következő sorokkal, hogy felvegye a szükséges függőségeket a projektbe:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

Az a **fő** metódust, adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a saját.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=11-14 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adja hozzá a POST-kérés Tudásbázis közzététele

A szükséges állandókat után adja hozzá a következő kódra, amely egy HTTPS-kérést küld a QnA Maker API Tudásbázis közzététele, és megkapja a választ:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=16-29&dedent=8 "Add HTTP Post request and response")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. 
 
## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot. A kérelem automatikusan elküldi a QnA Maker API a Tudásbázis közzététele, majd a konzolablakban a választ ki a program.

Miután a tudásbázis közzé lett téve, lekérdezheti a tartalmát a végpontról egy ügyfélalkalmazás vagy csevegőrobot használatával. 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

A Tudásbázis közzététele után kell a [végponti URL-cím létrehozásához választ](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
