---
title: "Csomag rögzíti az Azure hálózati figyelőt - Azure CLI 1.0 kezelése |} Microsoft Docs"
description: "Ezen a lapon ismerteti, hogyan kezelheti a csomag rögzítése funkció az Azure CLI 1.0 használatával hálózati figyelőt"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 2124dadd1fc6b5d57250c09d31155a4b421fac26
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-10"></a>Csomag rögzíti kezelése az Azure CLI 1.0 használata Azure hálózati figyelőt

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Az Azure REST API-n](network-watcher-packet-capture-manage-rest.md)

Hálózati figyelő csomagrögzítéssel rögzítési munkamenetek nyomon követéséhez forgalma és a virtuális gép létrehozását teszi lehetővé. Annak érdekében, hogy csak a kívánt forgalom rögzíti a rögzítési munkamenet szűrők célokat szolgálnak. Csomagrögzítéssel segít diagnosztizálni hálózati rendellenességeket proaktív és reaktív is. Egyéb felhasználásra tartalmazzák a hálózati statisztikákat, hálózati behatolások, ügyfél-kiszolgáló közötti kommunikációt, és még sok más hibakeresési információkat való összegyűjtéséhez. Őket távolról elindítása csomag rögzíti, ez a funkció megkönnyíti a csomagrögzítéssel fut, manuálisan, a kívánt számítógépet, amely értékes időt takaríthat meg okozta terheket.

Ebben a cikkben az Azure CLI 1.0 platformfüggetlen, elérhető a Windows, Mac és Linux.

Ez a cikk végigvezeti Önt a különböző felügyeleti feladatok, amelyek jelenleg a csomagrögzítéssel.

- [**A csomagrögzítéssel indítása**](#start-a-packet-capture)
- [**A csomagrögzítéssel leállítása**](#stop-a-packet-capture)
- [**A csomagrögzítéssel törlése**](#delete-a-packet-capture)
- [**A csomagrögzítéssel letöltése**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik-e a következőket:

- A csomagrögzítéssel létrehozni kívánt hálózati figyelőt régióban példánya
- A csomag rögzítési engedélyezett bővítményekhez virtuális gépet.

> [!IMPORTANT]
> Csomagrögzítéssel megköveteli az ügynök a virtuális gépen kell futnia. Az ügynök telepítve van a kiterjesztése. Virtuálisgép-bővítmények útmutatásra van szüksége, látogasson el [virtuálisgép-bővítmények és a szolgáltatások](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Virtuálisgép-bővítmény telepítése

### <a name="step-1"></a>1. lépés

Futtassa a `azure vm extension set` parancsmag a csomag rögzítési ügynök telepítéséhez a Vendég virtuális gépen.

A Windows virtuális gépek:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

A Linux virtuális gépek:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>2. lépés

Győződjön meg arról, hogy az ügynök telepítve van-e, futtassa a `vm extension get` parancsmagot, és adja át az erőforrás-csoport és a virtuális gép nevét. Ellenőrizze a megjelenő listában annak érdekében, hogy az ügynök telepítve van.

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

Az alábbi minta futtatását a válasz példája`azure vm extension get`

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                        Version  State
data:    ------------------------------  -----------------------     -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentWindows  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>A csomagrögzítéssel indítása

Ha az előző lépések befejeződött, a csomag rögzítési ügynök telepítve van a virtuális gépen.

### <a name="step-1"></a>1. lépés

A következő lépésre hálózati figyelőt példányának lekéréséhez. Ez a változó átadott a `network watcher show` parancsmag a 4. lépésben.

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>2. lépés

A storage-fiók beolvasása. Ez a tárfiók a csomag rögzítési fájl tárolására szolgál.

```azurecli
azure storage account list
```

### <a name="step-3"></a>3. lépés

A csomagrögzítéssel által tárolt adatok szűrők használható. Az alábbi példa állít be egy csomagrögzítéssel több szűrők.  Az első három szűrők összegyűjtéséhez kimenő TCP-forgalom csak a helyi IP 10.0.0.3 célport 20, a 80-as és a 443-as.  Az utolsó szűrő gyűjt csak az UDP-forgalmat.

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

A csomagrögzítéssel több szűrő adható meg. Ha egy összetett struktúra használ, célszerű szűrők használata a json-fájl szintaktikai hibák elkerülése érdekében. Például használja a jelzőt "-r" (ahelyett, hogy "-f"), és adja át a következő szűrőket tartalmazó json-fájl helyét:

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


A következő példa egy futtatásának várható kimenete a `network watcher packet-capture create` parancsmag.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>A csomagrögzítéssel beolvasása

Fut a `network watcher packet-capture show` parancsmag, beolvassa a jelenleg futó vagy befejezett csomagrögzítéssel állapotát.

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

A következő példa egy kimenete a `network watcher packet-capture show` parancsmag. A következő példa a rögzítés befejezése után van. A PacketCaptureStatus érték le van állítva, az egy TimeExceeded StopReason. Ezt az értéket jeleníti meg, hogy a csomagrögzítéssel sikeres volt-e, és az idő futott.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>A csomagrögzítéssel leállítása

Futtassa a `network watcher packet-capture stop` parancsmagot, ha a rögzítési munkamenet folyamatban az le van állítva.

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> A parancsmag nem választ ad vissza. Ha a jelenleg futó rögzítési munkamenet, vagy már leállt a meglévő munkamenetekben futtatott.

## <a name="delete-a-packet-capture"></a>A csomagrögzítéssel törlése

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> A csomagrögzítéssel törlése nem érinti a tárfiókban lévő fájlt.

## <a name="download-a-packet-capture"></a>A csomagrögzítéssel letöltése

A csomag rögzítési munkamenet befejezése után a rögzítési fájl is fel kell tölteni a blob storage vagy a virtuális gép helyi fájlba. A tárolási helye a csomagrögzítéssel definiálása a munkamenet létrehozását. Eszköz eléréséhez rögzítési-fájlokat egy tárfiókkal a Microsoft Azure Tártallózó, amely innen tölthető le: http://storageexplorer.com/

Ha egy tárfiókot meg van adva, csomag rögzítési fájlok kerülnek a storage-fiókok a következő helyen:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>További lépések

Csomag rögzíti a virtuális gép a riasztások megtekintésével automatizálása [riasztási kiváltott csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md)

Keresése, ha bizonyos adatforgalom engedélyezett a virtuális gép kívül vagy belül ellátogatva [ellenőrizze IP folyamat ellenőrzése](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
