---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, C# -QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez C# REST-alapú a rövid útmutató végigvezeti a közzététele, amely egy dedikált Azure Search-index a közzétett Tudásbázis jelölő leküldi a tesztelt Tudásbázis legújabb verzióját. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 0ce0f3673a297964932f3b97dfd1d49d7c1e55e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559785"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Gyors útmutató: Tudásbázis közzététele QnA Maker használatávalC#

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A közzététel leküldi a tudásbázis legújabb verzióját egy dedikált Azure Search-indexre, és létrehoz egy, az alkalmazásban vagy csevegőrobotban meghívható végpontot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 
* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy, az ehhez a rövid útmutatóhoz használandó mintát: [Hozzon létre egy új tudásbázist](create-new-kb-csharp.md).

> [!NOTE] 
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-csharp** GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)-adattárból érhetők el.

## <a name="create-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

1. Nyissa meg a Visual Studio 2019 Community Edition verziót.
1. Hozzon létre egy új **Console app (.net Core)** projektet, és `QnaMakerQuickstart`nevezze el a projektet. Fogadja el az alapértelmezett értékeket a többi beállításnál.

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
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
