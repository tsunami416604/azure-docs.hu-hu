---
title: 'Rövid útmutató: Válasz kérése a tudásbázistól - REST, Node.js - QnA Maker'
description: Ez a Node.js REST-alapú rövid útmutató végigvezeti a tudásbázistól, programozott módon történő válasz beszerzésén.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 9031c320b055c0a7191655d6531eaa31d9cc8bb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851728"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Rövid útmutató: Válaszok a kérdésre egy tudásbázisból a Node.js

Ez a rövid útmutató végigvezeti a programozott beolvasása egy közzétett QnA Maker tudásbázis. A tudásbázis olyan [adatforrásokból](../Concepts/knowledge-base.md) származó kérdéseket és válaszokat tartalmaz, mint például a gyakori kérdések. A kérdést a [rendszer](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) elküldi a QnA Maker szolgáltatásnak. A [válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a felül előre jelzett választ.

[Referenciadokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio kód](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza **a Kulcsok** az **Erőforrás-kezelés** az Azure-irányítópulton a QnA Maker erőforrás.
* **Lapbeállítások közzététele.** Ha nem rendelkezik közzétett tudásbázissal, hozzon létre egy üres tudásbázist, majd importáljon egy tudásbázist a **Beállítások** lapon, majd tegye közzé. Letöltheti és használhatja [ezt az alapvető tudásbázist.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)

    A közzétételi lap beállításai közé tartozik a POST útvonal érték, az Állomás érték és az EndpointKey érték.

    ![Publish settings (Közzétételi beállítások)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Node.js fájl létrehozása

Nyissa meg a VSCode `get-answer.js`programot, és hozzon létre egy új fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A fájl tetején `get-answer.js` adja hozzá a szükséges függőségeket a projekthez:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Ezután adja hozzá a QnA Maker eléréséhez szükséges állandókat. Ezek az értékek a tudásbázis közzététele után a **Közzététel** lapon találhatók.

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Post-kérelem hozzáadása a kérdés küldéséhez és a válasz kéréséhez

A következő kód https-kérelmet küld a QnA Maker API-nak, hogy küldje el a kérdést a tudásbázisnak, és megkapja a választ:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értéke tartalmazza `EndpointKey`a karakterláncot .

## <a name="install-the-dependencies"></a>A függőségek telepítése

Telepítse a függőségeket a parancssorból:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>A program futtatása

Futtassa a programot a parancssorból. Automatikusan elküldi a kérelmet a QnA Maker API-nak, majd kinyomtatja a konzolablakba.

Futtassa a fájlt:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

További információ a [kérésről](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és a [válaszról.](../how-to/metadata-generateanswer-usage.md#generateanswer-response)

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)