---
title: Események küldése az Azure Event Hubs a Go használatával |} A Microsoft Docs
description: Ismerkedés az események küldése az Event Hubs Go használatával
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 2cc7c19d623427f7c98bbb66089b764bb067136b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52954910"
---
# <a name="send-events-to-event-hubs-using-go"></a>Események küldése az Event Hubs Go használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba, a Go nyelven írt alkalmazások. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Nyissa meg telepített helyileg. Hajtsa végre a [ezek az utasítások](https://golang.org/doc/install) szükség esetén.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md).

Most folytassa a következő lépéseket ebben az oktatóanyagban.

## <a name="install-go-package"></a>Telepítse a Go-csomag

A Go-csomag beolvasása az Event hubs `go get` vagy `dep`. Példa:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>A kódfájl csomagok importálása

A Go-csomagok importálásához használja a következő mintakód:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy új egyszerű szolgáltatást a következő témakör utasításait követve [Azure-beli szolgáltatásnév létrehozása az Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). A megadott hitelesítő adatok mentéséhez a környezetben a következő nevekkel. Keresse meg a változók nevében a Góhoz készült Azure SDK és az Event Hubs-csomagokat is fürtjének:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Most hozzon létre egy hitelesítési szolgáltatót az Event Hubs-ügyfél, amely ezeket a hitelesítő adatokat használja:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Az Event Hubs-ügyfél létrehozása

Az alábbi kód létrehoz egy Event Hubs-ügyfél:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Üzenetek küldése

Az alábbi kódrészletben használja (1) a terminálból interaktív módon üzenetküldés, illetve (2) a programon belül üzenetek küldése:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Kiegészítő funkciók

Az eseményközpont lépjen be a partíciók azonosítóit:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

Események küldése az event hubs, az alkalmazás futtatásához. 

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="next-steps"></a>További lépések
Ez a rövid útmutatóban elküldött üzeneteket az eseményközpontba a Go használatával. Események fogadása az eseményközpontok a Go használatával kapcsolatban lásd: [események fogadása az event hubs - Go](event-hubs-go-get-started-receive-eph.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
