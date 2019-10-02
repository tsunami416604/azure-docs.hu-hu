---
title: 'Gyors útmutató: Válasz kérése a Tudásbázisból – REST, Node. js – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a Node. js REST-alapú rövid útmutató végigvezeti Önt a tudásalapú válasz beszerzésén, programozott módon.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 8ed5225591065082d937f1cac92893886bf90e40
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802942"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Gyors útmutató: Válaszok a Tudásbázisban a Node. js-vel kapcsolatos kérdésekre

Ez a rövid útmutató végigvezeti a közzétett QnA Maker Tudásbázisból származó válasz programozott módon történő beszerzésének lépésein. A Tudásbázis az [adatforrásokból](../Concepts/data-sources-supported.md) , például a GYIK-ből származó kérdéseket és válaszokat tartalmaz. A rendszer elküldi a [kérdést](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a QnA Maker szolgáltatásnak. A [Válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a legfontosabb előre jelzett választ. 

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza ki **kulcsok** alatt **erőforrás-kezelés** az Azure irányítópultján a QnA Maker erőforrás. 
* **Közzétételi** oldal beállításai Ha nem rendelkezik közzétett tudásbázissal, hozzon létre egy üres tudásbázist, majd importáljon egy tudásbázist a **Beállítások** lapon, majd tegye közzé. [Ezt az alapszintű tudásbázist](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)töltheti le és használhatja. 

    A közzétételi oldal beállításai közé tartozik az útvonal értéke, a gazdagép értéke és a EndpointKey érték. 

    ![Közzétételi beállítások](../media/qnamaker-quickstart-get-answer/publish-settings.png)

A rövid útmutató kódja [https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer) a tárházban található. 

## <a name="create-a-nodejs-file"></a>Node. js-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy új fájlt `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `get-answer.js` fájl tetején adja hozzá a szükséges függőségeket a projekthez:

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Ezután adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Ezek az értékek a **közzétételi** lapon jelennek meg, miután közzétette a tudásbázist. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>POST-kérelem hozzáadása a kérdés elküldéséhez és a válasz beszerzéséhez

A következő kód egy HTTPS-kérést küld a QnA Maker APInak, hogy elküldje a kérdést a Tudásbázisnak, és fogadja a választ:

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értéke tartalmazza a karakterláncot `EndpointKey`. 

## <a name="install-the-dependencies"></a>A függőségek telepítése

A függőségek telepítése a parancssorból:

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>A program futtatása

Futtassa a programot a parancssorból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

Futtassa a fájlt:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

További információ a [kérelemről](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és a [válaszról](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
