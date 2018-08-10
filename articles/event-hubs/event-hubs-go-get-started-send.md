---
title: Események küldése az Azure Event Hubs a Go használatával |} A Microsoft Docs
description: Ismerkedés az események küldése az Event Hubs Go használatával
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005597"
---
# <a name="send-events-to-event-hubs-using-go"></a>Események küldése az Event Hubs Go használatával

Az Azure Event Hubs szolgáltatás egy kiválóan méretezhető eseménykezelő rendszer, amely képes kezelni a több millió csatlakoztatott eszközök és más rendszerek által létrehozott hatalmas adatmennyiségek elemzését teszi az alkalmazások engedélyezése események száma másodpercenként. Adatoknak egy eseményközpontba való összegyűjtését, miután kap, és zpracovávat události használatával a folyamaton belüli kezelők vagy más elemzési rendszerek továbbítással.

Az Event Hubs kapcsolatos további információkért tekintse meg a [Event Hubs – áttekintés][Event Hubs overview].

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba, a Go nyelven írt alkalmazások. Események fogadása, használja a **eph lépjen** (Event Processor Host) csomag leírtak szerint [a megfelelő Receive cikk](event-hubs-go-get-started-receive-eph.md).

Jelen oktatóanyagban szereplő kód nézetéből [ezeket a GitHub-minták](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), amely a teljes megtekintéséhez tekintse meg működő alkalmazást, beleértve az importálási utasításokat és változódeklarációkat.

További példák állnak rendelkezésre [az Hubs csomag a tárház](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Nyissa meg telepített helyileg. Hajtsa végre a [ezek az utasítások](https://golang.org/doc/install) szükség esetén.
* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a virtuális gép létrehozásának megkezdése előtt.
* Egy meglévő Event Hubs névtér és az eseményközpont. Ezek az entitások utasításait követve hozhat létre [Ez a cikk](event-hubs-create.md).

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

## <a name="next-steps"></a>További lépések

Látogasson el a következő oldalakon további információt az Event Hubsról:

* [Események fogadása EventProcessorHost használatával](event-hubs-go-get-started-receive-eph.md)
* [Event Hubs – áttekintés][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
