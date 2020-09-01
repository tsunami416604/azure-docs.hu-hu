---
title: 'Rövid útmutató: Tudásbázis létrehozása – REST, Go – QnA Maker'
description: Ez a Go REST-alapú rövid útmutató végigvezeti egy olyan minta QnA Maker-tudásbázis programozott módon történő létrehozásán, amely a Cognitive Services API-fiók Azure-irányítópultján fog megjelenni.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 38bfada87701533002a2dcf304af573da4c87766
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259897"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Rövid útmutató: Tudásbázis létrehozása a QnA Makerben a Go használatával

Ez a rövid útmutató végigvezeti egy minta QnA Maker-tudásbázis programozott módon való létrehozásán. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/knowledge-base.md). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Tudásbázis létrehozása](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Műveletek részleteinek lekérése](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Minta ugrás](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Go 1.10.1](https://golang.org/dl/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.

## <a name="create-a-knowledge-base-go-file"></a>Egy tudásbázis Go-fájljának létrehozása

Hozzon létre egy `create-new-knowledge-base.go` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `create-new-knowledge-base.go` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>A tudásbázismodell definíciójának hozzáadása
A konstansok után adja hozzá az alábbi tudásbázismodell-definíciót. A modell a definíció után egy sztringgé konvertál.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Támogató struktúrák és függvények hozzáadása

Ezután adja hozzá a következő támogató függvényeket.

1. Adja hozzá a következő struktúrát egy HTTP-válaszhoz:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Adja hozzá a következő metódust a QnA Maker API-knak címzett POST-kérés kezeléséhez. Ebben a rövid útmutatóban a POST-ot arra használjuk, hogy a tudásbázis-definíciót elküldje a QnA Makernek.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Adja hozzá a következő metódust a QnA Maker API-knak címzett GET-kérés kezeléséhez. Ebben a rövid útmutatóban a GET-et arra használjuk, hogy ellenőrizzük a létrehozási művelet állapotát.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>A tudásbázist létrehozó függvény hozzáadása

Az alábbi függvények hozzáadásával egy HTTP POST-kérés segítségével létrehozhatja a tudásbázist. A _létrehozás_ **műveletazonosítót** a rendszer a POST válasz **Hely** fejlécmezőjében adja vissza, majd a GET kérésben az útvonal részeként használja. Az `Ocp-Apim-Subscription-Key` QnA Maker szolgáltatás kulcsa, amely a hitelesítéshez használható.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

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

## <a name="add-function-to-get-status"></a>Állapotlekérdező függvény hozzáadása

Az alábbi függvény hozzáadásával egy HTTP POST-kérés segítségével ellenőrizheti a művelet állapotát. Az `Ocp-Apim-Subscription-Key` QnA Maker szolgáltatás kulcsa, amely a hitelesítéshez használható.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

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
## <a name="add-main-function"></a>A fő függvény hozzáadása

A következő függvény a fő függvény, amely létrehozza a tudásbázist, és ismétlődő módon ellenőrzi a létrehozás állapotát. Mivel a tudásbázis létrehozása eltarthat egy ideig, előfordulhat, hogy többször is meg kell ismételnie az állapot-ellenőrző hívásokat, amíg az állapot sikeresnek vagy sikertelennek nem bizonyul.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


## <a name="compile-the-program"></a>A program lefordítása
Írja be a következő parancsot a fájl lefordításához. A parancssor nem adja vissza a sikeres buildek információit.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>A program futtatása

Írja be a következő parancsot egy parancssorba a program futtatásához. A parancs egy kérést küld a QnA Maker API-ra a tudásbázis létrehozásához, majd 30 másodpercenként lekérdezi az eredményeket. Mindegyik válasz megjelenik a konzolablakban.

```bash
go run create-new-knowledge-base
```

A tudásbázis létrehozása után a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg azt.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
