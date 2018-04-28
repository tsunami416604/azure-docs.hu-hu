---
title: Az Azure példányszintű nyilvános IP-cím (klasszikus) címek |} Microsoft Docs
description: Példány szintű nyilvános IP (ILPIP) címek és a kezelésük módjával PowerShell használatával.
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
ms.openlocfilehash: 4b4350e6b1616450ce45f9e947cc3b639a341ae7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="instance-level-public-ip-classic-overview"></a>Példány nyilvános IP (klasszikus) áttekintése
Egy szint nyilvános IP (ILPIP) példány nyilvános IP-címnek, amelyeket hozzárendelhet közvetlenül egy virtuális gép vagy Felhőszolgáltatás szerepkörpéldányt, nem pedig a felhőszolgáltatásba, amelyek tárolása a virtuális gép vagy szerepkör példányát. Egy ILPIP az a virtuális IP-cím (VIP) a felhőalapú szolgáltatáshoz hozzárendelt hely nem veszi. Ehelyett olyan további IP-cím segítségével csatlakozzon közvetlenül a virtuális gép vagy szerepkör-példányához.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, virtuális gépek erőforrás-kezelő létrehozása. Győződjön meg arról, hogy hogyan [IP-címek](virtual-network-ip-addresses-overview-classic.md) munkahelyi az Azure-ban.

![Különbség ILPIP és a VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Az 1. ábrán látható, a felhőalapú szolgáltatás érhető el egy VIP használatával az egyes virtuális gépek általában hozzáférnek VIP:&lt;portszámát&gt;. Egy ILPIP rendel egy adott virtuális gép virtuális gép elérése közvetlenül az adott IP-címet használja.

Amikor az Azure-ban létrehoz egy felhőalapú szolgáltatás, megfelelő DNS A-rekordokat jönnek létre automatikusan egy teljesen minősített tartománynevét (FQDN), a szolgáltatás eléréséhez a tényleges VIP használata helyett. Ugyanazt a folyamatot a egy ILPIP, amely hozzáférést biztosít a virtuális gép vagy szerepkör példány helyett a ILPIP teljes tartománynév alapján történik. Például ha nevű felhőalapú szolgáltatás létrehozása *contosoadservice*, és konfigurálja a webes szerepkör nevű *contosoweb* két példánnyal, Azure regisztrálja a következő A rekordok a példányok:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Minden virtuális gép vagy szerepkör-példány csak egy ILPIP rendelhet hozzá. Előfizetésenként legfeljebb 5 ILPIPs is használhatja. ILPIPs több hálózati Adapterrel virtuális gépek esetén nem támogatottak.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Miért kellene kérelem egy ILPIP?
Ha szeretne csatlakozni a virtuális gép vagy szerepkör példányához által közvetlenül hozzárendelt IP-címet, ahelyett, hogy az a felhőalapú szolgáltatás VIP:&lt;portszámát&gt;, egy ILPIP kérhetnek a virtuális gép vagy a szerepkör példánya.

* **Aktív FTP** -egy ILPIP rendel a virtuális gépek, megkaphatja a forgalom a portokon. Végpontok esetén nincs szükség a virtuális gép forgalom fogadására.  Lásd: (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)[az FTP protokoll áttekintése] az FTP protokoll leírását.
* **Kimenő IP** - kimenő forgalmat a virtuális gép forrásaként ILPIP van leképezve, és a ILPIP egyedileg azonosítja a virtuális Gépet, a külső részére.

> [!NOTE]
> A múltban ILPIP címnek néven ismert nyilvános IP (PIP)-cím.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>A virtuális gép egy ILPIP kezelése
A következő feladatok lehetővé teszik a létrehozása, hozzárendelése és ILPIPs eltávolítása a virtuális gépek:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Arról, hogyan kérhet egy ILPIP virtuális gép létrehozása PowerShell használatával
A következő PowerShell-parancsfájl létrehoz egy felhőalapú szolgáltatás nevű *FTPService*, lekéri a lemezkép az Azure-ból, létrehoz egy nevű virtuális gép *FTPInstance* a beolvasott kép használatával beállítja a virtuális gép egy ILPIP használatára, és hozzáadja a virtuális Gépet az új szolgáltatáshoz:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Hogyan lehet lekérni a virtuális gépek ILPIP információk
Az előző parancsfájl létrehozza a virtuális gép ILPIP információk megtekintéséhez futtassa a következő PowerShell-parancsot, és tekintse meg az értékeit *PublicIPAddress* és *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Egy virtuális Gépet egy ILPIP eltávolítása
Az előző parancsprogramban VM hozzáadott ILPIP eltávolításához futtassa a következő PowerShell-parancsot:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Egy ILPIP hozzáadása egy meglévő virtuális Gépen
Egy ILPIP hozzáadása a virtuális gép létrehozása az előző parancsfájl használatával, a következő parancsot:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>A Cloud Services szerepkör példány egy ILPIP kezelése

A Cloud Services szerepkör példánya egy ILPIP hozzáadásához kövesse az alábbi lépéseket:

1. Töltse le a .cscfg fájlban, a felhőszolgáltatás; Ehhez hajtsa végre a lépéseket a [felhőalapú szolgáltatások konfigurálása](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) cikk.
2. Frissítse a .cscfg fájlban adja hozzá a `InstanceAddress` elemet. Az alábbi minta hozzáadása egy elnevezett ILPIP *MyPublicIP* egy szerepkörpéldányt nevű *WebRole1*: 

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
3. Töltse fel a .cscfg fájlban, a felhőszolgáltatás; Ehhez hajtsa végre a lépéseket a [felhőalapú szolgáltatások konfigurálása](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) cikk.

## <a name="next-steps"></a>További lépések
* Megértéséhez hogyan [IP-címzés](virtual-network-ip-addresses-overview-classic.md) a klasszikus üzembe helyezési modellel működik.
* További tudnivalók [fenntartott IP-címek](virtual-networks-reserved-public-ip.md).
