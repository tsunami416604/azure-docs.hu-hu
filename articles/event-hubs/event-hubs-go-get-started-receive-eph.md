---
title: Események fogadása az Azure Event Hubs a Go használatával |} A Microsoft Docs
description: Események fogadása az Event hubs Eseményközpontokból a Go használatával – első lépések
services: event-hubs
author: joshgav
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: joshgav
ms.openlocfilehash: 14044938692e9a549faa3be127864bf1e399f858
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716885"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Események fogadása az Event Hubs Go használatával

Az Azure Event Hubs szolgáltatás egy kiválóan méretezhető eseménykezelő rendszer, amely képes kezelni a több millió csatlakoztatott eszközök és más rendszerek által létrehozott hatalmas adatmennyiségek elemzését teszi az alkalmazások engedélyezése események száma másodpercenként. Adatoknak egy eseményközpontba való összegyűjtését, miután kap, és zpracovávat události használatával a folyamaton belüli kezelők vagy más elemzési rendszerek továbbítással.

Az Event Hubs kapcsolatos további információkért tekintse meg a [Event Hubs – áttekintés][Event Hubs overview].

Ez az oktatóanyag leírja egy Go-alkalmazást az eseményközpontban lévő események fogadása. Megtudhatja, hogyan küldhet eseményeket megtekintéséhez [a megfelelő küldési cikk](event-hubs-go-get-started-send.md).

Jelen oktatóanyagban szereplő kód nézetéből [ezeket a GitHub-minták](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), amely a teljes megtekintéséhez tekintse meg utasítások és a változók deklarációja működik, beleértve az alkalmazás importálása.

További példák állnak rendelkezésre [az Hubs csomag a tárház](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz a következő előfeltételek vonatkoznak:

* Nyissa meg telepített helyileg. Hajtsa végre a [ezek az utasítások](https://golang.org/doc/install) szükség esetén.
* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a virtuális gép létrehozásának megkezdése előtt.
* Üzenetek fogadása kell üzeneteket a cél eseményközpont. Útmutató: üzenetek küldése a [oktatóanyag küldése](event-hubs-go-get-started-send.md).
* Egy meglévő eseményközponton (lásd a következő szakaszt).
* Egy meglévő tárfiókot és tárolót (lásd a következő szakasz után a szakaszt).

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ebben az oktatóanyagban egy meglévő Event Hubs névtér és az eseményközpont kezdődik. Ezek az entitások utasításait követve hozhat létre [Ez a cikk](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Hozzon létre egy tárfiókot és tárolót

Például a partíciók és az ellenőrzőpontok a bérletek állapot az adatfolyam között vannak megosztva fogadók egy Azure Storage-tároló használatával. Egy tárfiókot és tárolót hozhat létre a Go SDK-val, de is létrehozhat egyet a következő témakör utasításait követve [tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md).

Tároló-összetevők létrehozása a Go SDK-minták érhetők el a [Go-tárház minták](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) és a megfelelő ebben az oktatóanyagban a minta.

## <a name="receive-messages"></a>Üzenetek fogadása

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

## <a name="import-packages-in-your-code-file"></a>A kódfájl csomagok importálása

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

## <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy új egyszerű szolgáltatást a következő témakör utasításait követve [Azure-beli szolgáltatásnév létrehozása az Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). A megadott hitelesítő adatok mentéséhez a környezetben a következő nevekkel. Keresse meg a változók nevében a Góhoz készült Azure SDK és az Event Hubs-csomaggal is fürtjének.

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

## <a name="get-metadata-struct"></a>Metaadatok struct beolvasása

Exportálva az Azure-környezet az Azure Go SDK-val kapcsolatos metaadatok lekérése. Újabb műveletek megfelelő végpontok kereséséhez használja az ebben a struktúrában le.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Hitelesítő adatok segítő létrehozása 

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

## <a name="create-checkpointer-and-leaser"></a>Checkpointer és Leaser létrehozása 

Hozzon létre egy **Leaser**partíció egy adott fogadó bérlési felelős, és a egy **Checkpointer**, felelősek az ellenőrzőpontokat az üzenet-adatfolyamok, hogy elkezdheti a többi fogadó számára a megfelelő eltolás olvasásakor.

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

## <a name="construct-event-processor-host"></a>Event Processor Host létrehozása

Most már a következőképpen hozhat létre az EventProcessorHost szükséges darabok. Azonos **StorageLeaserCheckpointer** szolgál Leaser és Checkpointer, korábban leírtak szerint:

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

## <a name="create-handler"></a>Kezelő létrehozása 

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

## <a name="receive-messages"></a>Üzenetek fogadása

Mindent, el lehet indítani az Event Processor Host- `Start(context)` véglegesen fut, vagy azzal biztosítható, hogy `StartNonBlocking(context)` futtatásához csak is üzenetek érhetők el.

Ebben az oktatóanyagban elindítja és futtatja az alábbiak szerint: Tekintse meg a GitHub-mintát egy példa a `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Megjegyzések

Ez az oktatóprogram az **EventProcessorHost** egyetlen példányát használja. Átviteli sebesség és a megbízhatóság növelése érdekében érdemes több példányának futtatása **EventProcessorHost** különböző rendszereken. A rendszer gondoskodik arról, hogy csak egyetlen fogadó Leaser társítva van, és üzeneteket fogad, egy megadott partícióra, egy megadott időpontban.

## <a name="next-steps"></a>További lépések

Látogasson el a további információ az Event Hubs ezeket az oldalakat:

* [Események küldése az Go](event-hubs-go-get-started-send.md)
* [Event Hubs – áttekintés](event-hubs-about.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
