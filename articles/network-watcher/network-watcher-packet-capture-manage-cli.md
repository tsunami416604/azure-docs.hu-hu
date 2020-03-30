---
title: Csomagrögzítések kezelése az Azure Network Watcher rel - Azure CLI | Microsoft dokumentumok
description: Ez az oldal bemutatja, hogyan kezelhető a Network Watcher csomagrögzítési szolgáltatása az Azure CLI használatával
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7a69610d1ac176354a9d7e388a12ccc7f064d848
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382715"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Csomagrögzítések kezelése az Azure Network Watcher segítségével az Azure CLI használatával

> [!div class="op_single_selector"]
> - [Azure-portál](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

A Network Watcher csomagrögzítése lehetővé teszi rögzítési munkamenetek létrehozását a virtuális gépekre és a virtuális gépekről érkező és onnan érkező forgalom nyomon követésére. A rögzítési munkamenethez szűrők vannak megadva, amelyek biztosítják, hogy csak a kívánt forgalmat rögzítse. A csomagrögzítés segít a hálózati anomáliák diagnosztizálásában mind a reaktív, mind a proaktív módon. Más felhasználások közé tartozik a hálózati statisztikák gyűjtése, a hálózati behatolásokkal kapcsolatos információk megszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és még sok más. Azáltal, hogy távolról elindíthatja a csomagrögzítéseket, ez a képesség megkönnyíti a csomagrögzítés manuális és kívánt gépen történő futtatásának terhét, ami értékes időt takarít meg.

A cikkben ismertetett lépések végrehajtásához telepítenie kell [az Azure Parancssori felületet Mac, Linux és Windows (Azure CLI) rendszerhez.](/cli/azure/install-azure-cli)

Ez a cikk végigvezeti a csomagrögzítéshez jelenleg rendelkezésre álló különböző felügyeleti feladatokon.

- [**Csomagrögzítés indítása**](#start-a-packet-capture)
- [**Csomagrögzítés leállítása**](#stop-a-packet-capture)
- [**Csomagrögzítés törlése**](#delete-a-packet-capture)
- [**Csomagrögzítés letöltése**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy a következő erőforrásokkal rendelkezik:

- A Hálózatfigyelő egy példánya abban a régióban, ahol csomagrögzítést szeretne létrehozni
- Virtuális gép, amelyen engedélyezve van a csomagrögzítési bővítmény.

> [!IMPORTANT]
> A csomagrögzítéshez ügynöknek kell futnia a virtuális gépen. Az ügynök bővítményként van telepítve. A virtuálisgép-bővítményekkel kapcsolatos utasításokért látogasson el a [Virtuálisgép-bővítmények és -szolgáltatások oldalra.](../virtual-machines/windows/extensions-features.md)

## <a name="install-vm-extension"></a>Virtuálisgép-bővítmény telepítése

### <a name="step-1"></a>1. lépés

Futtassa a `az vm extension set` parancsot a csomagrögzítő ügynök telepítéséhez a vendég virtuális gépen.

Windows rendszerű virtuális gépek esetén:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Linuxos virtuális gépek esetén:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>2. lépés

Annak érdekében, hogy az `vm extension show` ügynök telepítve van, futtassa a parancsot, és adja át az erőforráscsoport és a virtuális gép nevét. Ellenőrizze az eredményül kapott listát, és győződjön meg arról, hogy az ügynök telepítve van.

Windows rendszerű virtuális gépek esetén:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Linuxos virtuális gépek esetén:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

A következő minta egy példa a futó`az vm extension show`

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

Az előző lépések befejezése után a csomagrögzítő ügynök telepítve van a virtuális gépen.

### <a name="step-1"></a>1. lépés

Tárfiók beolvasása. Ez a tárfiók a csomagrögzítő fájl tárolására szolgál.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>2. lépés

Ezen a ponton készen áll a csomagrögzítés létrehozására.  Először vizsgáljuk meg a konfigurálni kívánt paramétereket. A szűrők az egyik ilyen paraméter, amely a csomagrögzítés által tárolt adatok korlátozására használható. A következő példa beállít egy csomagrögzítést több szűrővel.  Az első három szűrő csak a helyi IP 10.0.0.3-ról gyűjti a kimenő TCP-forgalmat a 20-as, 80-as és 443-as célportokig.  Az utolsó szűrő csak az UDP-forgalmat gyűjti.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

A következő példa a `az network watcher packet-capture create` parancs futtatásából várt kimenet.

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

## <a name="get-a-packet-capture"></a>Csomagrögzítés beszerezni

A `az network watcher packet-capture show-status` parancs futtatása, az éppen futó vagy befejezett csomagrögzítés állapotának beolvasása.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

A következő példa a `az network watcher packet-capture show-status` parancs kimenete. A következő példa, ha a rögzítés leállítva, a StopReason of TimeExceeded.

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

A `az network watcher packet-capture stop` parancs futtatásával, ha a rögzítési munkamenet folyamatban van, az le áll.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> A parancs nem ad vissza választ, ha egy jelenleg futó rögzítési munkameneten vagy egy már leállított meglévő munkameneten fut.

## <a name="delete-a-packet-capture"></a>Csomagrögzítés törlése

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> A csomagrögzítés törlése nem törli a fájlt a tárfiókban.

## <a name="download-a-packet-capture"></a>Csomagrögzítés letöltése

Miután a csomagrögzítési munkamenet befejeződött, a rögzítési fájl feltölthető a blob storage vagy a virtuális gép helyi fájljába. A csomagrögzítés tárolási helye a munkamenet létrehozásakor van definiálva. A tárfiókba mentett rögzítési fájlok elérésének kényelmes eszköze a Microsoft Azure Storage Explorer, amely itt tölthető le:https://storageexplorer.com/

Ha meg van adva tárfiók, a csomagrögzítő fájlok at a következő helyen menti a rendszer egy tárfiókba:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan automatizálhatja a csomagrögzítéseket a virtuálisgép-riasztások segítségével a [Riasztás aktivált csomagrögzítésének létrehozásával](network-watcher-alert-triggered-packet-capture.md)

Annak megállapítása, hogy bizonyos forgalom engedélyezett-e a virtuális gépbe vagy a virtuális gépből, az [IP-folyamat ellenőrzése című ellátogat.Find](diagnose-vm-network-traffic-filtering-problem.md) if certain traffic is allowed in or out in your VM by check IP flow verify

<!-- Image references -->
