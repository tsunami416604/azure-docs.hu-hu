---
title: Azure rövid útmutató – Eseményközpont létrehozása a PowerShell-lel | Microsoft Docs
description: Ez a rövid útmutató azt mutatja be, hogyan hozhat létre egy eseményközpontot az Azure PowerShell-lel, majd hogyan küldhet és fogadhat eseményeket a .NET Standard SDK használatával.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b1f0c0d06f6c5f99a843a384e1dda667b967a02b
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42743907"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Rövid útmutató: Eseményközpont létrehozása a PowerShell-lel

Az Azure Event Hubs egy kiválóan méretezhető adatstreamelési platform és feldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy eseményközpontot az Azure PowerShell-lel, majd hogyan küldhet, illetve fogadhat adatokat egy eseményközpontból a .NET Standard SDK használatával.

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot][], mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](http://www.visualstudio.com/vs) vagy újabb.
- A [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 2.0-s vagy újabb verziója.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyileg használja a PowerShellt, a rövid útmutató elvégzéséhez a PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Erőforrások kiosztása

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Az eseményközpont létrehozásához szüksége lesz egy erőforráscsoportra. 

A következő példa létrehoz egy erőforráscsoportot az USA keleti régiójában. Cserélje le a `myResourceGroup` elemet a használni kívánt erőforráscsoport nevére:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az erőforráscsoport létrehozása után hozzon létre egy Event Hubs-névteret az erőforráscsoportban. Az Event Hubs-névtér egyedi, teljes tartománynevet biztosít, amelyben létrehozhatja az eseményközpontot. Cserélje le a `namespace_name` elemet a névtér egyedi nevére:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Most, hogy rendelkezik Event Hubs-névtérrel, hozzon létre egy eseményközpontot abban a névtérben:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Tárfiók létrehozása az Event Processor Host számára

Az Event Processor Host leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az ellenőrzőpontokat és a párhuzamos fogadókat. Az ellenőrzőpontok használatához az Event Processor Hostnak tárfiókra van szüksége. A tárfiók létrehozásához és a hozzá tartozó kulcsok lekéréséhez futtassa az alábbi parancsokat:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az eseményközponthoz való csatlakozáshoz és az események feldolgozásához szükség van egy kapcsolati sztringre. A kapcsolati sztring lekéréséhez futtassa a következőt:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

Most már elkezdhet streamelni az Event Hubsba. A mintákat letöltheti vagy Git-klónozhatja az [Event Hubs-adattárból](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Események betöltése

Az események streamelésének megkezdéséhez töltse le a [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) mintát a GitHubról, vagy klónozza az [Event Hubs GitHub-adattárát](https://github.com/Azure/azure-event-hubs) a következő paranccsal:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Lépjen az \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender mappába, és töltse be a SampleSender.sln fájlt a Visual Studióba.

Ezután adja hozzá a [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-csomagot a projekthez.

A Program.cs fájlban cserélje le a következő helyőrzőket az eseményközpont nevére és a kapcsolati sztringre:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Hozza létre és futtassa a mintát. Láthatja, ahogy az események betöltődnek az eseményközpontba:

![][3]

### <a name="receive-and-process-events"></a>Események fogadása és feldolgozása

Most töltse le az Event Processor Host-fogadómintát, amely fogadja az elküldött üzeneteket. Töltse le a [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) mintát a GitHubról, vagy klónozza az [Event Hubs GitHub-adattárát](https://github.com/Azure/azure-event-hubs) a következő paranccsal:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Lépjen az \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver mappába, és töltse be a SampleEphReceiver.sln megoldásfájlt a Visual Studióba.

Ezután adja hozzá a [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) és a [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) NuGet-csomagokat a projekthez.

A Program.cs fájlban cserélje le a következő állandókat a megfelelő értékekre:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Hozza létre és futtassa a mintát. Láthatja, ahogy a mintaalkalmazás fogadja az eseményeket:

![][4]

Az Azure Portalon az itt látható módon tekintheti meg az események feldolgozási sebességét egy adott Event Hubs-névtérnél:

![][5]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után törölheti az erőforráscsoportot és a benne található névteret, tárfiókot és eseményközpontot. Cserélje le a `myResourceGroup` elemet a létrehozott erőforráscsoport nevére. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Event Hubs-névteret és az eseményközpontba történő eseményküldéshez és -fogadáshoz szükséges egyéb erőforrásokat hozott létre. További információért folytassa az alábbi oktatóanyaggal:

> [!div class="nextstepaction"]
> [Event Hubs-adatstreamek adatanomáliáinak vizualizációja](event-hubs-tutorial-visualize-anomalies.md)

[hozzon létre egy ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
