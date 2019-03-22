---
title: Statikus belső magánhálózati IP - Azure-beli Virtuálisgép - klasszikus
description: Statikus belső IP-címek (DIP) és a kezelésük módjával ismertetése
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b83a6e2c81eac9993c481561e3cebbed681d2c4a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096044"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Hogyan állítható be a statikus belső magánhálózati IP-cím (klasszikus) PowerShell-lel
A legtöbb esetben nem kell a virtuális gép statikus belső IP-címet adjon meg. Egy virtuális hálózatban lévő virtuális gépek automatikusan fog kapni a belső IP-cím megadott tartományból. De bizonyos esetekben egy adott virtuális gép által használt statikus IP-cím megadása van értelme. Ha például a virtuális gép kívánja futtatni a DNS vagy a tartományvezérlő lesz. Statikus belső IP-címet a virtuális gép akár keresztül olyan leállítása vagy megszüntetési állapotban marad. 

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik:  [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a [Resource Manager üzemi modell](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Az Azure PowerShell Service Management moduljának telepítése

Futtassa a következő parancsokat, előtt győződjön meg arról, hogy a [Azure PowerShell Service Management modul](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) telepítve van a gépen. Azure PowerShell Service Management modul korábbi verzióinak, lásd: [Azure-modul a PowerShell-galériában](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Azt, hogyan ellenőrizheti egy adott IP-cím elérhető-e
Arra, ha az IP-cím *10.0.0.7* érhető el egy vnet nevű *TestVnet*, futtassa a következő PowerShell-parancsot, és ellenőrizze a értéke *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Ha szeretné tesztelni a fenti parancsot egy biztonságos környezetben irányelvekhez [hozzon létre egy virtuális hálózat (klasszikus)](virtual-networks-create-vnet-classic-pportal.md) hozhat létre egy vnetet nevű *TestVnet* és ellenőrizze, hogy használja a *10.0.0.0/8*  címtér.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Statikus belső IP-cím megadása a virtuális gép létrehozásakor
Az alábbi PowerShell-parancsfájlt hoz létre egy új felhőszolgáltatást nevű *TestService*, majd egy képet lekérő az Azure-ból, majd létrehoz egy virtuális Gépet nevű *TestVM* a beolvasott kép használatával új felhőszolgáltatás úgy állítja be a A virtuális gép egy nevű alhálózatban lehet *Subnet-1*, és beállítja a *10.0.0.7* a virtuális gép statikus belső IP-címként:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Hogyan kérheti le a statikus belső IP-információit a virtuális gép
Az a virtuális Gépet létrehozni a fenti szkript a statikus belső IP-információk megtekintéséhez futtassa a következő PowerShell-parancsot, és tekintse meg a tartozó értékeket *IP-cím*:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Virtuális gép statikus belső IP-cím eltávolítása
A statikus belső IP-cím hozzáadódik a szkriptben a virtuális gép eltávolításához futtassa a következő PowerShell-parancsot:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Egy meglévő virtuális gép statikus belső IP-cím hozzáadása
A fenti szkript használatával létrehozott virtuális géphez statikus belső IP-cím hozzáadásához futtassa a következő parancsot:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>További lépések
[Fenntartott IP-cím](virtual-networks-reserved-public-ip.md)

[Instance-Level Public IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Fenntartott IP-címet REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx)

