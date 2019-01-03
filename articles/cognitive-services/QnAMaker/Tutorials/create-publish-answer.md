---
title: Hozzon létre, közzététel, választ
titleSuffix: QnA Maker - Azure Cognitive Services
description: Ez a REST-alapú oktatóanyag végigvezeti egy tudásbázis programozott módon történő létrehozásának és közzétételének, majd egy kérdés a tudásbázisból való megválaszolásának folyamatán.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: diberry
ms.openlocfilehash: f178256180cfc75467a3dea2c7ac8d53fe9dd26a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995167"
---
# <a name="tutorial-using-c-create-knowledge-base-then-answer-question"></a>Oktatóanyag: Használatával C#, hozzon létre alapszintű majd kapcsolatos kérdésére választ kaphat

Ez az oktatóanyag végigvezeti egy tudásbázis (KB) programozott módon történő létrehozásának és közzétételének, majd egy ügyféltől származó kérdés a tudásbázisból való megválaszolásának folyamatán. 

> [!div class="checklist"]
* Tudásbázis létrehozása 
* Létrehozás állapotának ellenőrzése
* A tudásbázis betanítása és közzététele
* A végpont információinak lekérése
* CURL használata a tudásbázis lekérdezéséhez


Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:

* [Tudásbázis (KB) létrehozása](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Műveletek részleteinek lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [Tudásbázis részleteinek lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails) 
* [Tudásbázis végpontjainak lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys)
* [Közzététel](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**Visual Studio Community kiadás**](https://www.visualstudio.com/downloads/).
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza az irányítópulton az **Erőforrás-kezelés** területen lévő **Kulcsok** lehetőséget. 

> [!NOTE] 
> A teljes megoldás fájl(ok) érhetők el a [ **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="create-a-knowledge-base-project"></a>Tudásbázisprojekt létrehozása

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A Program.cs tetején cserélje le az önálló _using_ utasítást a következő sorokkal, hogy felvegye a szükséges függőségeket a projektbe:

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-a-kbdetails-class"></a>Egy KBDetails (Tudásbázis részletei) osztály hozzáadása
Adja hozzá ezt KBDetails osztályt a névtér zárójelei között. Ez az osztály lehetővé teszi, hogy a NewtonSoft kódtár egy C#-osztályba deszerializálja a JSON-választ.

[!code-csharp[Add a KBDetails class](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=15-26 "Add a KBDetails class")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

A Program osztály tetején adja hozzá a következő konstansokat a QnA Maker elérése érdekében:

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=30-57 "Add the required constants")]

## <a name="add-the-kb-definition"></a>A tudásbázis-definíció hozzáadása

A konstansok után adja hozzá az alábbi tudásbázismodell-definíciót:

[!code-csharp[Add the KB definition](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=59-85 "Add the KB definition")]

## <a name="add-supporting-functions-and-structures"></a>Támogató függvények és struktúrák hozzáadása
Adja hozzá a következő kódblokkot a Program osztályon belül:

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=87-123 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>POST kérés hozzáadása a tudásbázis létrehozására

A következő kód egy HTTPS-kérést küld a QnA Maker API-ra egy tudásbázis létrehozásához, és a következő választ kapja:

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=124-141 "Add a POST request to create KB")]

Az API egy JSON-választ ad vissza, amely tartalmazza a művelet azonosítóját. Későbbiekben a program a művelet azonosítója alapján megállapíthatja, hogy a tudásbázis sikeresen létrejött-e. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>GET-kérés hozzáadása a létrehozás állapotának meghatározásához

Ellenőrizze a létrehozás művelet állapotát.

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=142-151 "Add GET request to determine creation status")]

Az API-hívás egy JSON-választ ad vissza, amely tartalmazza a művelet állapotát: 

