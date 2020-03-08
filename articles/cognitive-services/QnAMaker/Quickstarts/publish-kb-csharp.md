---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, C# -QnA Maker'
description: Ez C# a REST-alapú rövid útmutató közzéteszi a tudásbázist, és létrehoz egy olyan végpontot, amely hívható az alkalmazásban vagy a csevegési robotban.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 0a2f64795d82928e33a10c7e9d162f2333cdf7bf
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851706"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a C# használatával

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A Publishing leküldi a Tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search indexre, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegési robotban.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.
* QnA Maker Tudásbázis-azonosító a `kbid` lekérdezési karakterlánc paraméterben az alább látható módon található.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE]
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-csharp** GitHub-adattárból](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

1. Nyissa meg a Visual Studio 2019 Community Edition verziót.
1. Hozzon létre egy új **Console app (.net Core)** projektet, és nevezze el a projektet `QnaMakerQuickstart`. Fogadja el az alapértelmezett értékeket a többi beállításnál.

## <a name="add-required-dependencies"></a>Szükséges függőségek hozzáadása

A Program.cs tetején cserélje le az önálló using utasítást a következő sorokkal, hogy felvegye a szükséges függőségeket a projektbe:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **program** osztályban adja hozzá a szükséges állandókat a QnA Maker eléréséhez.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>A Tudásbázis közzétételéhez adja hozzá a Main metódust

A szükséges állandókat után adja hozzá a következő kódra, amely egy HTTPS-kérést küld a QnA Maker API Tudásbázis közzététele, és megkapja a választ:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot. A kérelem automatikusan elküldi a QnA Maker API a Tudásbázis közzététele, majd a konzolablakban a választ ki a program.

Miután a tudásbázis közzé lett téve, lekérdezheti a tartalmát a végpontról egy ügyfélalkalmazás vagy csevegőrobot használatával.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Miután közzétette a tudásbázist, szüksége lesz a [végpont URL-címére a válasz létrehozásához](./get-answer-from-knowledge-base-csharp.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)