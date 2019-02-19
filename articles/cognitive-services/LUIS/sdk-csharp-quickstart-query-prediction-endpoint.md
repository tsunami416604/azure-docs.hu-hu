---
title: 'Gyors útmutató: C#SDK-t lekérdezés előrejelzési végpont'
titleSuffix: Azure Cognitive Services
description: Használja a C# előrejelzési egy felhasználó utterance (kifejezés) küldhet a LUIS és SDK-val.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: diberry
ms.openlocfilehash: e009ef159a51a22c56409cd1564fa9fde3c67b55
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405560"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Gyors útmutató: Előrejelzési koncového bodu lekérdezése C# .NET SDK-val

A .NET SDK használata, található [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), hogy küldjön egy felhasználó utterance (kifejezés) a Language Understanding (LUIS) és a felhasználó szándékának előrejelzése fogadjon. 

Ez a rövid útmutató küld egy felhasználó utterance (kifejezés), mint például `turn on the bedroom light`, egy nyilvános Language Understanding alkalmazáshoz, majd fogadja az előrejelzés, és megjeleníti a felső pontozási célt `HomeAutomation.TurnOn` és az entitás `HomeAutomation.Room` az utterance (kifejezés) található. 

## <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio Community 2017-es kiadása](https://visualstudio.microsoft.com/vs/community/)
* C# programozási nyelv (a VS Community 2017 része)
* A df67dcdb-c37d-46af-88e1-8b97951ca1c2 azonosítójú nyilvános alkalmazás

> [!Note]
> A teljes megoldás érhető el a [cognitive-szolgáltatások – nyelvi értelmezésre](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) GitHub-adattárban.

További dokumentáció keres?

 * [SDK-forrásdokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Cognitive Services és a Language Understanding kulcs lekérése

A nyilvános alkalmazás otthoni Automation használatához egy érvényes kulcs végpont ismeretekkel kell. Is használhatja, vagy a Cognitive Services kulcs (hozza létre alább az Azure CLI-vel), amely a cognitive services számos érvényes, vagy egy `Language Understanding` kulcsot. 

Használja a következő [kulcs Cognitive Services-szolgáltatás létrehozásához Azure CLI-paranccsal](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>.NET Core-projekt létrehozása

Hozzon létre egy .NET Core console projektet a Visual Studio Community 2017.

1. Open Visual Studio Community 2017.
1. Hozzon létre egy új projektet a **Visual C#**  válassza **Console App (.NET Core)**.
1. Adja meg a projekt nevét `QueryPrediction`, hagyja a többi alapértelmezett értéket, és válassza ki **OK**.
    Ez létrehoz egy egyszerű projekt nevű fájl elsődleges kód **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Adja hozzá a NuGet-SDK

1. Az a **Megoldáskezelőben**, válassza ki a projektet a faszerkezetes nézetben nevű **QueryPrediction**, kattintson a jobb gombbal. Válassza a menü **NuGet-csomagok kezelése...** .
1. Válassza ki **Tallózás** verziójúra `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Ha a csomag információkat jelenít meg, válassza ki a **telepítése** a csomag telepítéséhez a projektbe a Finderből. 
1. Adja hozzá a következő _használatával_ tetején utasításokkal **Program.cs**. Nem távolítja el a meglévő _használatával_ nyilatkozata `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Hozzon létre egy új módszer az előrejelzés

Hozzon létre egy új módszer `GetPrediction` kérés küldése a lekérdezés előrejelzési végpontnak. A metódus a későbbiekben létrehozása és konfigurálása szükséges minden objektumot ezután adja vissza egy `Task` együtt a [ `LuisResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) előrejelzési eredményeket. 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Hitelesítő objektum létrehozása

Adja hozzá a következő kódot a `GetPrediction` metódus az ügyfél hitelesítő adatok létrehozása a Cognitive Services-szolgáltatás a kulccsal.

Cserélje le `<REPLACE-WITH-YOUR-KEY>` -régióhoz a Cognitive Services-szolgáltatás kulcs. A kulcs a következő helyen a [az Azure portal](https://portal.azure.com) kulcsok oldalán az adott erőforráshoz.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Language Understanding ügyfél létrehozása

Az a `GetPrediction` metódus az előző kód után adja a következő kódot az új hitelesítő adatok létrehozása egy [ `LUISRuntimeClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) objektumot. 

Cserélje le `<REPLACE-WITH-YOUR-KEY-REGION>` -régióhoz a kulcsot, például `westus`. A kulcs régióban van a [az Azure portal](https://portal.azure.com) Áttekintés lapján az adott erőforráshoz.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Lekérdezési paraméterek beállítása

Az a `GetPrediction` metódus az előző kód után adja a következő kódot a lekérdezési paraméterek beállítása.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Előrejelzési végpont lekérdezése

Az a `GetPrediction` metódus az előző kód után adja a következő kódot a lekérdezési paraméterek beállítása:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Előrejelzési eredmények megjelenítése

Módosítsa a **fő** metódus hívása az új `GetPrediction` metódust, és lépjen vissza az előrejelzési eredmények:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>A projekt futtatása

A Studio a projekt buildjének elkészítéséhez, és a lekérdezés kimenetének futtassa a projektet lásd:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [.NET SDK-val](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) és a [.NET dokumentációjában](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Oktatóanyag: Felhasználói céljaira meghatározni a LUIS-alkalmazás létrehozása](luis-quickstart-intents-only.md) 