---
title: Az Azure példányszintű nyilvános IP-Címek (klasszikus) címek |} A Microsoft Docs
description: Példány szintű nyilvános IP (ILPIP)-címek és kezelheti őket a PowerShell használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: a10bf96f06c3917913c479d81e8772cb86cfe36e
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005266"
---
# <a name="instance-level-public-ip-classic-overview"></a>Példány szintű nyilvános IP-Címek (klasszikus) áttekintése
Egy példány szintű nyilvános IP (ILPIP) egy nyilvános IP-cím, amelyeket hozzárendelhet, közvetlenül a virtuális gépek vagy Cloud Services szerepkör-példány helyett a felhőszolgáltatáshoz, amely a virtuális gép vagy szerepkörpéldány példány található. Egy ILPIP nem kerül a sor, a virtuális IP-(VIP), amely a felhőszolgáltatás van rendelve. Inkább egy további IP-címet, amellyel közvetlenül csatlakozhat a virtuális gép vagy szerepkörpéldány példány.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft javasolja, hogy Resource Manager virtuális gépek létrehozása. Győződjön meg arról, hogy megismerte, hogyan [IP-címek](virtual-network-ip-addresses-overview-classic.md) munkahelyi az Azure-ban.

![ILPIP és virtuális IP-CÍMEK közötti különbség](./media/virtual-networks-instance-level-public-ip/Figure1.png)

1. ábrán látható, a felhőszolgáltatás érhető el egy virtuális IP-cím, amíg az egyes virtuális gépeket általában elért virtuális IP-cím használatával:&lt;portszám&gt;. Egy ILPIP rendel egy adott virtuális Gépre, a virtuális gép érhetők el közvetlenül az adott IP-cím használatával.

Egy felhőalapú szolgáltatás az Azure-ban való létrehozásakor megfelelő DNS-beli A rekordokat automatikusan létrehozza a férhessenek hozzá a szolgáltatás segítségével egy teljesen minősített tartománynevét (FQDN), a tényleges virtuális IP-CÍMEK használata helyett. Ugyanez a folyamat esetében egy ILPIP engedélyezi a hozzáférést a virtuális gép vagy szerepkörpéldány példányhoz a ILPIP helyett a teljes tartománynév alapján történik. Például ha nevű felhőszolgáltatás létrehozása *contosoadservice*, és konfigurál egy webes szerepkörben nevű *contosoweb* két olyan példányt, az Azure a következő regisztrálja A-rekordokat a példányok:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Virtuális gép vagy szerepkörpéldány példányonként csak egy ILPIP rendelhet hozzá. Előfizetésenként legfeljebb 5 ILPIPs is használhatja. ILPIPs több hálózati adapterrel rendelkező virtuális gépek nem támogatottak.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Miért érdemes lenne kérése egy ILPIP?
Ha meg szeretné tudni csatlakozni a virtuális gép vagy szerepkörpéldány példány közvetlenül hozzárendelt IP-címet, a felhő használata helyett szolgáltatás virtuális IP-cím:&lt;portszám&gt;, a virtuális gép vagy a szerepkörpéldány-ILPIP kérhetnek.

* **Aktív FTP** – egy ILPIP rendel egy virtuális Gépet, hogy adatforgalom az összes porton kapja. Végpontok nem szükségesek a virtuális gép forgalom fogadására.  Lásd: [az FTP protokoll áttekintése] (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) az FTP protokoll részleteiért.
* **Kimenő IP** – a virtuális gépről származó kimenő forgalom le van képezve a ILPIP, mint a forrás- és a ILPIP egyedileg azonosítja a virtuális Gépet a külső entitások.

> [!NOTE]
> Korábban egy ILPIP-cím néven ismert nyilvános IP (PIP)-címhez.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Virtuális gép egy ILPIP kezelése
A következő feladatok létrehozása, hozzárendelése és ILPIPs eltávolítása a virtuális gépek engedélyezése:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Arról, hogyan kérhet egy ILPIP során a virtuális gép létrehozása PowerShell használatával
A következő PowerShell-parancsprogram létrehozza nevű felhőszolgáltatás *FTPService*, az Azure-ból egy képet lekérő, létrehoz egy virtuális Gépet nevű *FTPInstance* a beolvasott kép használatával beállítja a virtuális gép egy ILPIP használja, és felveszi a virtuális Gépet az új szolgáltatást:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Hogyan kérhető le egy virtuális gép ILPIP-információk
Az előző szkripttel létrehozni a virtuális gép ILPIP információk megtekintéséhez futtassa a következő PowerShell-parancsot, és tekintse meg a tartozó értékeket. *PublicIPAddress* és *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Virtuális gép egy ILPIP eltávolítása
A hozzáadott a virtuális Gépet az előző szkriptben ILPIP eltávolításához futtassa a következő PowerShell-parancsot:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Egy ILPIP hozzáadása egy meglévő virtuális gép
Az előző parancsfájl használatával létrehozott virtuális géphez egy ILPIP hozzáadásához futtassa a következő parancsot:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>A Cloud Services szerepkör-példány egy ILPIP kezelése

A Cloud Services szerepkörpéldányt ad hozzá egy ILPIP, hajtsa végre az alábbi lépéseket:

1. Töltse le a .cscfg fájlban, a felhőalapú szolgáltatás által ismertetett lépéseket követve a [konfigurálása a Cloud Services hogyan](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) cikk.
2. Frissítse a .cscfg fájlban adja hozzá a `InstanceAddress` elemet. Az alábbi minta hozzáad egy nevű ILPIP *MyPublicIP* egy szerepkörpéldány nevű *WebRole1*: 

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
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Töltse fel a .cscfg fájlban, a felhőalapú szolgáltatás által ismertetett lépéseket követve a [konfigurálása a Cloud Services hogyan](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) cikk.

## <a name="next-steps"></a>További lépések
* Megismerheti hogyan [IP-címkezelés](virtual-network-ip-addresses-overview-classic.md) működik a klasszikus üzemi modellben.
* Ismerje meg [fenntartott IP-címek](virtual-networks-reserved-public-ip.md).
