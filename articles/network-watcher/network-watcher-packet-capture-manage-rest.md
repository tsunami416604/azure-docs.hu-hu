---
title: Csomagrögzítések kezelése az Azure Network Watcher – REST API segítségével | Microsoft dokumentumok
description: Ez az oldal bemutatja, hogyan kezelhető a Network Watcher csomagrögzítési szolgáltatása az Azure REST API használatával
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 5199cf95452f93db2c2dd747fcabc67a6722d31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840893"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Csomagrögzítések kezelése az Azure Network Watcher segítségével az Azure REST API-val

> [!div class="op_single_selector"]
> - [Azure-portál](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

A Network Watcher csomagrögzítése lehetővé teszi rögzítési munkamenetek létrehozását a virtuális gépekre és a virtuális gépekről érkező és onnan érkező forgalom nyomon követésére. A rögzítési munkamenethez szűrők vannak megadva, amelyek biztosítják, hogy csak a kívánt forgalmat rögzítse. A csomagrögzítés segít a hálózati anomáliák diagnosztizálásában mind a reaktív, mind a proaktív módon. Más felhasználások közé tartozik a hálózati statisztikák gyűjtése, a hálózati behatolásokkal kapcsolatos információk megszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és még sok más. Azáltal, hogy távolról elindíthatja a csomagrögzítéseket, ez a képesség megkönnyíti a csomagrögzítés manuális és kívánt gépen történő futtatásának terhét, ami értékes időt takarít meg.

Ez a cikk végigvezeti a csomagrögzítéshez jelenleg rendelkezésre álló különböző felügyeleti feladatokon.

- [**Csomagrögzítés beszerezni**](#get-a-packet-capture)
- [**Az összes csomagrögzítés felsorolása**](#list-all-packet-captures)
- [**Csomagrögzítés állapotának lekérdezése**](#query-packet-capture-status)
- [**Csomagrögzítés indítása**](#start-packet-capture)
- [**Csomagrögzítés leállítása**](#stop-packet-capture)
- [**Csomagrögzítés törlése**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben hívja meg a Network Watcher Rest API-t az IP-folyamat ellenőrzése futtatásához. Az ARMclient a REST API-t a PowerShell használatával hívja meg. ARMClient található csokoládés [armclient a Chocolatey](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit.

> A csomagrögzítéshez virtuálisgép-bővítmény `AzureNetworkWatcherExtension`szükséges. A bővítmény Windows virtuális gépen történő telepítéséhez látogasson el az [Azure Network Watcher Agent windowsos](../virtual-machines/windows/extensions-nwa.md) és Linuxos virtuális gépbővítménybe, és látogasson el az [Azure Network Watcher Agent linuxos virtuálisgép-bővítménybe.](../virtual-machines/linux/extensions-nwa.md)

## <a name="log-in-with-armclient"></a>Bejelentkezés armclient nal

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Virtuális gép beolvasása

Futtassa a következő parancsfájlt egy virtuális gép visszaadására. Ez az információ szükséges a csomagrögzítés elindításához.

A következő kódhoz változókra van szükség:

- **subscriptionId** – Az előfizetés-azonosító a **Get-AzSubscription** parancsmaggal is lehívható.
- **resourceGroupName** - A virtuális gépeket tartalmazó erőforráscsoport neve.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A következő kimenetből a következő példában a virtuális gép azonosítóját használja.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Csomagrögzítés beszerezni

A következő példa egyetlen csomagrögzítés állapotát kapja meg

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

A következő válaszok példák egy tipikus válasz vissza, amikor a rendszer lekérdezi az állapotát a csomag rögzítése.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Az összes csomagrögzítés felsorolása

A következő példa leveszi az összes csomagrögzítési munkamenetet egy régióban.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

A következő válasz egy tipikus válasz, amely az összes csomagrögzítés beszerzésekénél jelenik meg

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Lekérdezési csomag rögzítési állapota

A következő példa leveszi az összes csomagrögzítési munkamenetet egy régióban.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

A következő válasz egy tipikus válasz, amelyet a csomagrögzítés állapotának lekérdezésekénél adnak vissza.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Csomagrögzítés indítása

A következő példa csomagrögzítést hoz létre egy virtuális gépen.  A példa paraméterezett, hogy a rugalmasság egy példa létrehozása.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Csomagrögzítés leállítása

A következő példa leállítja a csomagrögzítést egy virtuális gépen.  A példa paraméterezett, hogy a rugalmasság egy példa létrehozása.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Csomagrögzítés törlése

A következő példa törli a csomagrögzítést egy virtuális gépen.  A példa paraméterezett, hogy a rugalmasság egy példa létrehozása.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> A csomagrögzítés törlése nem törli a fájlt a tárfiókból

## <a name="next-steps"></a>További lépések

A fájlok Azure storage-fiókokból való letöltésével kapcsolatos tudnivalókat az [Azure Blob storage .NET használatával történő használatának első lépései .](../storage/blobs/storage-dotnet-how-to-use-blobs.md) Egy másik eszköz, amely használható a Storage Explorer. További információ a Storage Explorer ről itt található az alábbi linken: [Storage Explorer](https://storageexplorer.com/)

Ismerje meg, hogyan automatizálhatja a csomagrögzítéseket a virtuálisgép-riasztások segítségével a [Riasztás aktivált csomagrögzítésének létrehozásával](network-watcher-alert-triggered-packet-capture.md)













