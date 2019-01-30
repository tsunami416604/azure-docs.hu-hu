---
title: Webes végpont - Powershell az Azure Blob storage-események küldése |} A Microsoft Docs
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
services: storage,event-grid
author: david-stanford
ms.author: dastanfo
ms.date: 08/23/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.custom: seodec18
ms.openlocfilehash: 3e1a482cc59d951fbe6b25fcf0bfc4a967222839
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246415"
---
# <a name="quickstart-route-storage-events-to-web-endpoint-with-powershell"></a>Gyors útmutató: Storage-események átirányítása webes végpontra a PowerShell-lel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben az Azure PowerShell használatával feliratkozás Blob storage-események, az eseményindító egy eseményt, és az eredmény megtekintéséhez. 

Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan webalkalmazásnak küldjük el, amely az üzenetek gyűjtésével és megjelenítésével foglalkozik.

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat a webalkalmazásnak.

![Eredmények megtekintése](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Beállítás

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk megköveteli, hogy az Azure PowerShell legújabb verzióját futtatja. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat hitelesítéséhez.

```powershell
Connect-AzAccount
```

Ez a példa **westus2** és a kijelölés tárolja egy változóban használható.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot.

A következő példában létrehozunk egy **gridResourceGroup** nevű erőforráscsoportot a **westus2** helyen.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Blobtároló események általános célú v2 tárfiókokban vagy Blob tárfiókokban érhetők el. Az **általános célú v2** fiókok olyan tárfiókok, amelyek a társzolgáltatások összes funkcióját támogatják, beleértve a blobokat, a fájlokat, az üzenetsorokat és a táblákat is. A **Blob Storage-fiók** egy speciális tárfiók a strukturálatlan adatok blobként (objektumként) való tárolására az Azure Storage-ban. A Blob Storage-fiókok olyanok, mint a meglévő általános célú tárfiókjai, és a jelenlegi rendszereivel megegyező szintű tartósságot, rendelkezésre állást, méretezhetőséget és teljesítményt nyújtanak, beleértve a 100%-os API-konzisztenciát a blokkblobokhoz és a hozzáfűző blobokhoz. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.

Az LRS replikációt a Blob storage-fiók létrehozása [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount), majd lekérheti a tárfiók környezetét, amely meghatározza a használandó tárfiókot. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat. Ez a példa létrehoz egy nevű tárfiókot **gridstorage** a helyileg redundáns tárolás (LRS). 

> [!NOTE]
> Tárfiókok nevének találhatók globális névtér úgy kell hozzáfűznie valamilyen véletlenszerű karakter, ez a szkript megadott névvel.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A `<your-site-name>` elemet a webalkalmazás egyedi nevére cserélje le. A webalkalmazás nevének egyedinek kell lennie, mert a DNS-bejegyzés része.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A helynek megjelenített üzenetek nélkül kell megjelennie.

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>Előfizetés a tárfiókra

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. Az alábbi példa bemutatja a létrehozott tárfiókra való feliratkozást, és átadja a webalkalmazásából származó URL-címet az eseményértesítés végpontjaként. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

![Előfizetési esemény megtekintése](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Esemény kiváltása a Blob Storage-ból

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először is hozzunk létre egy tárolót, és a egy objektumot. Ezután töltse fel az objektum a tárolóba.

```powershell
$containerName = "gridcontainer"
New-AzStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tekintse meg a webalkalmazást az imént elküldött esemény megtekintéséhez.

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy folytatja a storage és az esemény előfizetésének, ne törölje az erőforrásokat létrehozott ebben a cikkben. Ha nem szeretné folytatni, a következő paranccsal törölheti a ebben a cikkben létrehozott erőforrásokat.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthatnak segítséget a Blob Storage-események és az Event Grid:

- [Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)
- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
