---
title: Az Azure Network Watcher – PowerShell-kapcsolatok hibaelhárítása |} A Microsoft Docs
description: Ismerje meg, hogyan használható a kapcsolat hibaelhárítása az Azure Network Watcher PowerShell-lel funkciója.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: aac23f616cb9d7c3fb29e516cfa2daa47c00e8e2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989260"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-powershell"></a>Kapcsolatok hibaelhárítása az Azure Network Watcher PowerShell-lel

> [!div class="op_single_selector"]
> - [Portál](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Az Azure REST API-val](network-watcher-connectivity-rest.md)

Ismerje meg, hogyan használja a kapcsolat ellenőrzése, hogy közvetlen TCP-kapcsolatot a virtuális gépről egy adott végpontot is létesíthető hibaelhárítása.

## <a name="before-you-begin"></a>Előkészületek

* A Network Watcher-kapcsolat hibaelhárítása szeretné a régióban egy példányát.
* Virtuális gépek a kapcsolatok hibaelhárítása.

> [!IMPORTANT]
> Kapcsolódási hibák elhárítása megköveteli, hogy rendelkezik-e a virtuális gép a hibaelhárítás a `AzureNetworkWatcherExtension` Virtuálisgép-bővítmény telepítve van. A bővítmény telepítését egy Windows virtuális gépen látogasson el [Azure Network Watcher-ügynök virtuálisgép-bővítmény Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és Linux rendszerű virtuális gép látogasson el a [Azure Network Watcher-ügynök virtuálisgép-bővítmény Linuxhoz](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A bővítmény nem szükséges a cél-végponton.

## <a name="check-connectivity-to-a-virtual-machine"></a>Ellenőrizze a kapcsolatot a virtuális géphez

Ebben a példában ellenőrzi a kapcsolatot a cél virtuális gép 80-as porton keresztül. Ebben a példában megköveteli, hogy Ön a Network Watcher engedélyezve van a régióban, amely tartalmazza a forrás virtuális gép.  

### <a name="example"></a>Példa

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"
$destVMName = "Database0"

$RG = Get-AzureRMResourceGroup -Name $rgName

$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName
$VM2 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $destVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location} 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationId $VM2.Id -DestinationPort 80
```

### <a name="response"></a>Válasz

A következő választ az előző példában van.  A válaszban a `ConnectionStatus` van **nem elérhető**. Láthatja, hogy minden a mintavételezők küldött sikertelen. A kapcsolat a virtuális készülék egy felhasználó által konfigurált miatt nem sikerült `NetworkSecurityRule` nevű **UserRule_Port80**, a 80-as porton bejövő forgalom blokkolására konfigurált. Ez az információ a kapcsolódási problémák kutatás használható.

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "0f1500cd-c512-4d43-b431-7267e4e67017",
                       "Address": "10.1.3.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/auNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "a88940f8-5fbe-40da-8d99-1dee89240f64"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "NetworkSecurityRule",
                           "Context": [
                             {
                               "key": "RuleName",
                               "value": "UserRule_Port80"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "VnetLocal",
                       "Id": "a88940f8-5fbe-40da-8d99-1dee89240f64",
                       "Address": "10.1.4.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/dbNic0/ipConfigurati
                   ons/ipconfig1",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="validate-routing-issues"></a>Útválasztási problémák ellenőrzése

Ebben a példában egy virtuális gép és a egy távoli végpont közötti kapcsolatot ellenőrzi. Ebben a példában megköveteli, hogy Ön a Network Watcher engedélyezve van a régióban, amely tartalmazza a forrás virtuális gép.  

### <a name="example"></a>Példa

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzureRMResourceGroup -Name $rgName
$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress 13.107.21.200 -DestinationPort 80
```

### <a name="response"></a>Válasz

A következő példában a `ConnectionStatus` jelenik meg, mint **nem elérhető**. Az a `Hops` alatt látható részletes `Issues` , amely a forgalom miatt le lett tiltva a `UserDefinedRoute`. 

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "b4f7bceb-07a3-44ca-8bae-adec6628225f",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "3fee8adf-692f-4523-b742-f6fdf6da6584"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "UserDefinedRoute",
                           "Context": [
                             {
                               "key": "RouteType",
                               "value": "User"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "Destination",
                       "Id": "3fee8adf-692f-4523-b742-f6fdf6da6584",
                       "Address": "13.107.21.200",
                       "ResourceId": "Unknown",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-website-latency"></a>Ellenőrizze a webhely késés

Az alábbi példa ellenőrzi a kapcsolatot egy webhelyre. Ebben a példában megköveteli, hogy Ön a Network Watcher engedélyezve van a régióban, amely tartalmazza a forrás virtuális gép.  

### <a name="example"></a>Példa

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzureRMResourceGroup -Name $rgName
$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName


Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress http://bing.com/
```

### <a name="response"></a>Válasz

A következő választ, láthatja a `ConnectionStatus` állapota **elérhető**. Ha a kapcsolat létrejött, késés fel van tüntetve.

```
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 7
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "1f0e3415-27b0-4bf7-a59d-3e19fb854e3e",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0",
                       "Address": "204.79.197.200",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Ellenőrizze a kapcsolatot egy storage-végponthoz

Az alábbi példa ellenőrzi a kapcsolatot a virtuális gépről blog storage-fiókba. Ebben a példában megköveteli, hogy Ön a Network Watcher engedélyezve van a régióban, amely tartalmazza a forrás virtuális gép.  

### <a name="example"></a>Példa

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzureRMResourceGroup -Name $rgName

$VM1 = Get-AzureRMVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $VM1.Location }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

Test-AzureRmNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://contosostorageexample.blob.core.windows.net/ 
```

### <a name="response"></a>Válasz

A következő json az előző parancsmag futtatását a példaválasz. A cél érhető el, mert a `ConnectionStatus` tulajdonság állapota **elérhető**.  A részleteket az ugrások számát, a storage blob és a késés eléréséhez szükséges kapcsolatban állnak rendelkezésre.

```json
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 8
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "9e7f61d9-fb45-41db-83e2-c815a919b8ed",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "1e6d4b3c-7964-4afd-b959-aaa746ee0f15"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "1e6d4b3c-7964-4afd-b959-aaa746ee0f15",
                       "Address": "13.71.200.248",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="next-steps"></a>További lépések

Határozza meg, hogy bizonyos forgalom engedélyezve van a vagy a virtuális gép funkcionáló [ellenőrizze IP-folyamat ellenőrzésével](diagnose-vm-network-traffic-filtering-problem.md).

Ha a forgalmat blokkol, és nem lehet, [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és biztonsági meghatározott szabályokat.