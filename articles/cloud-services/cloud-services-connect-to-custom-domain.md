---
title: Felhőszolgáltatás csatlakoztatása egyéni tartományvezérlőhöz | Microsoft dokumentumok
description: Megtudhatja, hogy miként kapcsolhatja össze webes/feldolgozói szerepköreit egyéni AD-tartományhoz a PowerShell és az AD tartománybővítmény használatával
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: d40e392984d2675c748bda00c61cdaeb1c0932da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387020"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Az Azure Cloud Services szerepkörök csatlakoztatása az Azure-ban üzemeltetett egyéni AD-tartományvezérlőhöz
Először egy virtuális hálózatot (VNet) hozunk létre az Azure-ban. Ezután hozzáadjuk a virtuális hálózathoz egy (Egy Azure virtuális gépen üzemeltetett) Active Directory-tartományvezérlőt. Ezután hozzáadjuk a meglévő felhőszolgáltatási szerepköröket az előre létrehozott virtuális hálózathoz, majd csatlakoztatjuk őket a tartományvezérlőhöz.

Mielőtt elkezdenénk, néhány dolgot szem előtt kell tartani:

1. Ez az oktatóanyag a PowerShellt használja, ezért győződjön meg arról, hogy az Azure PowerShell telepítve van, és készen áll. Ha segítségre van szüksége az Azure PowerShell beállításához, olvassa el [az Azure PowerShell telepítése és konfigurálása című témakört.](/powershell/azure/overview)
2. Az AD-tartományvezérlőnek és a web-/feldolgozói szerepkör példányainak a virtuális hálózatban kell lenniük.

Kövesse ezt a lépésről-lépésre útmutatót, és ha bármilyen probléma merül fel, hagyjon nekünk egy megjegyzést a cikk végén. Valaki kap vissza hozzád (igen, mi olvasni megjegyzéseket).

A felhőszolgáltatás által hivatkozott hálózatnak **klasszikus virtuális hálózatnak**kell lennie.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Virtuális hálózatot hozhat létre az Azure-ban az Azure Portalon vagy a PowerShell használatával. Ebben az oktatóanyagban a PowerShell használatos. Ha virtuális hálózatot szeretne létrehozni az Azure Portal használatával, olvassa el a [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)című témakört. A cikk egy virtuális hálózat (Resource Manager) létrehozásáról rendelkezik, de létre kell hoznia egy virtuális hálózatot (Klasszikus) a felhőszolgáltatásokhoz. Ehhez a portálon válassza az **Erőforrás létrehozása**lehetőséget, írja be a *virtuális hálózat* kifejezést a **Keresőmezőbe,** majd nyomja le az **Enter billentyűt.** A keresési eredmények között a **Minden**csoportban válassza a **Virtuális hálózat**lehetőséget. A **Telepítési modell kiválasztása**csoportban válassza a **Klasszikus**lehetőséget, majd a **Létrehozás lehetőséget.** Ezután kövesse a cikkben leírt lépéseket.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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
Miután befejezte a virtuális hálózat beállítását, létre kell hoznia egy AD tartományvezérlőt. Ebben az oktatóanyagban egy AD-tartományvezérlőt fogunk létrehozni egy Azure virtuális gépen.

Ehhez hozzon létre egy virtuális gépet a PowerShellen keresztül a következő parancsokkal:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>A virtuális gép előléptetése tartományvezérlővé
A virtuális gép AD-tartományvezérlőként való konfigurálásához be kell jelentkeznie a virtuális gépre, és konfigurálnia kell azt.

A virtuális gépbe való bejelentkezéshez az RDP-fájlt a PowerShellen keresztül szerezheti be, használja a következő parancsokat:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Miután bejelentkezett a virtuális gépbe, állítsa be a virtuális gépet AD tartományvezérlőként az [ügyfél AD-tartományvezérlő beállításának](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)lépésről lépésre című útmutatójában.

## <a name="add-your-cloud-service-to-the-virtual-network"></a>A felhőszolgáltatás hozzáadása a virtuális hálózathoz
Ezután hozzá kell adnia a felhőszolgáltatás üzembe helyezését az új virtuális hálózathoz. Ehhez módosítsa a felhőszolgáltatás cscfg hozzáadásával a megfelelő szakaszokat a cscfg segítségével a Visual Studio vagy a szerkesztő az Ön által kiválasztott.

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

Ezután készítse el a felhőszolgáltatási projektet, és telepítse az Azure-ba. Ha segítségre van szüksége a felhőszolgáltatási csomag Azure-ba történő üzembe helyezéséhez, olvassa el A felhőszolgáltatás létrehozása és üzembe helyezése című [témakört.](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>A webes/feldolgozói szerepkörök csatlakoztatása a tartományhoz
Miután a felhőalapú szolgáltatásprojekt üzembe került az Azure-ban, csatlakoztassa a szerepkörpéldányokat az egyéni AD-tartományhoz az AD-tartománybővítmény használatával. Ha hozzá szeretné adni az AD-tartománybővítményt a meglévő felhőszolgáltatások üzembe helyezéséhez, és csatlakozni szeretne az egyéni tartományhoz, hajtsa végre a következő parancsokat a PowerShellben:

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

Ennyi az egész.

A felhőszolgáltatásoknak az egyéni tartományvezérlőhöz kell csatlakozniuk. Ha szeretne többet megtudni a különböző lehetőségek az AD-tartománybővítmény konfigurálásához, használja a PowerShell súgóját. Néhány példa következik:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



