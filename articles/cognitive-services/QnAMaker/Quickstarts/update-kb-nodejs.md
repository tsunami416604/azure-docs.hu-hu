---
title: 'Rövid útmutató: Tudásbázis frissítése – REST, Node.js – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató végigvezeti a meglévő QnA Maker-tudásbázisok (KB) programozott módon való frissítésén.  Ez a JSON lehetővé teszi, hogy adatforrások hozzáadásával, módosításával vagy törlésével frissítse a tudásbázist.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 1b92589fe01f171b732c04057dc290f0f32cb31a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647816"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-nodejs"></a>Rövid útmutató: Tudásbázis frissítése a QnA Makerben a Node.js használatával

Ez a rövid útmutató végigvezeti egy meglévő QnA Maker tudásbázis (KB) programozott módon való frissítésén.  Ez a JSON lehetővé teszi, hogy adatforrások hozzáadásával, módosításával vagy törlésével frissítse a tudásbázist.

Ez az API egyenértékű azzal, mintha szerkesztést végezne, majd a QnA Maker portál **Save and train** (Mentés és betanítás) gombját használná.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Frissítés](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) – A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja. 
* [Műveletek részleteinek lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Előfeltételek

* [Node.js 6+](https://nodejs.org/en/download/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 
* A QnA Maker tudásbázis (KB) azonosítója az URL-címben található a kbid lekérdezésisztring-paraméterben, amint az alább látható.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-nodejs.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>Node.js-tudásbázisfájl létrehozása

Hozzon létre egy `update-knowledge-base.js` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `update-knowledge-base.js` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása
A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is. Cserélje le a `subscriptionKey` változó értéket a saját QnA Maker-kulcsára. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>Tudásbázis-azonosító hozzáadása

A fenti konstansok után adja hozzá a tudásbázis azonosítóját is, és adja hozzá az elérési úthoz:

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>A tudásbázis-frissítési modell definíciójának hozzáadása

A konstansok után adja hozzá az alábbi tudásbázis-frissítési definíciót. A frissítési definíció három szakaszból áll:

* add
* update
* delete

Mindegyik szakasz használható ugyanabban az API-kérésben. 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>Támogató függvények hozzáadása

Ezután adja hozzá a következő támogató függvényeket.

1. A következő függvények hozzáadásával olvasható formában jelenítheti meg a JSON-fájlt:

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. Adja hozzá a következő függvényeket, hogy a HTTP-válasz kezelésével lekérje a létrehozási művelet állapotát:

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>PATCH-kérés hozzáadása a tudásbázis frissítése érdekében

Az alábbi függvények hozzáadásával HTTP PATCH-kérésekkel frissítheti a tudásbázist. Az `Ocp-Apim-Subscription-Key` QnA Maker szolgáltatás kulcsa, amely a hitelesítéshez használható.

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

Az API egy JSON-választ ad vissza, amely tartalmazza a művelet azonosítóját. A műveletazonosító szükséges az állapot lekéréséhez, ha a művelet még nem fejeződött be.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>GET-kérés hozzáadása a művelet állapotának meghatározására

Ellenőrizze a művelet állapotát.
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

Az API-hívás egy JSON-választ ad vissza, amely tartalmazza a művelet állapotát: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Ismételje a hívást, amíg nem sikerül vagy meg nem hiúsul: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>update_kb metódus hozzáadása

A következő metódust frissíti a tudásbázist, és megismétli az állapotellenőrzést. Mivel a tudásbázis létrehozása eltarthat egy ideig, előfordulhat, hogy többször is meg kell ismételnie az állapot-ellenőrző hívásokat, amíg az állapot sikeresnek vagy sikertelennek nem bizonyul.

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>A program futtatása

Írja be a következő parancsot egy parancssorba a program futtatásához. A parancs egy kérést küld a QnA Maker API-ra a tudásbázis frissítéséhez, majd 30 másodpercenként lekérdezi az eredményeket. Mindegyik válasz megjelenik a konzolablakban.

```bash
node update-knowledge-base.js
```

A frissített tudásbázist a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)