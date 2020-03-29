---
title: 'Rövid útmutató: Válasz kérése a tudásbázistól - REST, C# - QnA Maker'
description: Ez a C# REST-alapú rövid útmutató végigvezeti a választ kap egy tudásbázis, programozott módon.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 16093ec5e837b098da3c9b038fe2a57cd76c7151
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851805"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>Rövid útmutató: Válaszok egy kérdésre egy c-vel rendelkező tudásbázisból #

Ez a rövid útmutató végigvezeti a programozott beolvasása egy közzétett QnA Maker tudásbázis. A tudásbázis olyan [adatforrásokból](../Concepts/knowledge-base.md) származó kérdéseket és válaszokat tartalmaz, mint például a gyakori kérdések. A kérdést a [rendszer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) elküldi a QnA Maker szolgáltatásnak. A [válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a felül előre jelzett választ.

[Referenciadokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza **a Kulcsok** az **Erőforrás-kezelés** az Azure-irányítópulton a QnA Maker erőforrás.
* **Lapbeállítások közzététele.** Ha nem rendelkezik közzétett tudásbázissal, hozzon létre egy üres tudásbázist, majd importáljon egy tudásbázist a **Beállítások** lapon, majd tegye közzé. Letöltheti és használhatja [ezt az alapvető tudásbázist.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    A közzétételi lap beállításai közé tartozik a POST útvonal érték, az Állomás érték és az EndpointKey érték.

    ![Publish settings (Közzétételi beállítások)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

1. Nyissa meg a Visual Studio 2019 közösségi kiadását.
1. Hozzon létre egy új Konzol alkalmazás (.NET Core) projektet, és nevezze el a projektet QnaMakerQuickstart néven. Fogadja el az alapértelmezett értékeket a többi beállításnál.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A Program.cs fájl tetején cserélje le az egyszeres utasítást a következő sorokra a szükséges függőségek hozzáadásához a projekthez:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Az `Program` osztály tetején, a `Main`belül, adja hozzá a szükséges állandók eléréséhez QnA Maker. Ezek az értékek a tudásbázis közzététele után a **Közzététel** lapon találhatók.

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Post-kérelem hozzáadása kérdés küldéséhez és válaszkéréshez

A következő kód https-kérelmet küld a QnA Maker API-nak, hogy küldje el a kérdést a tudásbázisnak, és megkapja a választ:

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értéke tartalmazza `EndpointKey`a karakterláncot .

További információ a [kérésről](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és a [válaszról.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

A program létrehozása és futtatása a Visual Studio-ból. Automatikusan elküldi a kérelmet a QnA Maker API-nak, majd kinyomtatja a konzolablakba.

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)