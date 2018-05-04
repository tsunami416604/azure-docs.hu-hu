---
title: Egyéni vezénylési állapota a tartós funkciók – Azure
description: Megtudhatja, hogyan konfigurálhatja és használhatja egyéni vezénylési állapota a tartós funkcióihoz.
services: functions
author: kadimitr
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/24/2018
ms.author: azfuncdf
ms.openlocfilehash: 840b96b9cfdb28ca1b17f54698677f4d491342c8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Egyéni vezénylési állapota a tartós függvények (az Azure Functions)

Egyéni vezénylési állapot lehetővé teszi az orchestrator-függvény egyéni állapot értékének beállítása. A HTTP-GetStatus API-n keresztül valósul meg ez az állapot vagy a `DurableOrchestrationClient.GetStatusAsync` API.

## <a name="sample-use-cases"></a>Példa használati esetek 

### <a name="visualize-progress"></a>Folyamatban lévő megjelenítése

Ügyfelek kérdezze le a állapot végpontot, és jelenítse meg a felhasználói felület, amely az aktuális végrehajtási szakasza visualizes. A következő példa bemutatja, folyamatban lévő megosztás:

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

És hogy az ügyfél majd kap a vezénylési kimenete csak akkor, ha `CustomStatus` "Londoni" mező értéke:

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

Egy másik érdekes forgatókönyv van szegmentálja a felhasználók egyéni jellemzőire vagy kapcsolati alapján testreszabott kimeneti vissza. Egyéni vezénylési állapot segítségével az ügyféloldali kódot általános marad. Az összes fő módosítások a kiszolgáló oldalán történik, az alábbi mintában látható módon:

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

### <a name="instruction-specification"></a>Utasítás meghatározása 

Az orchestrator egyedi utasításokat biztosíthat az ügyfeleknek az egyéni állapot keresztül. Az egyéni állapot utasításokat kell hozzárendelni a vezénylési kódot lépéseit:

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

A következő példában az egyéni beállítás először;

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

A vezénylési futása közben, a külső ügyfelek lehet beolvasni az egyéni állapotát:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Az ügyfelek a következő válasz jelenik meg: 

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
>  Az egyéni adattartalom korlátozódik a 16 KB-os UTF-16 JSON-szöveg, mert fel kell tudni az Azure Table Storage oszlop elfér. Ha nagyobb payload van szükségük a fejlesztők a külső tárhelyen.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a tartós funkciók HTTP API-k](durable-functions-http-api.md)