```JSON
{
  "operationState": "Running",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
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
  "operationId": "YYYe12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>CreateKB metódus hozzáadása

A következő metódus magában foglalja a tudásbázis létrehozásához és az állapot ellenőrzéséhez szükséges hívásokat.  A _létrehozás_ **műveletazonosítót** a rendszer a POST-válasz **Hely** fejlécmezőjében adja vissza, majd a GET-kérésben az útvonal részeként használja. Mivel a tudásbázis létrehozása eltarthat egy ideig, előfordulhat, hogy többször is meg kell ismételnie az állapot-ellenőrző hívásokat, amíg az állapot sikeresnek vagy sikertelennek nem bizonyul. Ha a művelet sikeres, a tudásbázis azonosítóját adja vissza a **resourceLocation** változóban. 

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=152-227 "Add GET request to determine creation status")]

## <a name="add-publish-method"></a>Közzétételi metódus hozzáadása

A tudásbázis sikeres létrehozása után végezze el a tudásbázis közzétételét. Elképzelhető, hogy egy képzési API-ra mutató hívásra számított. Ebben a verzióban erre nincs szükség. 

A következő kód egy HTTPS-kérést küld a QnA Maker API-ra egy KB közzétételéhez, és a következő választ kapja:

[!code-csharp[Add publish method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=228-259 "Add publish method")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A rövid útmutatóban szereplő kód 204 válaszhoz ad hozzá szöveget, hogy megtekinthesse az eredményt.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="generating-an-answer"></a>Válasz létrehozása
Ahhoz, hogy egy kérdés elküldése és a legjobb válasz lekérése céljából hozzáférhessen a tudásbázishoz, a programnak egy, a tudásbázis részletei API-ból származó _végpont gazdagépre_, és a végpontok API-ból származó _elsődleges végpont kulcsra_ van szüksége. Ezek a metódusok a válasz összeállításához szükséges metódussal együtt a következő szakaszokban találhatók. 

Az alábbi táblázat bemutatja, hogyan használja a rendszer az adatokat az URI létrehozására:

|Válasz URI-sablon létrehozása|
|--|
|https://**HOSTNAME**.azurewebsites.net/qnamaker/knowledgebases/**KBID**/generateAnswer|

Az _elsődleges végpont_ fejlécként továbbítódik a válasz létrehozására vonatkozó kérelem hitelesítéséhez:

|Fejléc neve|Fejléc értéke|
|--|--|
|Engedélyezés|`Endpoint ` + **elsődleges végpont**<br>Például: `Endpoint xxxxxxx`<br>Figyelje meg, hogy a `Endpoint` szöveg és az elsődleges végpont értéke között egy szóköz szerepel. 

A kérelem törzsének a megfelelő JSON-t kell továbbítania:

```JSON
{
    question: "What languages does QnA Maker support?"
}
```

## <a name="get-kb-details"></a>Tudásbázis részleteinek lekérése
Adja hozzá a következő metódust a tudásbázis részleteinek lekéréséhez. Ezek a részletek tartalmazzák a tudásbázis gazdagépnevét. A gazdagépnév a QnA Maker azon Azure-beli webszolgáltatásának neve, amelyet a QnA Maker-erőforrás létrehozásakor megadott. 

[!code-csharp[Get KB Details](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=260-273 "Add publish method")]

Ez az API-hívás egy JSON-választ ad vissza: 

```JSON
{
  "id": "ZZZ31e19-cba7-48d1-8594-5c4297ecc9c1",
  "hostName": "https://qnamaker-s0-s.azurewebsites.net",
  "lastAccessedTimestamp": "2018-10-11T18:10:05Z",
  "lastChangedTimestamp": "2018-10-11T18:09:37Z",
  "lastPublishedTimestamp": "2018-10-11T18:10:15Z",
  "name": "QnA Maker FAQ from quickstart",
  "userId": "AAAc3841df0b42cdb00f53a49d51a89c",
  "urls": [
    "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

## <a name="get-kb-endpoints"></a>Tudásbázis végpontjainak lekérése
Adja hozzá a következő metódust a QnA Maker elsődleges végpontjainak lekéréséhez. Ezek a végpontok nem kizárólagosan kapcsolódnak a tudásbázishoz, hanem a QnA Maker Azure Portalról származó erőforráskulcsaihoz társított összes tudásbázishoz érvényesek.  

[!code-csharp[Get KB Endpoints](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=274-289 "Get KB Endpoints")]

Ez az API-hívás egy JSON-választ ad vissza: 

```JSON
{
  "primaryEndpointKey": "AAAb5719-e2f7-4a33-937d-7a3b4736a1be",
  "secondaryEndpointKey": "BBBcba78-c1d2-4166-b98f-c77255aefaba",
  "installedVersion": "4.2.0",
  "lastStableVersion": "4.2.0"
}
```

## <a name="get-an-answer"></a>Válasz lekérése
Adja hozzá a következő metódust, amellyel lekérheti a felhasználó kérdéséhez kapcsolódó választ. 

[!code-csharp[Get Answer](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=290-315 "Get Answer")]

Ez az API-hívás egy JSON-választ ad vissza: 

```JSON
{
  "answers": [
    {
      "questions": [
        "Does QnA Maker support non-English languages?"
      ],
      "answer": "See more details about [supported languages](https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/overview/languages-supported).\n\n\nIf you have content from multiple languages, be sure to create a separate service for each language.",
      "score": 82.19,
      "id": 11,
      "source": "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```

## <a name="main-method"></a>A fő metódus
A fő metódus a válasz létrehozásához, közzétételéhez és összeállításához szükséges szinkron hívásokat jeleníti meg. 

[!code-csharp[Main method](~/samples-qnamaker-csharp/documentation-samples/tutorials/create-publish-answer-knowledge-base/QnaMakerQuickstart/Program.cs?range=316-337 "Main method")]

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot. 

A tudásbázis létrehozása után a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg azt. Ha már tudja, hogyan használható a válasz összeállítása API, bármely nyelvvel vagy HTTP-kérelem-keretrendszerrel használhatja az API-t. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
