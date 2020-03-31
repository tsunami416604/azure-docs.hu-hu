---
title: Az előfizetésben lévő összes virtuális gép részleteinek összegyűjtése a PowerShell segítségével
description: Az előfizetésben lévő összes virtuális gép részleteinek összegyűjtése a PowerShell segítségével
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 237081380445f2b2e4168ee3afe9a3ed7544fc89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900203"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Az előfizetésben lévő összes virtuális gép részleteinek összegyűjtése a PowerShell segítségével

Ez a parancsfájl létrehoz egy csv, amely tartalmazza a virtuális gép nevét, erőforráscsoport neve, régió, virtuális hálózat, alhálózat, privát IP-cím, operációs rendszer típusa és nyilvános IP-címe a virtuális gépek a megadott előfizetésben.

Ha nem rendelkezik [Azure-előfizetéssel](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="sample-script"></a>Példaszkript

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

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
Ez a parancsfájl a következő parancsokat használja a virtuális gépek részleteinek egy előfizetésben való létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

|Parancs|Megjegyzések|
|-|-|
|[Select-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)|Beállítja a bérlő, előfizetés és a környezet parancsmagok használata az aktuális munkamenetben.|
|[Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM)|Virtuális gép tulajdonságainak beolvasása.|
|[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzPublicIpAddress)|Nyilvános IP-címet kap.|
|[Get-AzNetwork illesztő](https://docs.microsoft.com/powershell/module/Az.Network/Get-AzNetworkInterface)|Beszerzi a hálózati interfészt.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/windows/powershell-samples?toc=/azure/virtual-machines/windows/toc.json) találhat.


