---
title: Csomagrögzítés kezelése az Azure Network Watcher – PowerShell |} A Microsoft Docs
description: Jelen lap bemutatja, hogyan kezelheti a PowerShell használatával a Network Watcher packet rögzítési funkciója
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 770b2e5dfef1a414da3cc8ec691c6d6ce20183fc
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102498"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>A csomagrögzítés kezelése az Azure Network Watcher PowerShell-lel

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher csomagrögzítés nyomon követésére, és a virtuális gépről érkező forgalom rögzítése-munkamenetek létrehozását teszi lehetővé. Szűrők annak érdekében, hogy csak a kívánt forgalmat rögzíti a rögzítési munkamenet-okat. A csomagrögzítés segítségével diagnosztizálhatja a hálózat rendellenességeket, proaktív és reaktív is. Más használati módjai többek között, hálózati statisztika, azonosítsa a hálózati behatolásokat, hibakeresése, ügyfél-kiszolgáló közötti kommunikációt, és még sok más információk összegyűjtéséhez. Képes lesz távolról indításához csomagrögzítés, ez a funkció egyszerűsíti a csomagrögzítés fut, manuálisan, a kívánt számítógépre, amelyen értékes időt takarít meg terhe.

Ez a cikk végigvezeti a különböző felügyeleti feladatok csomagrögzítés jelenleg elérhető.

- [**Csomagrögzítés indítása**](#start-a-packet-capture)
- [**Csomagrögzítés leállítása**](#stop-a-packet-capture)
- [**Csomagrögzítés törlése**](#delete-a-packet-capture)
- [**Töltse le a csomagrögzítés**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy az alábbi forrásanyagokat:

* Csomagrögzítés létrehozni kívánt példányát, Network Watcher régióban

* A csomag rögzítése a bővítmény engedélyezve van a virtuális gép.

> [!IMPORTANT]
> A csomagrögzítés szükséges virtuálisgép-bővítmények `AzureNetworkWatcherExtension`. A bővítmény telepítését egy Windows virtuális gépen látogasson el [Azure Network Watcher-ügynök virtuálisgép-bővítmény Windows](../virtual-machines/windows/extensions-nwa.md) és Linux rendszerű virtuális gép látogasson el a [Azure Network Watcher-ügynök virtuálisgép-bővítmény Linuxhoz](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Virtuálisgép-bővítmény telepítése

### <a name="step-1"></a>1. lépés

```powershell
$VM = Get-AzureRmVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>2. lépés

Az alábbi példa lekéri a sémakiterjesztési adatok futtatásához szükséges a `Set-AzureRmVMExtension` parancsmagot. Ez a parancsmag telepíti a csomag rögzítési ügynököt a Vendég virtuális gépen.

> [!NOTE]
> A `Set-AzureRmVMExtension` parancsmag több percet is igénybe vehet.

Windows virtuális gépek:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Linux rendszerű virtuális gépek:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Az alábbi példa a sikeres válasz futtatása után a `Set-AzureRmVMExtension` parancsmagot.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>3. lépés

Győződjön meg arról, hogy az ügynök telepítve van-e, futtassa a `Get-AzureRmVMExtension` parancsmagot, és adja át azt a virtuális gép és a bővítmény nevét.

```powershell
Get-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Az alábbi minta egy példa a válasz futását `Get-AzureRmVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Csomagrögzítés indítása

Ha az előző lépések befejeződött, a csomag rögzítési ügynök telepítve van a virtuális gépen.

### <a name="step-1"></a>1. lépés

A következő lépés, hogy a Network Watcher-példány beolvasása. Az megörökli a változót a `New-AzureRmNetworkWatcherPacketCapture` parancsmag a 4. lépésben.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>2. lépés

Storage-fiók beolvasása. Ez a tárfiók a packet capture fájl tárolására szolgál.

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>3. lépés

Szűrők a csomagrögzítés által tárolt adatok korlátozására használható. Az alábbi példa két szűrő beállítása.  Egy szűrő csak a helyi 10.0.0.3 IP-cím kimenő TCP-forgalom, 20, a 80-as és 443-as célportok gyűjti.  A második szűrőt gyűjti csak az UDP-forgalmat.

```powershell
$filter1 = New-AzureRmPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzureRmPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Csomagrögzítés több szűrőt is meghatározhatók.

### <a name="step-4"></a>4. lépés

Futtassa a `New-AzureRmNetworkWatcherPacketCapture` parancsmagot, hogy a csomag rögzítési folyamat elindításához szükséges értékeket átadása az előző lépésben lekért.
```powershell

New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Az alábbi példában a várt kimeneti futtatását a `New-AzureRmNetworkWatcherPacketCapture` parancsmagot.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Csomagrögzítés beolvasása

Fut a `Get-AzureRmNetworkWatcherPacketCapture` parancsmag jelenleg fut, vagy befejezett csomagrögzítés állapotát kérdezi le.

```powershell
Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Az alábbi példa kimenetében a `Get-AzureRmNetworkWatcherPacketCapture` parancsmagot. Az alábbi példa a rögzítés befejezése után van. A PacketCaptureStatus érték le van állítva, és a egy TimeExceeded StopReason. Ez az érték látható, hogy a csomagrögzítés sikeres volt-e, és futtatta az időt.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Csomagrögzítés leállítása

Futtassa a `Stop-AzureRmNetworkWatcherPacketCapture` parancsmagot, ha azt folyamatban a rögzítési munkamenet le van állítva.

```powershell
Stop-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> A parancsmag nem választ ad vissza. Ha futtatunk egy éppen futó rögzítési munkamenet vagy egy létező munkamenet már leállt.

## <a name="delete-a-packet-capture"></a>Csomagrögzítés törlése

```powershell
Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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

Keresse meg, ha az egyes forgalmat a virtuális gép orr engedélyezett, funkcionáló [ellenőrizze IP-folyamat ellenőrzése](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->














