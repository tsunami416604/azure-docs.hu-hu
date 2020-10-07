---
title: 'Gyors útmutató: Válasz kérése a Tudásbázisból – REST, Node.js – QnA Maker'
description: Ez a Node.js REST-alapú rövid útmutató végigvezeti egy adott Tudásbázisból származó válasz beszerzésének lépésein.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: f14a2f4ffcc0c136e0770235003cbb2b6f4ff9f8
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777590"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Gyors útmutató: a Tudásbázis kérdéseire kaphat választ Node.js

Ez a rövid útmutató végigvezeti a közzétett QnA Maker Tudásbázisból származó válasz programozott módon történő beszerzésének lépésein. A Tudásbázis az [adatforrásokból](../Concepts/knowledge-base.md) , például a GYIK-ből származó kérdéseket és válaszokat tartalmaz. A rendszer elküldi a [kérdést](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a QnA Maker szolgáltatásnak. A [Válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a legfontosabb előre jelzett választ.

[Dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime)  |  [Minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza a **kulcsok** elemet az **Erőforrás-kezelés** területen az QnA Maker erőforráshoz tartozó Azure-irányítópulton.
* **Közzétételi** oldal beállításai Ha nem rendelkezik közzétett tudásbázissal, hozzon létre egy üres tudásbázist, majd importáljon egy tudásbázist a **Beállítások** lapon, majd tegye közzé. [Ezt az alapszintű tudásbázist](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)töltheti le és használhatja.

    A közzétételi oldal beállításai közé tartozik az útvonal értéke, a gazdagép értéke és a EndpointKey érték.

    ![Publish settings (Közzétételi beállítások)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Node.js-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy nevű új fájlt `get-answer.js` .

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `get-answer.js` fájl tetején adja hozzá a szükséges függőségeket a projekthez:

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="dependencies":::

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

Ezután adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Ezek az értékek a **közzétételi** lapon jelennek meg, miután közzétette a tudásbázist.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>POST-kérelem hozzáadása a kérdés elküldéséhez és a válasz beszerzéséhez

A következő kód egy HTTPS-kérést küld a QnA Maker APInak, hogy elküldje a kérdést a Tudásbázisnak, és fogadja a választ:

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="query":::

## <a name="run-the-program"></a>A program futtatása

Futtassa a programot a parancssorból. A szolgáltatás automatikusan elküldi a kérést a QnA Maker APInak, majd a konzol ablakba fog nyomtatni.

Futtassa a fájlt:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

További információ a [kérelemről](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és a [válaszról](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
