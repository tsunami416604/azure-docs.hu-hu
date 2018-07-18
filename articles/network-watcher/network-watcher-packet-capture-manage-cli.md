---
title: Csomagrögzítés kezelése az Azure Network Watcher – Azure CLI-vel |} A Microsoft Docs
description: Jelen lap bemutatja, hogyan kezelheti a packet capture funkciójának a Network Watcher az Azure CLI használatával
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9b40a85cf3c4edd26f2fc15045f3d6862d4ac1ff
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090486"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>A csomagrögzítés kezelése az Azure Network Watcher az Azure CLI használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Az Azure REST API-val](network-watcher-packet-capture-manage-rest.md)

Network Watcher csomagrögzítés nyomon követésére, és a virtuális gépről érkező forgalom rögzítése-munkamenetek létrehozását teszi lehetővé. Szűrők annak érdekében, hogy csak a kívánt forgalmat rögzíti a rögzítési munkamenet-okat. A csomagrögzítés segítségével diagnosztizálhatja a hálózat rendellenességeket, proaktív és reaktív is. Más használati módjai többek között, hálózati statisztika, azonosítsa a hálózati behatolásokat, hibakeresése, ügyfél-kiszolgáló közötti kommunikációt, és még sok más információk összegyűjtéséhez. Képes lesz távolról indításához csomagrögzítés, ez a funkció egyszerűsíti a csomagrögzítés fut, manuálisan, a kívánt számítógépre, amelyen értékes időt takarít meg terhe.

Ez a cikk használja felületek következő generációját képviseli CLI a resource management üzemi modellhez, az Azure CLI 2.0 használatával, amely Windows, Mac és Linux rendszereken érhető el.

Ebben a cikkben szereplő lépések végrehajtásához kell [telepítse az Azure parancssori felület Mac, Linux és Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

Ez a cikk végigvezeti a különböző felügyeleti feladatok csomagrögzítés jelenleg elérhető.

- [**Csomagrögzítés indítása**](#start-a-packet-capture)
- [**Csomagrögzítés leállítása**](#stop-a-packet-capture)
- [**Csomagrögzítés törlése**](#delete-a-packet-capture)
- [**Töltse le a csomagrögzítés**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy az alábbi forrásanyagokat:

- Csomagrögzítés létrehozni kívánt példányát, Network Watcher régióban
- A csomag rögzítése a bővítmény engedélyezve van a virtuális gép.

> [!IMPORTANT]
> A csomagrögzítés kell egy ügynököt a virtuális gépen kell futnia. Az ügynök telepítve van a kiterjesztése. Virtuálisgép-bővítményekkel kapcsolatos utasításokért látogasson el a [virtuális gépi bővítmények és szolgáltatások](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Virtuálisgép-bővítmény telepítése

### <a name="step-1"></a>1. lépés

Futtassa a `az vm extension set` parancsmag a csomag rögzítési ügynök telepítéséhez a Vendég virtuális gépen.

Windows virtuális gépek:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Linux rendszerű virtuális gépek:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
````

### <a name="step-2"></a>2. lépés

Győződjön meg arról, hogy az ügynök telepítve van-e, futtassa a `vm extension show` parancsmagot, és adja át azt az erőforrás-csoport és a virtuális gép nevét. Ellenőrizze a megjelenő listában, annak érdekében, hogy az ügynök telepítve van.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Az alábbi minta egy példa a válasz futását `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Csomagrögzítés indítása

Ha az előző lépések befejeződött, a csomag rögzítési ügynök telepítve van a virtuális gépen.

### <a name="step-1"></a>1. lépés

A következő lépés, hogy a Network Watcher-példány beolvasása. A Network Watcher Helyrendszerszerepkörre nevére átadott a `az network watcher show` parancsmag a 4. lépésben.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>2. lépés

Storage-fiók beolvasása. Ez a tárfiók a packet capture fájl tárolására szolgál.

```azurecli
azure storage account list
```

### <a name="step-3"></a>3. lépés

Szűrők a csomagrögzítés által tárolt adatok korlátozására használható. Az alábbi példa a több szűrőt beállítja a csomagrögzítés.  Az első három szűrők összegyűjtéséhez kimenő TCP-forgalom csak a helyi IP-cím 10.0.0.3 célportok 20, a 80-as és 443-as porton.  Az utolsó szűrő gyűjti csak az UDP-forgalmat.

```azurecli
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Az alábbi példában a várt kimeneti futtatását a `az network watcher packet-capture create` parancsmagot.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Csomagrögzítés beolvasása

Fut a `az network watcher packet-capture show-status` parancsmag jelenleg fut, vagy befejezett csomagrögzítés állapotát kérdezi le.

```azurecli
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Az alábbi példa kimenetében a `az network watcher packet-capture show-status` parancsmagot. A következő például, amikor a rögzítés le van állítva, és a egy TimeExceeded StopReason. 

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Csomagrögzítés leállítása

Futtassa a `az network watcher packet-capture stop` parancsmagot, ha azt folyamatban a rögzítési munkamenet le van állítva.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> A parancsmag nem választ ad vissza. Ha futtatunk egy éppen futó rögzítési munkamenet vagy egy létező munkamenet már leállt.

## <a name="delete-a-packet-capture"></a>Csomagrögzítés törlése

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Csomagrögzítés törlése nem törli a storage-fiókban a fájlt.

## <a name="download-a-packet-capture"></a>Töltse le a csomagrögzítés

A csomag rögzítési munkamenet befejezése után a rögzítési feltöltés blob storage-bA vagy egy helyi fájlba a virtuális gépen. A csomagrögzítés tárolási helyét a munkamenet létrehozásakor van meghatározva. Ezek eléréséhez eszköz rögzítési-fájlokat a storage-fiók itt tölthető le a Microsoft Azure Storage Explorerben:  http://storageexplorer.com/

Ha egy storage-fiók van megadva, packet capture fájlok egy storage-fiókba, a következő helyen találhatóak meg:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan automatizálhatja a virtuális gép riasztások csomagrögzítés megtekintésével [hozzon létre egy aktivált riasztás csomagrögzítés](network-watcher-alert-triggered-packet-capture.md)

Keresse meg, ha bizonyos van engedélyezi a forgalmat a virtuális gép vagy a funkcionáló [ellenőrizze IP-folyamat ellenőrzése](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
