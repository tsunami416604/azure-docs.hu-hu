---
title: 'Rövid útmutató: Események küldése és fogadása az Ugrás használatával – Azure Event Hubs'
description: 'Rövid útmutató: Ez a cikk egy forgatókönyvet biztosít egy Olyan Go-alkalmazás létrehozásához, amely eseményeket küld az Azure Event Hubs-ból.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720588"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Rövid útmutató: Események küldése az Eseményközpontokból az Eseményközpontokból az Ugrás funkcióval
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag bemutatja, hogyan írhat Go-alkalmazásokat események küldéséhez vagy események fogadásához egy eseményközpontból. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Menj telepített helyileg. Szükség esetén kövesse [ezeket az utasításokat.](https://golang.org/doc/install)
- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,][] mielőtt elkezdené.
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az okat a felügyeleti hitelesítő adatokat, amelyeket az alkalmazásnak az eseményközponttal való kommunikációhoz szüksége kell. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást.

## <a name="send-events"></a>Események küldése
Ez a szakasz bemutatja, hogyan hozhat létre egy Go-alkalmazást események küldéséhez egy eseményközpontba. 

### <a name="install-go-package"></a>Go csomag telepítése

A(z) On-hubok `go get` `dep`Go-csomagjának beszereznie a vagy a alkalmazással. Példa:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Csomagok importálása a kódfájlba

Az Go csomagok importálásához használja a következő kódpéldát:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy új szolgáltatásegyszerűt az [Azure CLI 2.0 használatával egyszerű Azure-szolgáltatáslétrehozása](/cli/azure/create-an-azure-service-principal-azure-cli)című útmutató utasításait követve. Mentse a megadott hitelesítő adatokat a környezetben a következő nevekkel. Az Azure SDK for Go és az Event Hubs csomagok is előre konfigurálva vannak a következő változónevek megkeresésére:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Most hozzon létre egy engedélyezési szolgáltatót az Event Hubs-ügyfélszámára, amely ezeket a hitelesítő adatokat használja:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Eseményközpontok ügyfél létrehozása

A következő kód létrehoz egy Event Hubs-ügyfelet:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Kód írása az üzenetek küldéséhez

A következő kódrészletben az (1) segítségével küldhet interaktív üzeneteket egy terminálról, vagy (2) üzeneteket küldhet a programon belül:

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Extrák

A partíciók azonosítóinak beszereznie az eseményközpontban:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Futtassa az alkalmazást az események nek az eseményközpontba küldéséhez. 

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="receive-events"></a>Események fogadása

### <a name="create-a-storage-account-and-container"></a>Tárfiók és tároló létrehozása

Állapot, például a partíciók és ellenőrzőpontok az eseménystreamben vannak megosztva a fogadók egy Azure Storage-tároló használatával. Létrehozhat egy tárfiókot és tárolót a Go SDK-val, de létrehozhat egyet az [Azure storage-fiókok – beépülő modul](../storage/common/storage-create-storage-account.md)utasításait követve.

A Storage-összetevők a Go SDK-val való létrehozásához a [Go-minták tárházában](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) és az oktatóanyagnak megfelelő mintában érhetők el.

### <a name="go-packages"></a>Go csomagok

Az üzenetek fogadásához az Event Hubs Go-csomagjait a következővel vagy `go get` `dep`a következőkkel kapja meg:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Csomagok importálása a kódfájlba

Az Go csomagok importálásához használja a következő kódpéldát:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy új szolgáltatásegyszerűt az [Azure CLI 2.0 használatával egyszerű Azure-szolgáltatáslétrehozása](/cli/azure/create-an-azure-service-principal-azure-cli)című útmutató utasításait követve. Mentse a megadott hitelesítő adatokat a környezetben a következő nevekkel: Mind az Azure SDK for Go és az Event Hubs csomag előre konfigurálva van, hogy megkeresse ezeket a változóneveket.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Ezután hozzon létre egy engedélyezési szolgáltatót az Event Hubs-ügyfélszámára, amely ezeket a hitelesítő adatokat használja:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Metaadatok beszereznie

Az Azure Go SDK használatával metaadatokat is kaphat az Azure Go SDK használatával. A későbbi műveletek ezt a struct-ot használják a megfelelő végpontok megkereséséhez.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Hitelesítő adatok segítőjnek létrehozása 

Hozzon létre egy hitelesítő adatokat segítő, amely a korábbi Azure Active Directory (AAD) hitelesítő adatok at a Storage megosztott hozzáférésű aláírás (SAS) hitelesítő adatok létrehozásához. Az utolsó paraméter arra utasítja a konstruktort, hogy ugyanazokat a környezeti változókat használja, mint korábban:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Ellenőrző mutató és lízingelő létrehozása 

Hozzon létre egy **bérlőt**, aki egy partíciót egy adott címzettnek lízingel, és egy **ellenőrző mutatót,** amely az üzenetfolyam ellenőrzőpontjainak írásáért felelős, hogy más fogadók a megfelelő eltolásból kezdhessék az olvasást.

Jelenleg egyetlen **StorageLeaserCheckpointer** érhető el, amely ugyanazt a storage-tárolót használja a bérletek és az ellenőrzőpontok kezeléséhez. A storage-fiók és a tároló nevek mellett a **StorageLeaserCheckpointer** szüksége van az előző lépésben létrehozott hitelesítő adatokra, és az Azure-környezet struct a tároló megfelelő eléréséhez.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Eseményprocesszor-gazdagép konstruktivitása

Most már a darab létrehozásához szükséges EventProcessorHost, az alábbiak szerint. Ugyanaz **a StorageLeaserCheckpointer** a rendszer lízingbe vevőként és ellenőrző mutatóként is használja, ahogy azt korábban leírtuk:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Kezelő létrehozása 

Most hozzon létre egy kezelőt, és regisztrálja azt az eseményprocesszor-állomással. Az állomás indításakor ezt és a többi megadott kezelőt alkalmazza a bejövő üzenetekre:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Kód írása az üzenetek fogadásához

Ha minden be van állítva, elindíthatja az Eseményfeldolgozó-állomást, `Start(context)` hogy az folyamatosan fusson, vagy `StartNonBlocking(context)` csak addig fusson, amíg az üzenetek rendelkezésre állnak.

Ez az oktatóanyag a következőképpen kezdődik és fut; tekintse meg a GitHub-mintát egy példa használatával: `StartNonBlocking`

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[ingyenes számla]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
