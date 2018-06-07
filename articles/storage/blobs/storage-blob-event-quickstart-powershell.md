---
title: Egyéni webkiszolgáló-végpont - Powershell Azure Blob storage-események átirányíthatja |} Microsoft Docs
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 05/24/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: b6764ffa0e7cfbc888f11c22af855d48d8160372
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650502"
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Útvonal Blob storage-események egy egyéni webkiszolgáló-végponthoz, a PowerShell használatával

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben előfizetni a Blob storage-események, eseményindító egy eseményt, az Azure PowerShell használatával, és az eredmény. 

Általában olyan végponttal, amely az eseményadatok feldolgozza, és műveleteket hajtja végre küldött események. Ez a cikk leegyszerűsítése az események küldése egy webalkalmazásba, amely összegyűjti és az üzeneteket jelenít meg.

Ha végzett, megjelenik az, hogy az eseményadat a webes alkalmazás el lett küldve.

![Eredmények megtekintése](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>Beállítás

Ehhez a cikkhez az Azure PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és a hitelesítéshez kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzureRmAccount
```

> [!NOTE]
> Rendelkezésre állás a Storage-események kötődik esemény rács [rendelkezésre állási](../../event-grid/overview.md) és más régiókban is elérhető lesz a esemény rács hasonlóan.

Ez a példa **westus2** , és tárolja a kijelölt változó használható.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal.

A következő példában létrehozunk egy **gridResourceGroup** nevű erőforráscsoportot a **westus2** helyen.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Create a storage account

A Blob storage-események használatához szüksége vagy egy [Blob storage-fiók](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) vagy egy [általános célú v2 tárfiókkal](../common/storage-account-options.md#general-purpose-v2). **Általános célú v2 (GPv2)** minden funkció támogatása az összes tárolószolgáltatásokra, köztük a blobokat, fájlok, üzenetsorok és táblák storage-fiókok vannak. A **Blob storage-fiók** egy speciális tárfiók a strukturálatlan adatok blobként (objektumokként) az Azure Storage való tárolására. BLOB storage-fiókok hasonló általános célú tárfiókok, és minden nagy tartósságot, rendelkezésre állási, méretezhetőség és teljesítmény szolgáltatás használata ma beleértve a 100 %-os API-konzisztenciát a blokkblobokhoz, és a hozzáfűző blobokhoz. A csak blokkok és hozzáfűző blobok tárolását igénylő alkalmazásokhoz javasoljuk a Blob Storage-fiókok használatát.  

A Blob storage-fiók létrehozása az LRS-replikáció használatával [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), majd lekérheti a tárfiók környezetét, amely definiálja a tárfiókot használni. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat. Ez a példa nevű tárfiók létrehozása **gridstorage** a helyileg redundáns tárolás (LRS). 

> [!NOTE]
> Tárfiókneveket globális névteret szerepelnek, ezért meg kell néhány véletlenszerű karakter hozzáfűzése a parancsfájl a megadott névvel.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. Általában a végpont eseményadatok alapján műveleteket hajtja végre. A gyors üzembe helyezés leegyszerűsítése telepít egy [előre elkészített webalkalmazás](https://github.com/dbarkol/azure-event-grid-viewer) , amely az esemény üzeneteket jelenít meg. A telepített megoldás tartalmaz az App Service-csomag, az App Service web app és forráskód a Githubról.

Cserélje le `<your-site-name>` a webalkalmazás egyedi névvel. A webalkalmazás nevének egyedinek kell lennie, mert része a DNS-bejegyzés.

```powershell
$sitename="<your-site-name>"

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/dbarkol/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

A telepítés néhány percet is igénybe vehet. A telepítés sikeres befejezése, után megtekintheti a győződjön meg arról, hogy a webalkalmazás fut-e. A webböngésző keresse meg: `https://<your-site-name>.azurewebsites.net`

Meg kell jelennie a hely nem aktuálisan megjelenő üzeneteket.

## <a name="subscribe-to-your-storage-account"></a>A storage-fiók előfizetés

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. A következő példa a tárfiókot hozott létre, és adja át az URL-címet a webes alkalmazás és a végpontnak a eseményértesítés számítógépcsoportra fizetett elő. A webalkalmazás a végpontot tartalmaznia kell a utótag `/api/updates/`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

A webalkalmazás ismét megtekintheti, és figyelje meg, hogy egy előfizetés érvényesítési esemény el lett küldve azt. Válassza ki a szem ikonra, bontsa ki az eseményadatok. Esemény rács küld az érvényesítési eseményt, így a végpont ellenőrizheti, hogy azt kéri a eseményadatok. A webalkalmazás az előfizetés érvényesítése kódot tartalmazza.

![Előfizetés eseményének megtekintése](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Esemény kiváltása a Blob Storage-ból

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először hozzon létre egy tárolót és egy objektumot. Ezt követően most töltse fel az objektum a tárolóba.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Tekintse meg az imént a telefonjára küldött esemény webalkalmazás megtekintése.

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
Ha azt tervezi, hogy folytatja a tárfiók és az esemény előfizetésének használatát, akkor ne törölje a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthatnak segítséget a Blob Storage-események és az Event Grid:

- [Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)
- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
