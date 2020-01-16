---
title: Azure-példányok szintjének nyilvános IP-címei (klasszikus) | Microsoft Docs
description: A példányok nyilvános IP-címeinek (ILPIP) és a PowerShell használatával történő kezelésének ismertetése.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: 74d10c8fbe2f82d6148f5e13cb57c46dd645f76f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979541"
---
# <a name="instance-level-public-ip-classic-overview"></a>A példányok szintjének nyilvános IP-címe (klasszikus) – áttekintés
A példányok szintjének nyilvános IP-címe (ILPIP) egy nyilvános IP-cím, amelyet közvetlenül hozzárendelhet egy virtuális géphez vagy Cloud Services szerepkör-példányhoz, nem pedig ahhoz a felhőalapú szolgáltatáshoz, amelyet a virtuális gép vagy a szerepkör példánya tárol. A ILPIP nem helyezi el a felhőalapú szolgáltatáshoz hozzárendelt virtuális IP-címet (VIP). Ehelyett egy további IP-cím is használható, amellyel közvetlenül csatlakozhat a virtuális géphez vagy a szerepkör-példányhoz.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft az erőforrás-kezelőn keresztül javasolja a virtuális gépek létrehozását. Győződjön meg arról, hogy az [IP-címek](virtual-network-ip-addresses-overview-classic.md) hogyan működnek az Azure-ban.

![Különbség a ILPIP és a VIP között](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Ahogy az 1. ábrán is látható, a Cloud Service-t VIP-kapcsolaton keresztül érheti el, míg az egyes virtuális gépek általában a VIP használatával érhetők el:&lt;portszám&gt;. Egy ILPIP egy adott virtuális géphez való hozzárendelésével a virtuális gép közvetlenül az adott IP-cím használatával érhető el.

Amikor létrehoz egy felhőalapú szolgáltatást az Azure-ban, a rendszer automatikusan létrehozza a megfelelő DNS-rekordokat, hogy lehetővé tegye a hozzáférést a szolgáltatáshoz egy teljes tartománynév (FQDN) használatával, a tényleges VIP használata helyett. Ugyanez a folyamat történik egy ILPIP, amely lehetővé teszi a virtuális gép vagy szerepkör-példány hozzáférését a ILPIP helyett a teljes tartománynévvel. Ha például létrehoz egy *contosoadservice*nevű Cloud Service-t, és két példánnyal konfigurálja a *contosoweb* nevű webes szerepkört, a. Cscfg `domainNameLabel` pedig a *WebPublicIP*értékre van állítva, az Azure regisztrálja az alábbi rekordokat a példányokhoz:


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Minden egyes virtuális géphez vagy szerepkör-példányhoz csak egy ILPIP rendelhet hozzá. Előfizetésnél akár 5 ILPIPs is használhat. A ILPIPs nem támogatottak a több hálózati adapterrel rendelkező virtuális gépek esetében.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Miért igényelhetek ILPIP?
Ha szeretne csatlakozni a virtuális géphez vagy a szerepkör-példányhoz egy közvetlenül hozzárendelt IP-cím helyett, nem pedig a Cloud Service VIP:&lt;portszámot&gt;, kérjen egy ILPIP a virtuális géphez vagy a szerepkör-példányhoz.

* **Aktív FTP** – egy ILPIP egy virtuális géphez rendelésével bármilyen porton fogadhat forgalmat. Nem szükségesek végpontok ahhoz, hogy a virtuális gép fogadja a forgalmat.  Az FTP protokollról az FTP [protokoll áttekintése](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) című témakörben talál további információt.
* A virtuális gépről származó **kimenő IP** -kimenő forgalom a forrásként van leképezve a ILPIP, és a ILPIP egyedileg azonosítja a virtuális gépet külső entitásoknak.

> [!NOTE]
> A múltban egy ILPIP-címet nyilvános IP-címként (PIP) neveztek.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Virtuális gép ILPIP kezelése
A következő feladatok lehetővé teszik ILPIPs létrehozását, hozzárendelését és eltávolítását a virtuális gépekről:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>ILPIP igénylése virtuális gépek létrehozásakor a PowerShell használatával
A következő PowerShell-szkript létrehoz egy *FTPService*nevű Cloud Service-t, lekéri az Azure-ból származó rendszerképet, létrehoz egy *FTPInstance* nevű virtuális gépet a beolvasott rendszerkép használatával, beállítja a virtuális gépet egy ILPIP használatára, és hozzáadja a virtuális gépet az új szolgáltatáshoz:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Ha egy másik Storage-fiókot szeretne megadni az új virtuálisgép-lemez helyeként, használhatja a **MediaLocation** paramétert:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Virtuális gép ILPIP-adatainak beolvasása
Az előző parancsfájllal létrehozott virtuális gép ILPIP-adatainak megtekintéséhez futtassa a következő PowerShell-parancsot, és figyelje meg a *PublicIPAddress* és a *PublicIPName*értékeit:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Várt kimenet:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>ILPIP eltávolítása virtuális gépről
Az előző szkriptben a virtuális géphez hozzáadott ILPIP eltávolításához futtassa a következő PowerShell-parancsot:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>ILPIP hozzáadása meglévő virtuális géphez
Ha ILPIP szeretne hozzáadni az előző parancsfájllal létrehozott virtuális géphez, futtassa a következő parancsot:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Cloud Services szerepkör-példány ILPIP kezelése

Ha ILPIP szeretne hozzáadni egy Cloud Services szerepkör-példányhoz, hajtsa végre a következő lépéseket:

1. Töltse le a. cscfg fájlt a Cloud Service-hez a [Cloud Services konfigurálása](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) című cikkben ismertetett lépések végrehajtásával.
2. Frissítse a. cscfg fájlt úgy, hogy hozzáadja a `InstanceAddress` elemet. Az alábbi minta egy *MyPublicIP* nevű ILPIP hoz létre egy *webrole1 webes*nevű szerepkör-példányhoz: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Töltse fel a. cscfg-fájlt a Cloud Service-be a [Cloud Services konfigurálása](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) című cikkben ismertetett lépések végrehajtásával.

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>ILPIP információinak beolvasása egy felhőalapú szolgáltatáshoz
A ILPIP információinak megtekintéséhez futtassa a következő PowerShell-parancsot, és figyelje meg a *PublicIPAddress*, a *PublicIPName*, a *PublicIPDomainNameLabel* és a *PublicIPFqdns*értékeit:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Az altartomány egy rekordjának lekérdezéséhez `nslookup` is használhatja:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan működik az [IP-címzés](virtual-network-ip-addresses-overview-classic.md) a klasszikus üzemi modellben.
* További információ a [fenntartott IP](virtual-networks-reserved-public-ip.md)-címekről.
