---
title: Csomagrögzítések kezelése – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ez az oldal bemutatja, hogyan kezelhető a Network Watcher csomagrögzítési szolgáltatása a PowerShell használatával
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
ms.openlocfilehash: 06263f85f7d6ad6cc80724baab01124833498739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129652"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Csomagrögzítések kezelése az Azure Network Watcher segítségével a PowerShell használatával

> [!div class="op_single_selector"]
> - [Azure-portál](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

A Network Watcher csomagrögzítése lehetővé teszi rögzítési munkamenetek létrehozását a virtuális gépekre és a virtuális gépekről érkező és onnan érkező forgalom nyomon követésére. A rögzítési munkamenethez szűrők vannak megadva, amelyek biztosítják, hogy csak a kívánt forgalmat rögzítse. A csomagrögzítés segít a hálózati anomáliák diagnosztizálásában mind a reaktív, mind a proaktív módon. Más felhasználások közé tartozik a hálózati statisztikák gyűjtése, a hálózati behatolásokkal kapcsolatos információk megszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és még sok más. Azáltal, hogy távolról elindíthatja a csomagrögzítéseket, ez a képesség megkönnyíti a csomagrögzítés manuális és kívánt gépen történő futtatásának terhét, ami értékes időt takarít meg.

Ez a cikk végigvezeti a csomagrögzítéshez jelenleg rendelkezésre álló különböző felügyeleti feladatokon.

- [**Csomagrögzítés indítása**](#start-a-packet-capture)
- [**Csomagrögzítés leállítása**](#stop-a-packet-capture)
- [**Csomagrögzítés törlése**](#delete-a-packet-capture)
- [**Csomagrögzítés letöltése**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy a következő erőforrásokkal rendelkezik:

* A Hálózatfigyelő egy példánya abban a régióban, ahol csomagrögzítést szeretne létrehozni

* Virtuális gép, amelyen engedélyezve van a csomagrögzítési bővítmény.

> [!IMPORTANT]
> A csomagrögzítéshez virtuálisgép-bővítmény `AzureNetworkWatcherExtension`szükséges. A bővítmény Windows virtuális gépen történő telepítéséhez látogasson el az [Azure Network Watcher Agent windowsos](../virtual-machines/windows/extensions-nwa.md) és Linuxos virtuális gépbővítménybe, és látogasson el az [Azure Network Watcher Agent linuxos virtuálisgép-bővítménybe.](../virtual-machines/linux/extensions-nwa.md)

## <a name="install-vm-extension"></a>Virtuálisgép-bővítmény telepítése

### <a name="step-1"></a>1. lépés

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>2. lépés

A következő példa a `Set-AzVMExtension` parancsmag futtatásához szükséges bővítményadatokat olvassa be. Ez a parancsmag telepíti a csomagrögzítő ügynököt a vendég virtuális gépen.

> [!NOTE]
> A `Set-AzVMExtension` parancsmag több percig is eltarthat.

Windows rendszerű virtuális gépek esetén:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Linuxos virtuális gépek esetén:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

A következő példa egy sikeres `Set-AzVMExtension` válasz a parancsmag futtatása után.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>3. lépés

Annak érdekében, hogy az `Get-AzVMExtension` ügynök telepítve van, futtassa a parancsmamot, és adja át a virtuális gép nevét és a bővítmény nevét.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

A következő minta egy példa a futó`Get-AzVMExtension`

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

Az előző lépések befejezése után a csomagrögzítő ügynök telepítve van a virtuális gépen.

### <a name="step-1"></a>1. lépés

A következő lépés a Network Watcher példány beolvasása. Ezt a változót `New-AzNetworkWatcherPacketCapture` a 4.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>2. lépés

Tárfiók beolvasása. Ez a tárfiók a csomagrögzítő fájl tárolására szolgál.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>3. lépés

A szűrők segítségével korlátozhatja a csomagrögzítés által tárolt adatokat. A következő példa két szűrőt állít be.  Az egyik szűrő csak a helyi IP 10.0.0.3-ról gyűjti a kimenő TCP-forgalmat a 20-as, 80-as és 443-as célportig.  A második szűrő csak az UDP-forgalmat gyűjti.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> A csomagrögzítéshez több szűrő is definiálható.

### <a name="step-4"></a>4. lépés

Futtassa a `New-AzNetworkWatcherPacketCapture` parancsmabot a csomagrögzítési folyamat elindításához, és adja át az előző lépésekben beolvasott szükséges értékeket.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

A következő példa a `New-AzNetworkWatcherPacketCapture` parancsmag futtatásából várt kimenet.

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

## <a name="get-a-packet-capture"></a>Csomagrögzítés beszerezni

A `Get-AzNetworkWatcherPacketCapture` parancsmag futtatása, az éppen futó vagy befejezett csomagrögzítés állapotának lekérése.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

A következő példa a `Get-AzNetworkWatcherPacketCapture` parancsmag kimenete. A következő példa a rögzítés befejezése után. A PacketCaptureStatus érték Leállítva, a TimeExceeded stopreason értékkel. Ez az érték azt mutatja, hogy a csomagrögzítés sikeres volt, és lefutotta az idejét.
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

A `Stop-AzNetworkWatcherPacketCapture` parancsmag futtatásával, ha a rögzítési munkamenet folyamatban van, le áll.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> A parancsmag nem ad vissza választ, ha egy jelenleg futó rögzítési munkameneten vagy egy már leállított munkameneten fut.

## <a name="delete-a-packet-capture"></a>Csomagrögzítés törlése

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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














