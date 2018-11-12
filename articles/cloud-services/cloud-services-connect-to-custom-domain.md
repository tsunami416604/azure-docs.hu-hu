---
title: Cloud Service csatlakoztatása egyéni tartományvezérlőhöz |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat egy egyéni Active Directory-tartománynak, PowerShell és az Active Directory-tartománynak bővítmény használatával a webes/feldolgozói szerepkörök
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: bb812699795f112023b579352ac3a52bef311d40
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232647"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Az Azure Cloud Services-szerepkörök csatlakozik az Azure AD Domain Controller egyéni
Mi lesz először beállítása egy virtuális hálózatot (VNet) az Azure-ban. Majd hozzáadjuk egy Active Directory tartományvezérlővel (egy Azure virtuális gépen található) a virtuális hálózathoz. Ezután azt fogja hozzáadása meglévő felhőszolgáltatásokhoz tartozó szerepkörök az előre létrehozott virtuális hálózathoz, majd csatlakoztassa őket a tartományvezérlő.

Mielőtt, néhány dolgot figyelembe kell venni:

1. Ebben az oktatóanyagban a Powershellt használja, ezért ellenőrizze, hogy az Azure PowerShell telepítve, nyissa meg. Azure PowerShell-lel beállításához segítséget szeretne kérni, lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).
2. Az AD-tartományvezérlő és a webes/feldolgozói szerepkört példányok kell lennie a virtuális hálózaton.

Kövesse a cikk részletesen ismerteti, és ha problémákat tapasztal, írjon rólunk a cikk végén található megjegyzés. Valaki fog kollégáink felveszik Önnel (Igen, hogy olvasási megjegyzések).

A hálózat, a felhőalapú szolgáltatás által hivatkozott kell lennie egy **klasszikus virtuális hálózat**.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Létrehozhat egy virtuális hálózatot az Azure-ban az Azure portal vagy a PowerShell használatával. Ebben az oktatóanyagban a PowerShell használnak. Hozzon létre egy virtuális hálózatot az Azure portal használatával, lásd: [hozzon létre egy virtuális hálózatot](../virtual-network/quick-create-portal.md). A cikk ismerteti, hogy egy virtuális hálózatot (Resource Manager) hoz létre, de létre kell hoznia egy virtuális hálózat (klasszikus) felhőszolgáltatásokhoz. Ehhez a portálon, válassza ki a **erőforrás létrehozása**, típus *virtuális hálózat* a a **keresési** mezőbe, majd nyomja le az **Enter**. A keresési eredmények alapján **mindent**válassza **virtuális hálózati**. Alatt **telepítési modell kiválasztása**válassza **klasszikus**, majd **létrehozás**. Kövesse a cikkben található lépéseket.

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
Miután befejezte a virtuális hálózat létrehozása, szüksége lesz egy AD-tartományvezérlő létrehozása. A jelen oktatóanyag esetében azt szeretné állítani egy AD-tartományvezérlő egy Azure virtuális gépen.

Ehhez hozzon létre egy virtuális gépet a PowerShell használatával a következő parancsokat:

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
A virtuális gép konfigurálásához AD tartományvezérlőként, szüksége lesz jelentkezzen be a virtuális Gépre, és konfigurálja azt.

Jelentkezzen be a virtuális Gépet, akkor az RDP-fájljának beolvasása a Powershellen keresztül, használja a következő parancsokat:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Miután bejelentkezett a virtuális Gépet, a virtuális gép beállításához AD-tartományvezérlő, a következő, a részletes útmutató [hogyan állítható be az ügyfél AD-tartományvezérlő](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>A Felhőszolgáltatás a virtuális hálózat hozzáadása
Következő lépésként hozzá kell a felhőszolgáltatás üzembe helyezésének és az új vnet között. Ehhez módosítsa a cloud service cscfg vonatkozó szakaszaihoz vezetnek ad hozzá a cscfg a Visual Studio vagy a tetszőleges szövegszerkesztőben.

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

Ezután a cloud services-projekt létrehozása és üzembe helyezése az Azure. Kérjen segítséget a cloud services csomag telepítése az Azure-ba, lásd: [létrehozása és a egy felhőalapú szolgáltatás üzembe helyezése](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>A webes/feldolgozói szerepkörök a csatlakozás tartományhoz
Az Azure-ban a felhőszolgáltatás-projekt üzembe helyezése után csatlakozzon a szerepkörpéldányok az egyéni AD-tartományhoz az Active Directory-tartománynak bővítmény használatával. Adja hozzá az Active Directory-tartománynak bővítményt a meglévő cloud services üzembe helyezésével, és az egyéni tartományhoz csatlakozik, hajtsa végre az alábbi parancsokat a PowerShellben:

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

És ennyi az egész.

A cloud Services szolgáltatások az egyéni tartományvezérlőn kell csatlakoztatni. Ha további információ az Active Directory-tartománynak bővítmény konfigurálása a különböző beállításokat szeretne, használja a PowerShell segítségével. Néhány példa kövesse:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
