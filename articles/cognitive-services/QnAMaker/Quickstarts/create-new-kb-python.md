---
title: 'Rövid útmutató: Tudásbázis létrehozása – REST, Python – QnA Maker'
description: Ez a Python REST-alapú rövid útmutató végigvezeti egy olyan minta QnA Maker-tudásbázis programozott módon történő létrehozásán, amely a Cognitive Services API-fiók Azure-irányítópultján fog megjelenni.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 6155722b81b32e426a91a283910e8bdce926e342
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777648"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Rövid útmutató: Tudásbázis létrehozása a QnA Makerben a Python használatával

Ez a rövid útmutató végigvezeti egy minta QnA Maker-tudásbázis programozott módon való létrehozásán és közzétételén. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/knowledge-base.md). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Tudásbázis létrehozása](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Műveletek részleteinek lekérése](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Python-minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.7](https://www.python.org/downloads/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.

## <a name="create-a-knowledge-base-python-file"></a>A tudásbázis Python-fájljának létrehozása

Hozzon létre egy `create-new-knowledge-base-3x.py` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `create-new-knowledge-base-3x.py` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="dependencies":::

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása
A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is. Cserélje le a és a értékét a `<your-qna-maker-subscription-key>` `<your-resource-name>` saját QnA Maker kulcsára és az erőforrás nevére.

A program osztály tetején adja hozzá a szükséges állandókat a QnA Maker eléréséhez.

Állítsa be a következő értékeket:

* `<your-qna-maker-subscription-key>` – A **kulcs** egy 32 karakterből álló karakterlánc, és a Azure Portal QnA Maker erőforrásban, a rövid útmutató lapon érhető el. Ez nem ugyanaz, mint az előrejelzési végpont kulcsa.
* `<your-resource-name>` – Az **Erőforrás neve** a szerzői végpont URL-címének létrehozásához használható a következő formátumban: `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Ez nem ugyanaz az URL-cím, amely az előrejelzési végpont lekérdezésére szolgál.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="constants":::

## <a name="add-the-kb-model-definition"></a>A tudásbázismodell definíciójának hozzáadása

A konstansok után adja hozzá az alábbi tudásbázismodell-definíciót. A modell a definíció után egy sztringgé konvertál.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="model":::

## <a name="add-supporting-function"></a>Támogató függvény hozzáadása

A következő függvények hozzáadásával olvasható formában jelenítheti meg a JSON-fájlt:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="pretty":::

## <a name="add-function-to-create-kb"></a>A tudásbázist létrehozó függvény hozzáadása

Az alábbi függvény hozzáadásával egy HTTP POST-kérés segítségével létrehozhatja a tudásbázist.
Az API egy JSON-választ ad vissza, amely tartalmazza a művelet azonosítóját a **Location** fejlécmezőben. A művelet azonosítója alapján megállapíthatja, hogy a tudásbázis sikeresen létrejött-e. Az `Ocp-Apim-Subscription-Key` QnA Maker szolgáltatás kulcsa, amely a hitelesítéshez használható.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="create_kb":::

Az API egy JSON-választ ad vissza, amely tartalmazza a művelet azonosítóját. A művelet azonosítója alapján megállapíthatja, hogy a tudásbázis sikeresen létrejött-e.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-check-creation-status"></a>Függvény hozzáadása a létrehozás állapotának ellenőrzéséhez

Az alábbi függvény a műveletazonosítónak az URL-útvonal végén való elküldésével ellenőrzi a létrehozás állapotát. A `check_status` meghívása a fő _while_ iteráción belül található.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="get_status":::

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

## <a name="add-main-code-block"></a>A fő kódblokk hozzáadása
Az alábbi iteráció rendszeresen lekérdezi a létrehozási művelet állapotát, amíg a művelet be nem fejeződik.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="main":::

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Írja be a következő parancsot egy parancssorba a program futtatásához. A parancs egy kérést küld a QnA Maker API-ra a tudásbázis létrehozásához, majd 30 másodpercenként lekérdezi az eredményeket. Mindegyik válasz megjelenik a konzolablakban.

```bash
python create-new-knowledge-base-3x.py
```

A tudásbázis létrehozása után a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg azt. Válassza ki a megtekinteni kívánt tudásbázis nevét, például a QnA Maker FAQ (QnA Maker GYIK) nevet.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
