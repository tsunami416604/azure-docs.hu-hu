---
title: 'Rövid útmutató: Tudásbázis létrehozása – REST, C# – QnA Maker'
description: Ez a C# REST-alapú rövid útmutató végigvezeti egy olyan minta QnA Maker-tudásbázis programozott módon történő létrehozásán, amely a Cognitive Services API-fiók Azure-irányítópultján fog megjelenni.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: e31345c3b83e1ff5e01952d69dde9353b8234757
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338112"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Gyors útmutató: Tudásbázis létrehozása QnA Maker C# használatával a REST segítségével

Ez a rövid útmutató végigvezeti egy minta QnA Maker-tudásbázis programozott módon való létrehozásán és közzétételén. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../index.yml). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Tudásbázis létrehozása](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Műveletek részleteinek lekérése](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Dokumentáció](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [C# minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.
* Rendelkeznie kell [QnA Maker erőforrással](../How-To/set-up-qnamaker-service-azure.md). Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben.

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `qna-maker-quickstart` . Ez a parancs egy egyszerű "„Helló világ!” alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```dotnetcli
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

A Program.cs tetején cserélje le az önálló using utasítást a következő sorokkal, hogy felvegye a szükséges függőségeket a projektbe:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

A program osztály tetején adja hozzá a szükséges állandókat a QnA Maker eléréséhez.

Adja meg a következő értékeket a környezeti változókban:

* `QNA_MAKER_SUBSCRIPTION_KEY` – A **kulcs** egy 32 karakterből álló karakterlánc, és a Azure Portal QnA Maker erőforrásban, a rövid útmutató lapon érhető el. Ez nem ugyanaz, mint az előrejelzési végpont kulcsa.
* `QNA_MAKER_ENDPOINT` – A **végpont** a szerzői URL-cím, a következő formátumban: `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Ez nem ugyanaz az URL-cím, amely az előrejelzési végpont lekérdezésére szolgál.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="constants":::

## <a name="add-the-kb-definition"></a>A tudásbázis-definíció hozzáadása

A konstansok után adja hozzá az alábbi tudásbázis-definíciót:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="kb":::

## <a name="add-supporting-functions-and-structures"></a>Támogató függvények és struktúrák hozzáadása
Adja hozzá a következő kódblokkot a Program osztályon belül:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="support":::

## <a name="add-a-post-request-to-create-kb"></a>POST kérés hozzáadása a tudásbázis létrehozására

A következő kód egy HTTPS-kérést küld a QnA Maker API-ra egy tudásbázis létrehozásához, és a következő választ kapja:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post_create_kb":::

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

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get_status":::

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

A következő metódus létrehozza a tudásbázist, és megismétli az állapotellenőrzést.  A _létrehozás_ **műveletazonosítót** a rendszer a POST válasz **Hely** fejlécmezőjében adja vissza, majd a GET kérésben az útvonal részeként használja. Mivel a tudásbázis létrehozása eltarthat egy ideig, előfordulhat, hogy többször is meg kell ismételnie az állapot-ellenőrző hívásokat, amíg az állapot sikeresnek vagy sikertelennek nem bizonyul. Ha a művelet sikeres, a tudásbázis azonosítóját adja vissza a **resourceLocation** változóban.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="create_kb":::

## <a name="add-the-createkb-method-to-main"></a>A CreateKB metódus hozzáadása a Main metódushoz

A Main metódus módosítása a CreateKB metódus meghívására:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="main":::

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozza létre és futtassa a programot. A parancs automatikusan egy kérést küld a QnA Maker API-ra a tudásbázis létrehozásához, majd 30 másodpercenként lekérdezi az eredményeket. Mindegyik válasz megjelenik a konzolablakban.

A tudásbázis létrehozása után a QnA Maker portálján, a [My knowledge bases](https://www.qnamaker.ai/Home/MyServices) (Saját tudásbázisok) lapon tekintheti meg azt.


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)