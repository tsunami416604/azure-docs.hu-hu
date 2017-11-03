---
title: "Csatlakozás egy Felhőszolgáltatás egy egyéni tartományvezérlőhöz |} Microsoft Docs"
description: "Ismerje meg a webes vagy feldolgozói szerepköröket egy egyéni AD tartományhoz, PowerShell és az Active Directory-tartománynak bővítmény használatával"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 17f6918371678ac849198bff4e3b3eea8678c660
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure Cloud Services szerepkörök csatlakozik egy egyéni Azure-ban üzemeltetett AD tartományvezérlő
Első üzembe helyezünk egy virtuális hálózatot (VNet) az Azure-ban. Majd felveszi azt a virtuális hálózat egy Active Directory-tartományvezérlőhöz (az Azure virtuális gép üzemeltetett). Ezután azt fogja meglévő felhőszolgáltatás szerepköreit hozzá a korábban létrehozott virtuális hálózatot, majd csatlakoztassa őket a tartományvezérlő.

Mielőtt azt először néhány dolgot szem előtt tartani:

1. Ez az oktatóanyag PowerShell használja, ezért győződjön meg arról, hogy az Azure PowerShell telepítve, és készen áll. Ha segítséget szeretne kapni beállítása az Azure PowerShell, lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).
2. Az AD-tartományvezérlő és a webes vagy feldolgozói szerepkör-példányok kell lennie a Vneten belül.

Kövesse a cikk részletesen ismerteti, és ha esetleges problémákat tapasztal, hagyja, a cikk végén megjegyzést. Valaki fog visszaszerezheti (Igen, találtunk megjegyzések).

A hálózat, a felhőalapú szolgáltatás által hivatkozott kell egy **klasszikus virtuális hálózatot**.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Létrehozhat egy virtuális hálózatot az Azure-ban az Azure portálon vagy a PowerShell. Ebben az oktatóanyagban a PowerShell használjuk. Hozzon létre egy virtuális hálózatot az Azure portál használatával, lásd: [virtuális hálózat létrehozása](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A virtuális hálózat beállításának befejezése után szüksége lesz egy AD-tartományvezérlő létrehozása. Ebben az oktatóanyagban azt szeretné állítani egy AD-tartományvezérlő egy Azure virtuális gépen.

Ehhez hozzon létre egy virtuális gépet a PowerShell segítségével az alábbi parancsok használatával:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>A virtuális gép egy tartományvezérlő előléptetése
A virtuális gép konfigurálása az AD-tartományvezérlő, be kell jelentkezzen be a virtuális Gépet, majd konfigurálja.

Jelentkezzen be a virtuális Gépet, akkor az RDP-fájl beolvasása a PowerShell használatával, az alábbi parancsokat használja:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Miután bejelentkezett a virtuális gép, állítsa be a virtuális gép AD-tartományvezérlő, tekintse meg a részletes útmutatót [az ügyfél AD-tartományvezérlő beállításával](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>A felhőalapú szolgáltatás hozzáadása a virtuális hálózathoz
Ezt követően kell a felhőalapú szolgáltatás központi telepítés hozzáadása az új Vnetet. Ehhez az szükséges, módosítsa a felhőalapú szolgáltatás szolgáltatáskonfigurációs séma vonatkozó szakaszaihoz ad hozzá a szolgáltatáskonfigurációs séma, a Visual Studio vagy az Ön által választott szerkesztőben.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Ezután hozza létre a felhőalapú szolgáltatások projektet, és telepítheti az Azure. Ha segítséget szeretne kapni a cloud services csomag telepítése az Azure-ba, tekintse meg a [létrehozásáról és központi telepítése egy felhőalapú szolgáltatás](cloud-services-how-to-create-deploy.md#how-to-deploy-a-cloud-service)

## <a name="connect-your-webworker-roles-to-the-domain"></a>A webes vagy feldolgozói szerepköröket csatlakozni a tartományhoz
Miután a felhőszolgáltatás-projekt Azure van telepítve, csatlakoztassa a szerepkörpéldányok az egyéni AD-tartomány, az Active Directory-tartománynak kiterjesztés használatával. Az Active Directory-tartománynak-bővítmény hozzáadása a meglévő felhőalapú szolgáltatások telepítéséhez, és az egyéni tartományhoz, a PowerShell hajtható végre a következő parancsokat:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

És azt.

A felhőszolgáltatások egyéni tartományvezérlő kell csatlakoztatni. Ha azt szeretné, további információt az Active Directory-tartománynak bővítmény konfigurálásáról a különböző beállítások, a PowerShell súgójának használata. Néhány példa kövesse:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
