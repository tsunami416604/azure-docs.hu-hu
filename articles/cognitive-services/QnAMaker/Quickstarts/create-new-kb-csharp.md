---
title: Hozzon létre Tudásbázis – RESTC#
titlesuffix: QnA Maker- Azure Cognitive Services
description: Ez a C# REST-alapú rövid útmutató végigvezeti egy olyan minta QnA Maker-tudásbázis programozott módon történő létrehozásán, amely a Cognitive Services API-fiók Azure-irányítópultján fog megjelenni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: bf02247008c79c48eedd09e68c81db1aa7634146
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225185"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>Gyors útmutató: Tudásbázis létrehozása a QnA Maker használatávalC#

Ez a rövid útmutató végigvezeti egy minta QnA Maker-tudásbázis programozott módon való létrehozásán és közzétételén. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/data-sources-supported.md). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja. 

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Tudásbázis létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Műveletek részleteinek lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Közzététel](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 

> [!NOTE] 
> A teljes megoldás fájl(ok) érhetők el a [ **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp).

## <a name="create-a-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A Program.cs tetején cserélje le az önálló using utasítást a következő sorokkal, hogy felvegye a szükséges függőségeket a projektbe:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

A Program osztály tetején adja hozzá a következő konstansokat a QnA Maker elérése érdekében:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-24 "Add the required constants")]

## <a name="add-the-kb-definition"></a>A tudásbázis-definíció hozzáadása

A konstansok után adja hozzá az alábbi tudásbázis-definíciót:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=32-57 "Add the required constants")]

## <a name="add-supporting-functions-and-structures"></a>Támogató függvények és struktúrák hozzáadása
Adja hozzá a következő kódblokkot a Program osztályon belül:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=62-82 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>POST kérés hozzáadása a tudásbázis létrehozására

A következő kód egy HTTPS-kérést küld a QnA Maker API-ra egy tudásbázis létrehozásához, és a következő választ kapja:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=91-105 "Add a POST request to create KB")]

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

## <a name="add-get-request-to-determine-creation-status"></a>GET-kérés hozzáadása a létrehozás állapotának meghatározásához

Ellenőrizze a művelet állapotát.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=159-170 "Add GET request to determine creation status")]

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

## <a name="add-createkb-method"></a>CreateKB metódus hozzáadása

A következő metódus létrehozza a tudásbázist, és megismétli az állapotellenőrzést.  A _létrehozás_ **műveletazonosítót** a rendszer a POST-válasz **Hely** fejlécmezőjében adja vissza, majd a GET-kérésben az útvonal részeként használja. Mivel a tudásbázis létrehozása eltarthat egy ideig, előfordulhat, hogy többször is meg kell ismételnie az állapot-ellenőrző hívásokat, amíg az állapot sikeresnek vagy sikertelennek nem bizonyul. Ha a művelet sikeres, a tudásbázis azonosítóját adja vissza a **resourceLocation** változóban. 

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=176-237 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>A CreateKB metódus hozzáadása a Main metódushoz

A Main metódus módosítása a CreateKB metódus meghívására:

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=239-248 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot. A parancs automatikusan egy kérést küld a QnA Maker API-ra a tudásbázis létrehozásához, majd 30 másodpercenként lekérdezi az eredményeket. Mindegyik válasz megjelenik a konzolablakban.

A tudásbázis létrehozása után a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg azt. 


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
