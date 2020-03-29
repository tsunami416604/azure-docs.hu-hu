---
title: 'Rövid útmutató: Tudásbázis közzététele, REST, C# - QnA Maker'
description: Ez a C# REST-alapú gyorsindítás közzéteszi a tudásbázist, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobotban.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 0a2f64795d82928e33a10c7e9d162f2333cdf7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851706"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a C# használatával

Ez a REST-alapú rövid útmutató végigvezeti a tudásbázis (KB) programozott közzétételén. A közzététel leküldéses a tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search index, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegőrobot.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs és a végpont (amely tartalmazza az erőforrás nevét) lekéréséhez válassza az erőforrás **gyorsindítását** az Azure Portalon.
* A QnA Maker tudásbázisának (KB) azonosítója megtalálható az URL-címben a lekérdezési karakterlánc paraméterben az `kbid` alábbiak szerint.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE]
> A teljes megoldásfájl(ok) az [ **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub repository-ból](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

1. Nyissa meg a Visual Studio 2019 közösségi kiadását.
1. Hozzon létre egy új **Console App (.NET Core) projektet,** és nevezze el a projektet. `QnaMakerQuickstart` Fogadja el az alapértelmezett értékeket a többi beállításnál.

## <a name="add-required-dependencies"></a>Szükséges függőségek hozzáadása

A Program.cs tetején cserélje le az önálló using utasítást a következő sorokkal, hogy felvegye a szükséges függőségeket a projektbe:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **Program** osztályban adja hozzá a QnA Maker eléréséhez szükséges állandókat.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=8-34 "Add the required constants")]

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>A tudásbázis közzétételéhez a Fő metódus hozzáadása

A szükséges állandók után adja hozzá a következő kódot, amely HTTPS-kérelmet küld a QnA Maker API-hoz egy tudásbázis közzétételéhez, és megkapja a választ:

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=36-56 "Add HTTP Post request and response")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot. Automatikusan elküldi a kérelmet a QnA Maker API-nak a tudásbázis közzétételére, majd a válasz kikerül a konzolablakba.

Miután a tudásbázis közzé lett téve, lekérdezheti a tartalmát a végpontról egy ügyfélalkalmazás vagy csevegőrobot használatával.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

A tudásbázis közzététele után a [végpont URL-címére](./get-answer-from-knowledge-base-csharp.md)van szükség a válasz létrehozásához.

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)