---
title: Statikus belső privát IP - Azure VM – klasszikus
description: Statikus belső IP-címek (immerzióban) és a kezelésük módjával ismertetése
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.openlocfilehash: b668a06b91a5fcb1dd08737e0422b599bdb3e27f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Hogyan kell beállítani egy statikus belső magánhálózati IP-cím (klasszikus) PowerShell használatával
A legtöbb esetben nem kell a virtuális gép statikus belső IP-címet megadni. A virtuális hálózat virtuális gépek automatikusan fog kapni a belső IP-címnek megadott tartomány. Azonban bizonyos esetekben egy statikus IP-címet ad meg egy adott virtuális gép teljesen logikus. Ha például a virtuális Gépet DNS futtatni fogja vagy egy tartományvezérlő. Egy statikus belső IP-címet a virtuális gép akár keresztül stop/deprovision állapotban marad. 

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a [Resource Manager üzembe helyezési modellben](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Ha rendelkezésére áll-e egy adott IP-cím ellenőrzése
Ha ellenőrizni az IP-cím *10.0.0.7* érhető el egy nevű vnetet *TestVnet*, futtassa a következő PowerShell-parancsot, és ellenőrizze a következő *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Ha a fenti parancs teszteléséhez biztonságos környezetben útmutatását [hozzon létre egy virtuális hálózatot (klasszikus)](virtual-networks-create-vnet-classic-pportal.md) hozhat létre egy vnetet nevű *TestVnet* , és győződjön meg arról, használja a *10.0.0.0/8*  címterének.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Egy statikus belső IP-cím megadása a virtuális gép létrehozásakor
Az alábbi PowerShell-parancsfájlt hoz létre egy új felhőalapú szolgáltatás nevű *TestService*, majd lemezkép lekéri az Azure-ból, majd létrehoz egy nevű virtuális gép *TestVM* a beolvasott kép használatával új felhőalapú szolgáltatás, úgy állítja be a Nevű alhálózat virtuális Gépet *alhálózat-1*, és beállítja a *10.0.0.7* , egy statikus belső IP-cím a virtuális gép számára:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Hogyan lehet lekérni a virtuális gép statikus belső IP-információit
A fenti parancsfájl létrehozza a virtuális gép statikus belső IP-információit megtekintéséhez futtassa a következő PowerShell-parancsot, és tekintse meg az értékeit *IP-cím*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Egy statikus belső IP-cím eltávolítása a virtuális gépek
A statikus belső IP-címet a fenti parancsprogramban VM hozzáadott eltávolításához futtassa a következő PowerShell-parancsot:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Egy statikus belső IP-cím hozzáadása egy meglévő virtuális Gépen
Hozzáadása egy belső statikus IP-cím kötése a virtuális gép létre a parancsfájl a fent runt a következő parancsot:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>További lépések
[Fenntartott IP-cím](virtual-networks-reserved-public-ip.md)

[Példányszintű nyilvános IP-cím (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Fenntartott IP-cím REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx)

