---
title: 'Gyors útmutató: Hozzon létre a Tudásbázis - REST, a Go - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ez a Go REST-alapú rövid útmutató végigvezeti egy olyan minta QnA Maker-tudásbázis programozott módon történő létrehozásán, amely a Cognitive Services API-fiók Azure-irányítópultján fog megjelenni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: 4019f323ae1f4bb4f5f60f5a691ebced639d81de
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208125"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Gyors útmutató: Tudásbázis létrehozása a QnA Maker Go használatával

Ez a rövid útmutató végigvezeti egy minta QnA Maker-tudásbázis programozott módon való létrehozásán. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/data-sources-supported.md). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja. 

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Tudásbázis létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Műveletek részleteinek lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Előfeltételek

* [Go 1.10.1](https://golang.org/dl/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 

## <a name="create-a-knowledge-base-go-file"></a>Egy tudásbázis Go-fájljának létrehozása

Hozzon létre egy `create-new-knowledge-base.go` nevű fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A `create-new-knowledge-base.go` tetején a következő sorok hozzáadásával adja hozzá a szükséges függőségeket a projekthez:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása
A fenti szükséges függőségek után adja hozzá a QnA Maker eléréséhez szükséges konstansokat is. Cserélje le a `subscriptionKey` változó értéket a saját QnA Maker-kulcsára.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>A tudásbázismodell definíciójának hozzáadása
A konstansok után adja hozzá az alábbi tudásbázismodell-definíciót. A modell a definíció után egy sztringgé konvertál.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>Támogató struktúrák és függvények hozzáadása

Ezután adja hozzá a következő támogató függvényeket.

1. Adja hozzá egy HTTP-kérés struktúráját:

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. Adja hozzá a következő metódust a QnA Maker API-knak címzett POST-kérés kezeléséhez. Ebben a rövid útmutatóban a POST-ot arra használjuk, hogy a tudásbázis-definíciót elküldje a QnA Makernek.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. Adja hozzá a következő metódust a QnA Maker API-knak címzett GET-kérés kezeléséhez. Ebben a rövid útmutatóban a GET-et arra használjuk, hogy ellenőrizzük a létrehozási művelet állapotát. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>A tudásbázist létrehozó függvény hozzáadása

Az alábbi függvények hozzáadásával egy HTTP POST-kérés segítségével létrehozhatja a tudásbázist. A _létrehozás_ **műveletazonosítót** a rendszer a POST-válasz **Hely** fejlécmezőjében adja vissza, majd a GET-kérésben az útvonal részeként használja. Az `Ocp-Apim-Subscription-Key` QnA Maker szolgáltatás kulcsa, amely a hitelesítéshez használható. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


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
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
