---
title: "Az Azure fenntartott IP-címek (klasszikus) - PowerShell kezelése |} Microsoft Docs"
description: "Fenntartott IP-címek (klasszikus) és a kezelésük módjával PowerShell használatával."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 5e9c83cebec96c6bc8afd53b0c637d7af899746f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="reserved-ip-addresses-classic"></a>Fenntartott IP-címek (klasszikus)

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [Sablon](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

IP-címek az Azure-ban két kategóriába sorolhatók: dinamikus és fenntartott. Azure által kezelt nyilvános IP-címek alapértelmezés szerint dinamikus. Ez azt jelenti, hogy az IP-címet használja, egy adott felhőalapú szolgáltatás (VIP) vagy a virtuális gépek eléréséhez, vagy közvetlenül szerepkörpéldányt (ILPIP) időről időre, ha az módosíthatja erőforrások állítsa le vagy leállt (felszabadított).

IP-címek módosítása érdekében foglalhat IP-címet. Fenntartott IP-címek csak virtuális IP-címhez, győződjön meg arról, hogy a felhőalapú szolgáltatás IP-címe ugyanaz marad, akár erőforrások állítsa le vagy leállt (felszabadított) használható. Továbbá meglévő dinamikus IP-címek fenntartott IP-címekre VIP használt válthat.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Megtudhatja, hogyan lefoglalni egy statikus nyilvános IP cím használatával a [Resource Manager üzembe helyezési modellben](virtual-network-ip-addresses-overview-arm.md).

IP-címek az Azure-ban kapcsolatos további tudnivalókért olvassa el a [IP-címek](virtual-network-ip-addresses-overview-classic.md) cikk.

## <a name="when-do-i-need-a-reserved-ip"></a>Mikor kell a foglalt IP-cím?
* **Ahhoz, hogy az IP-cím az előfizetésében foglalt**. Ha semmilyen körülmények között az előfizetésből nem kiadott IP-címet lefoglalni kívánt, foglalt nyilvános IP-címhez kell használnia.  
* **Azt szeretné, hogy az IP-marad a felhőalapú szolgáltatással, még akkor is keresztben leállt, vagy állapota (VM) felszabadítása**. Ha azt szeretné, hogy a szolgáltatás elérése IP-cím használatával, amely nem változik, még ha a felhőszolgáltatásban található virtuális gépek a állnak le, vagy állítsa le a (felszabadított).
* **Győződjön meg arról, hogy az Azure-ból kimenő forgalom használ egy előre jelezhető IP-címet szeretne**. Előfordulhat, hogy a helyi tűzfal konfigurálva ahhoz, hogy csak adott IP-címekről érkező forgalmat. Olyan IP-címet lefoglalásával tudja a forrás IP-címet, és nem szükséges frissíteni a tűzfalszabályok egy IP-módosítása miatt.

## <a name="faq"></a>GYIK
1. Használható az összes Azure-szolgáltatások egy fenntartott IP-cím? <br>
    Nem. Fenntartott IP-címek csak a virtuális gépek és virtuális IP-címhez keresztül közzétett felhő példány szerepköreit használható.
2. Hány foglalt IP-cím is van? <br>
    További információkért lásd: a [Azure korlátozza](../azure-subscription-service-limits.md#networking-limits) cikk.
3. Nem járnak a foglalt IP-cím? <br>
    Egyes esetekben. Díjszabása, tekintse meg a [fenntartott IP cím díjszabás](http://go.microsoft.com/fwlink/?LinkID=398482) lap.
4. Hogyan foglaljon le egy IP-címet? <br>
    Használhatja a PowerShell, a [Azure szolgáltatásfelügyelet REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx), vagy a [Azure-portálon](https://portal.azure.com) Azure-régióban IP-címet lefoglalni. A fenntartott IP-cím az előfizetéshez tartozik.
5. Használhatok egy fenntartott IP-cím affinitáscsoport-alapú Vnetekhez? <br>
    Nem. Fenntartott IP-címek csak a regionális Vnetek támogatottak. Fenntartott IP-címek nem támogatottak a Vnetek társított affinitáscsoportokat. Egy VNet társít egy régiót vagy affinitáscsoportot kapcsolatos további információkért tekintse meg a [regionális Vnetek és Affinitáscsoportok](virtual-networks-migrate-to-regional-vnet.md) cikk.

## <a name="manage-reserved-vips"></a>Fenntartott virtuális IP-címek kezelése

Győződjön meg arról, hogy telepítette és konfigurálta a PowerShell; Ehhez hajtsa végre a lépéseket a [telepítse és konfigurálja a PowerShell](/powershell/azure/overview) cikk. 

Fenntartott IP-címek használata előtt hozzá kell adnia az előfizetése. A foglalt IP-cím létrehozása a nyilvános IP-címkészletet érhető el a *USA középső RÉGIÓJA* helyét, a következő parancsot:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Figyelje meg, azonban, hogy nem adható meg mi IP folyamatban van fenntartva. Milyen IP-cím van lefoglalva az előfizetés megtekintéséhez futtassa a következő PowerShell-parancsot, és figyelje meg értékeit *ReservedIPName* és *cím*:

```powershell
Get-AzureReservedIP
```

Várt kimenet:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Amikor egy fenntartott IP-cím a PowerShell használatával hoz létre, nem adható meg egy erőforráscsoportot a foglalt IP-cím létrehozásához. Még egy erőforráscsoportot az Azure helyek *alapértelmezett-hálózat* automatikusan. A fenntartott IP-történő létrehozásakor a [Azure-portálon](http://portal.azure.com), megadhatja, hogy bármely erőforráscsoport választja. Eltérő erőforráscsoportban létrehozásakor a foglalt IP-cím *alapértelmezett-hálózat* azonban amikor hivatkozik a foglalt IP-cím parancsok, mint `Get-AzureReservedIP` és `Remove-AzureReservedIP`, a nevet kell hivatkoznia  *Erőforrás-csoportnév fenntartott-ip-név csoport*.  Például, ha egy nevű fenntartott IP-cím létrehozása *myReservedIP* erőforráscsoportban nevű *myResourceGroup*, kell hivatkoznia, mint a foglalt IP-cím neve *csoport myResourceGroup myReservedIP*.   

Ha egy IP-cím foglalt, marad az előfizetéshez társított amíg nem törli azokat. A foglalt IP-cím törlése, futtassa a következő PowerShell-parancsot:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Foglaljon le egy meglévő felhőszolgáltatáshoz IP-címe
Az IP-címét egy meglévő felhőszolgáltatáshoz foglalhat hozzáadásával a `-ServiceName` paraméter. A felhőalapú szolgáltatások IP-cím lefoglalása *TestService* a a *USA középső RÉGIÓJA* helyét, a következő PowerShell-parancsot:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Új felhőalapú szolgáltatás foglalt IP-cím hozzárendelése
Az alábbi parancsfájlt hoz létre egy új fenntartott IP-cím, majd társítja azt nevű új felhőalapú szolgáltatás *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Amikor létrehoz egy fenntartott IP-cím egy felhőalapú szolgáltatás használata, továbbra is hivatkozik a virtuális gép használatával *VIP:&lt;portszám >* bejövő kommunikációhoz. Ha olyan IP-címet nem jelenti azt közvetlenül csatlakozzon a virtuális gép. A foglalt IP-cím hozzá van rendelve a felhőszolgáltatás, amely a virtuális gép már alkalmazva van. Ha azt szeretné, a virtuális gépek IP-címekként közvetlenül kapcsolódni, hogy konfigurálnia olyan példányszintű nyilvános IP-címet. Olyan példányszintű nyilvános IP-címet egy olyan nyilvános IP-cím (más néven egy ILPIP) közvetlenül a virtuális Géphez rendelt típusú. Nem lehet lefoglalni. További információkért olvassa el a [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) cikk.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>A foglalt IP-cím eltávolítása a futó központi telepítés
A foglalt IP-cím hozzá egy új felhőalapú szolgáltatás eltávolításához futtassa a következő PowerShell-parancsot:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Egy futó telepítésből egy fenntartott IP-cím eltávolítása nem törli a Foglalás az előfizetésből. Egyszerűen az előfizetésében szereplő más erőforrás által használandó IP szabadít fel.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Egy futó telepítéshez foglalt IP-cím hozzárendelése
A következő parancsok nevű felhőalapú szolgáltatás létrehozása *TestService2* nevű új virtuális gépen *TestVM2*. A meglévő foglalt IP-cím, nevű *MyReservedIP* áll majd a felhőszolgáltatáshoz társított.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>A foglalt IP-cím egy felhőszolgáltatásra történő társíthatók a szolgáltatás konfigurációs fájl használatával
A foglalt IP-cím egy felhőszolgáltatásra történő szolgáltatás (szolgáltatáskonfigurációs SÉMA) konfigurációs fájl használatával is társíthat. Az alábbi minta XML-kód bemutatja, hogyan nevű fenntartott virtuális IP-címhez használandó felhőszolgáltatás konfigurálása *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>További lépések
* Megértéséhez hogyan [IP-címzés](virtual-network-ip-addresses-overview-classic.md) a klasszikus üzembe helyezési modellel működik.
* További tudnivalók [magánhálózati IP-címek fenntartott](virtual-networks-reserved-private-ip.md).
* További tudnivalók [példány szint nyilvános IP (ILPIP) címek](virtual-networks-instance-level-public-ip.md).

