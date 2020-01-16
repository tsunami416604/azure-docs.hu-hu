---
title: Statikus belső magánhálózati IP – Azure VM – klasszikus
description: A statikus belső IP-címek (DIPs) ismertetése és azok kezelése
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a13a0a54e9ded48cc5848843f4c329b2dea90f65
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975220"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Statikus belső magánhálózati IP-cím beállítása a PowerShell (klasszikus) használatával
A legtöbb esetben nem kell statikus belső IP-címet megadnia a virtuális géphez. A virtuális hálózatban lévő virtuális gépek automatikusan egy belső IP-címet kapnak egy megadott tartományból. Bizonyos esetekben azonban egy adott virtuális géphez statikus IP-címet kell megadni. Ha például a virtuális gép a DNS-t futtatja, vagy tartományvezérlőként fog futni. A statikus belső IP-címek a virtuális géppel együtt maradnak, még leállítás/megszüntetési állapotban is. 

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy a legtöbb új központi telepítés a [Resource Manager](virtual-networks-static-private-ip-arm-ps.md)-alapú üzemi modellt használja.
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>A Azure PowerShell Service Management modul telepítése

A következő parancsok futtatása előtt győződjön meg arról, hogy a [Azure PowerShell Service Management modul](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) telepítve van a számítógépen. Azure PowerShell Service Management modul verziótörténete: [PowerShell-Galéria Azure-modul](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Annak ellenőrzése, hogy elérhető-e egy adott IP-cím
Annak ellenőrzéséhez, hogy az IP- *10.0.0.7* elérhető-e egy *TestVnet*nevű vnet, futtassa a következő PowerShell-parancsot, és ellenőrizze a *IsAvailable*értékét.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Ha a fenti parancsot egy biztonságos környezetben szeretné tesztelni, kövesse a [virtuális hálózat létrehozása (klasszikus)](virtual-networks-create-vnet-classic-pportal.md) című témakör útmutatását a *TestVnet* nevű vnet létrehozásához, és győződjön meg róla, hogy a *10.0.0.0/8* címtartomány használatát használja.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Statikus belső IP-cím megadása virtuális gép létrehozásakor
Az alábbi PowerShell-szkript létrehoz egy *TestService*nevű új felhőalapú szolgáltatást, majd beolvas egy rendszerképet az Azure-ból, majd létrehoz egy *TestVM* nevű virtuális gépet az új Cloud Service-ben a beolvasott rendszerkép használatával, beállítja a virtuális gépet egy *alhálózat-1*alhálózatba, és a *10.0.0.7* statikus belső IP-ként állítja be a virtuális géphez:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Statikus belső IP-információk lekérése egy virtuális géphez
A fenti parancsfájllal létrehozott virtuális gép statikus belső IP-adatainak megtekintéséhez futtassa a következő PowerShell-parancsot, és figyelje meg az *IP*-címek értékeit:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Statikus belső IP-cím eltávolítása virtuális gépről
A fenti szkriptben a virtuális géphez hozzáadott statikus belső IP-cím eltávolításához futtassa a következő PowerShell-parancsot:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Statikus belső IP-cím hozzáadása meglévő virtuális géphez
Ha statikus belső IP-címet szeretne hozzáadni a fenti szkript használatával létrehozott virtuális géphez, futtassa a következő parancsot:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Következő lépések
[Fenntartott IP](virtual-networks-reserved-public-ip.md)

[Példány szintű nyilvános IP-cím (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Fenntartott IP REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx)

