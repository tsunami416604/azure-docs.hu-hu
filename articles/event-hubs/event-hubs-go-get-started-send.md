---
title: Go - az Azure Event Hubs használatával események küldéséhez és fogadásához |} A Microsoft Docs
description: Ez a cikk egy útmutató egy Go-alkalmazást, amely az Azure Event Hubsból küldi az eseményeket létrehozásához.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682384"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Események küldése vagy események fogadása az Event Hubs Go használatával
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag leírja, hogyan írhat a Go-alkalmazásokba, eseményeket küldeni, vagy események fogadása az eseményközpontba. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Nyissa meg telepített helyileg. Hajtsa végre a [ezek az utasítások](https://golang.org/doc/install) szükség esetén.
- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a virtuális gép létrehozásának megkezdése előtt.
- **Event Hubs-névtér és eseményközpont létrehozása**. Használja a [az Azure portal](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md).

## <a name="send-events"></a>Események küldése
Ez a szakasz bemutatja, hogyan hozhat létre egy Go-alkalmazást eseményeket küld egy eseményközpontnak. 

### <a name="install-go-package"></a>Telepítse a Go-csomag

A Go-csomag beolvasása az Event hubs `go get` vagy `dep`. Példa:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>A kódfájl csomagok importálása

A Go-csomagok importálásához használja a következő mintakód:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

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

### <a name="create-event-hubs-client"></a>Az Event Hubs-ügyfél létrehozása

Az alábbi kód létrehoz egy Event Hubs-ügyfél:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Kód írása az üzenetek küldéséhez

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Kiegészítő funkciók

Az eseményközpont lépjen be a partíciók azonosítóit:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Események küldése az event hubs, az alkalmazás futtatásához. 

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="receive-events"></a>Események fogadása

### <a name="create-a-storage-account-and-container"></a>Hozzon létre egy tárfiókot és tárolót

Például a partíciók és az ellenőrzőpontok a bérletek állapot az adatfolyam között vannak megosztva fogadók egy Azure Storage-tároló használatával. Egy tárfiókot és tárolót hozhat létre a Go SDK-val, de is létrehozhat egyet a következő témakör utasításait követve [tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md).

Tároló-összetevők létrehozása a Go SDK-minták érhetők el a [Go-tárház minták](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) és a megfelelő ebben az oktatóanyagban a minta.

### <a name="go-packages"></a>Nyissa meg a csomagok

Az üzenetek fogadásához beszerzése a Go-csomagokat az Event hubs `go get` vagy `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>A kódfájl csomagok importálása

A Go-csomagok importálásához használja a következő mintakód:

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

Hozzon létre egy új egyszerű szolgáltatást a következő témakör utasításait követve [Azure-beli szolgáltatásnév létrehozása az Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). A megadott hitelesítő adatok mentéséhez a környezetben a következő nevekkel: Mindkét Azure SDK a Go és az Event Hubs-csomag fürtjének keresse meg a változók nevében.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Ezután hozzon létre egy hitelesítési szolgáltatót az Event Hubs-ügyfél, amely ezeket a hitelesítő adatokat használja:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Metaadatok struct beolvasása

Exportálva az Azure-környezet az Azure Go SDK-val kapcsolatos metaadatok lekérése. Újabb műveletek megfelelő végpontok kereséséhez használja az ebben a struktúrában le.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Hitelesítő adatok segítő létrehozása 

Hozzon létre egy hitelesítő adat segítő, amely az előző Azure Active Directory (AAD) hitelesítő adatokat használ a tároló közös hozzáférésű Jogosultságkód (SAS) hitelesítő adatok létrehozása. Az utolsó paraméter megadja a konstruktor számára ugyanazon környezeti változók használatára, mert a korábban használt:

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Hozzon létre egy ellenőrzést mutató és a egy leaser 

Hozzon létre egy **leaser**partíció egy adott fogadó bérlési felelős, és a egy **mutató ellenőrizze**, felelősek az ellenőrzőpontokat az üzenet-adatfolyamok, hogy elkezdheti a többi fogadó számára a megfelelő eltolás olvasásakor.

Jelenleg egyetlen **StorageLeaserCheckpointer** áll rendelkezésre, amely használja ugyanazt a Storage tárolót bérleteket és az ellenőrzőpontok kezelése. A tárolási fiók és a tároló nevét, valamint a **StorageLeaserCheckpointer** van szüksége a hitelesítő adatokat az előző lépést, és megfelelően elérni a tárolót az Azure-környezet struct jön létre.

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

### <a name="construct-event-processor-host"></a>Event Processor Host létrehozása

Most már a következőképpen hozhat létre az EventProcessorHost szükséges darabok. Azonos **StorageLeaserCheckpointer** leaser és ellenőrzés mutató, az előzőekben leírtaknak használja:

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

Most hozzon létre egy kezelő, és az Event Processor Host való regisztrálásához. A gazdagép indításakor vonatkozik ez, és bármely egyéb megadott kezelők bejövő üzenetek:

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

Mindent, el lehet indítani az Event Processor Host- `Start(context)` véglegesen fut, vagy azzal biztosítható, hogy `StartNonBlocking(context)` futtatásához csak is üzenetek érhetők el.

Ebben az oktatóanyagban elindítja és futtatja az alábbiak szerint: Tekintse meg a GitHub-mintát egy példa a `StartNonBlocking`:

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
- [Funkciók és az Azure Event Hubs terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
