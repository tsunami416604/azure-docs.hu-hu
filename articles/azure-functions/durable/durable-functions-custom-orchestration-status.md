---
title: Egyéni vezénylési állapot Durable Functions – Azure
description: Megtudhatja, hogyan konfigurálhatja és használhatja az egyéni vezénylési állapot Durable Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: b8017288adb75c990113b0f2ff5ba29a1f1e0a18
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642654"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) az egyéni vezénylési állapot

Egyéni vezénylési állapot lehetővé teszi az orchestrator függvény egyéni állapot értékének beállítása. A HTTP-GetStatus API-n keresztül biztosított Ez az állapot vagy a `DurableOrchestrationClient.GetStatusAsync` API-t.

> [!NOTE]
> Egyéni vezénylési állapot JavaScript egy soron következő kiadásban elérhető lesz.

## <a name="sample-use-cases"></a>Példa használati esetek 

### <a name="visualize-progress"></a>Előrehaladásának megjelenítése

Ügyfelek lekérdezi az állapot végpontját és egy folyamat, amely megjeleníti az aktuális végrehajtási fázis felhasználói felület megjelenítéséhez. A következő minta azt mutatja be, folyamat megosztása:

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  var outputs = new List<string>();

  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
  context.SetCustomStatus("Tokyo");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
  context.SetCustomStatus("Seattle");
  outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
  context.SetCustomStatus("London");

  // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
  return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
  return $"Hello {name}!";
}
```

Majd az ügyfél fog kapni a vezénylési kimenetét, és csak akkor, ha `CustomStatus` mező "London" értékre van állítva:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
  [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
  [OrchestrationClient] DurableOrchestrationClientBase starter,
  string functionName,
  ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
      await Task.Delay(200);
      durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
      Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

### <a name="output-customization"></a>Kimeneti testreszabása 

Egy másik érdekes forgatókönyv van szegmentálja a felhasználók egyedi jellemzőit és az interakciók alapján testre szabott kimeneti felismerésével. Egyéni vezénylési állapot segítségével az ügyféloldali kódot általános marad. Az összes fő módosításokat a kiszolgálói oldalon történik, az alábbi mintában látható módon:

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCity = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
} 
```

### <a name="instruction-specification"></a>Utasítás specifikáció 

Az orchestrator biztosíthat egyedi utasításokat az egyéni állapot-n keresztül az ügyfelek számára. Az egyéni utasításokat lesz rendelve a lépéseket a vezénylési kódban:

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] DurableOrchestrationContextBase context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

## <a name="sample"></a>Sample

Az alábbi minta egyéni Ez a beállítás először;

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

A vezénylési futása közben a külső ügyfelek lehet beolvasni az egyéni állapot:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Az ügyfelek a következő választ fog kapni: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  Az egyéni hasznos adat, képesnek kell lennie ahhoz, hogy elférjen az Azure Table Storage oszlop azért legfeljebb 16 KB-os UTF-16 JSON-szövegben. Ha nagyobb hasznos adat van szükségük a fejlesztők a külső tárhelyen.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Durable Functions HTTP API-k ismertetése](durable-functions-http-api.md)


