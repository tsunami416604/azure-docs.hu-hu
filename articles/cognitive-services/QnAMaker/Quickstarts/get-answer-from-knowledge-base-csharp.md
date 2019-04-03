---
title: 'Gyors útmutató: Első válasz a Tudásbázis - REST, C# – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ez C# REST-alapú a rövid útmutató végigvezeti a válasz lekérése a Tudásbázis programozott módon.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 8ded7a86c1b4fb53ca81fc4e7b0c0d794c515ddf
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886342"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-c"></a>A kérdésekre adott válaszok a a Tudásbázis beolvasásaC#

Ez a rövid útmutató végigvezeti programozott módon első válasz a QnA Maker közzétett Tudásbázis. A Tudásbázis kérdéseket tartalmaz, valamint választ ad a [adatforrások](../Concepts/data-sources-supported.md) például – gyakori kérdések. A [kérdés](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a QnA Maker szolgáltatásnak továbbítja. A [válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a top előre jelzett választ. 


## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza ki **kulcsok** alatt **erőforrás-kezelés** az Azure irányítópultján a QnA Maker erőforrás. 
* **Közzététel** beállítások lapon. Közzétett Tudásbázis nem rendelkezik, hozzon létre egy üres Tudásbázis, akkor a Tudásbázis importálja a **beállítások** lapon, majd közzéteheti. Letöltheti és használhatja [Ez a alapvető Tudásbázis](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    A közzétételi oldalon beállítások közé tartozik a POST útvonal, gazdagép értékét, és EndpointKey értékkel. 

    ![Közzétételi beállítások](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Ez a rövid útmutató kódja megtalálható a [ https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/get-answer) tárház. 

## <a name="create-a-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

1. Nyissa meg a Visual Studio 2017 Community Editiont.
1. Hozzon létre egy új Konzolalkalmazás (.NET Core) projektet és neve a projekt QnaMakerQuickstart. Fogadja el az alapértelmezett értékeket a többi beállításnál.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A Program.cs fájl elején cserélje le az egyetlen utasítás használatával a következő sorokat a szükséges függőségek hozzáadása a projekthez:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Felső részén a `Program` osztályhoz, belül a `Main`, adja hozzá a szükséges állandókat a QnA Maker eléréséhez. A rendszer ezeket az értékeket a **közzététel** lapon a Tudásbázis közzététele után. 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=14-30 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adjon hozzá egy POST-kérés küldése a kérdés és válasz

A következő kódot egy HTTPS-kérést küld a QnA Maker API, a kérdés küldeni a Tudásbázis és a választ kap:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=32-57 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értékének tartalmazza a karakterláncot `EndpointKey`. 

Tudjon meg többet a [kérelem](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és [válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response). 

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozhat létre, és futtassa a programot a Visual Studióból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
