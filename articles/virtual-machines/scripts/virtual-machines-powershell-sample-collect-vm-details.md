---
title: A PowerShell használatával egy adott előfizetés összes virtuális gép adatainak gyűjtése |} A Microsoft Docs
description: A PowerShell használatával egy adott előfizetés összes virtuális gép adatainak gyűjtése
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: ???
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: b4828b34a089a59e630aaaf7652e8623b3e2c7b8
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659709"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>A PowerShell használatával egy adott előfizetés összes virtuális gép adatainak gyűjtése

Ez a szkript létrehoz egy fürt megosztott kötetei szolgáltatás, amely tartalmazza a virtuális gép nevét, erőforráscsoport-nevet, régió, virtuális hálózat, alhálózat, magánhálózati IP-cím, operációs rendszer típusa és a megadott előfizetésben virtuális gép nyilvános IP-cím.

Ha nem rendelkezik egy [Azure-előfizetés](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free) megkezdése előtt.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="sample-script"></a>Példaszkript

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "ea7ded4e-153a-4e65-ad70-25bf9f7b91bc"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VirturalNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VirturalNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript hozzon létre egy csv-exportálás virtuális gépek részleteit egy adott előfizetés a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

|Parancs|Megjegyzések|
|-|-|
|[Select-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)|A bérlői, előfizetés és környezeti parancsmagok használatához a jelenlegi munkamenet állítja be.|
|[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM)|A virtuális gép tulajdonságainak beolvasása.|
|[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzPublicIpAddress)|Nyilvános IP-cím beolvasása.|
|[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzNetworkInterface)|Lekérdezi egy hálózati adaptert.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/windows/powershell-samples?toc=/azure/virtual-machines/windows/toc.json) találhat.


