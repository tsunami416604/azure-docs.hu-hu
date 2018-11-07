---
title: Az Azure fenntartott IP-címek (klasszikus) kezelése |} A Microsoft Docs
description: Fenntartott IP-címek (klasszikus) és azokat kezelheti az Azure PowerShell és az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: bd2b28a7f8d0a765e10ffa58b5a72b4bd5bc47b0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228181"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Fenntartott IP-címek (klasszikus üzembe helyezési modell)

 IP-címek az Azure-ban két kategóriába sorolhatók: dinamikus és fenntartott. Az Azure által felügyelt, nyilvános IP-címek dinamikusak alapértelmezés szerint. Ez azt jelenti, hogy az IP-cím egy adott felhőalapú szolgáltatás (VIP) vagy a virtuális gép eléréséhez használt, vagy közvetlenül szerepkörpéldány (ILPIP) módosíthatja időről időre, ha erőforrások állítsa le vagy leállítva (felszabadítva).

Megakadályozni IP-címek módosítása, IP-cím tartható fenn. Fenntartott IP-címek csak egy virtuális IP-CÍMEK, biztosítva, hogy a felhőszolgáltatás IP-címe ugyanaz marad, még akkor is, mint az erőforrások állítsa le vagy leállítva (felszabadítva) használható. Emellett átválthat a meglévő dinamikus IP-címek fenntartott IP-címet a virtuális IP-címhez használja.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, hogyan foglaljon le egy statikus nyilvános IP cím használatával a [Resource Manager üzemi modell](virtual-network-ip-addresses-overview-arm.md).

Az Azure-beli IP-címek kapcsolatos további információkért olvassa el a [IP-címek](virtual-network-ip-addresses-overview-classic.md) cikk.

## <a name="when-do-i-need-a-reserved-ip"></a>Ha szükséges a fenntartott IP-cím?
* **Győződjön meg arról, hogy a IP-cím az előfizetésében foglalt szeretné**. Ha szeretne lefoglalni az IP-címet, amely nem jelent semmilyen körülmények között az előfizetésből, használjon fenntartott nyilvános IP-cím.  
* **Azt szeretné, hogy az IP-címet a felhőszolgáltatást, még akkor is keresztben leállt, vagy (VM) állapot felszabadítása maradjon**. Ha azt szeretné, hogy a szolgáltatás IP-cím használatával érhető el, amely nem változik, még ha a felhőszolgáltatás virtuális gépeinek állítsa le vagy stop (deallocated).
* **Ügyeljen arra, hogy az Azure-ból kimenő forgalmat egy előre jelezhető IP-címet szeretne**. Előfordulhat, hogy a helyszíni tűzfal csak az adott IP-címekről érkező forgalmat engedélyezi. IP-címet lefoglalásával tudja a forrás IP-címet, és nem kell frissítenie a tűzfalszabályok egy IP-módosítás miatt.

## <a name="faqs"></a>Gyakori kérdések
- Használható az Azure-szolgáltatásokhoz fenntartott IP-cím?
    Nem. Fenntartott IP-címek csak akkor használható, a virtuális gépeket és példányszerepköröket egy VIP-n keresztül teszi közzé, felhőalapú szolgáltatás.
- Hány fenntartott IP-címek is használhatok?
    További információkért lásd: a [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) cikk.
- Felszámolnak díjat a fenntartott IP-címek?
    Egyes esetekben. A díjszabás részleteiért tekintse meg a [fenntartott IP cím-díjszabás –](https://go.microsoft.com/fwlink/?LinkID=398482) lapot.
- Hogyan foglaljon le egy IP-címet?
    Használhatja a PowerShell, a [Azure Management REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx), vagy a [az Azure portal](https://portal.azure.com) lefoglalása az IP-cím, egy Azure-régióban. Fenntartott IP-cím az előfizetés társítva.
- Használható affinitáscsoport-alapú virtuális hálózatok fenntartott IP-cím?
    Nem. Fenntartott IP-címek csak a regionális virtuális hálózatok támogatottak. Fenntartott IP-címek nem támogatottak az affinitáscsoportok társított virtuális hálózatok. Virtuális hálózat társítása a régiója vagy affinitáscsoportja kapcsolatos további információkért lásd: a [vonatkozó regionális virtuális hálózatok és az Affinitáscsoportok](virtual-networks-migrate-to-regional-vnet.md) cikk.

## <a name="manage-reserved-vips"></a>Fenntartott virtuális IP-címek kezelése

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használatával

Fenntartott IP-címek használata előtt hozzá kell adnia azt az előfizetéséhez. Hozzon létre egy fenntartott IP-cím elérhető a nyilvános IP-címkészlet a *USA középső RÉGIÓJA* helye az alábbiak szerint:

> [!NOTE]
> Klasszikus üzemi modell telepítenie kell az Azure PowerShell Service Management verzióját. További információ: [Az Azure PowerShell Service Management modul áttekintése](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Vegye figyelembe azonban, hogy nem adható meg mi IP folyamatban van fenntartva. Milyen IP-címek vannak fenntartva az előfizetés megtekintéséhez futtassa a következő PowerShell-parancsot, és figyelje meg a tartozó értékeket *ReservedIPName* és *cím*:

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
>Ha egy fenntartott IP-cím létrehozása a PowerShell használatával, egy erőforráscsoportot, amelybe a fenntartott IP-cím létrehozása nem adható meg. Helyezzen egy erőforráscsoportban, nevű Azure helyek *alapértelmezett hálózati* automatikusan. Ha hoz létre a fenntartott IP-használatával a [az Azure portal](http://portal.azure.com), megadhatja, hogy bármely erőforráscsoport választja. Más, az erőforráscsoport létrehozásakor a fenntartott IP-cím *alapértelmezett hálózati* azonban minden alkalommal, amikor hivatkozik a foglalt IP-cím parancsok például `Get-AzureReservedIP` és `Remove-AzureReservedIP`, a nevet kell hivatkozni  *Csoport erőforráscsoportnevet szolgáltatás számára fenntartott ip-neve*.  Például, ha létrehoz egy fenntartott IP-cím nevű *myReservedIP* nevű erőforráscsoportból *myResourceGroup*, hivatkoznia kell a neve, a fenntartott IP-cím *myResourceGroup erőforráscsoport myReservedIP*.   


Miután egy IP-cím van fenntartva, továbbra is az előfizetéshez társított amíg nem törli azokat. Törölje a következő fenntartott IP-cím:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Az Azure parancssori felület (klasszikus) használatával
Hozzon létre egy fenntartott IP-cím elérhető a nyilvános IP-címkészlet a *USA középső RÉGIÓJA* az Azure klasszikus parancssori felület a következő helyen:

> [!NOTE]
> Klasszikus üzembe helyezés az Azure klasszikus parancssori felület kell használnia. Az Azure klasszikus parancssori felület telepítésével kapcsolatos információkért lásd: [a klasszikus Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 A parancs:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Példa:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Megtekintheti, milyen IP-címek vannak fenntartva az előfizetésben az Azure CLI használatával az alábbiak szerint: 

A parancs:
```azurecli
azure network reserved-ip list
```
Miután egy IP-cím van fenntartva, továbbra is az előfizetéshez társított amíg nem törli azokat. Törölje a következő fenntartott IP-cím:

A parancs:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Példa:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Egy létező felhőszolgáltatás IP-címének fenntartása
Egy létező felhőszolgáltatás IP-cím tartható fenn hozzáadásával a `-ServiceName` paraméter. Egy felhőalapú szolgáltatás IP-címének fenntartása *TestService* a a *USA középső RÉGIÓJA* helye az alábbiak szerint:

- Azure PowerShell (klasszikus) használatával:

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Az Azure parancssori felület (klasszikus) használatával:
  
    A parancs:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Példa:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Új felhőszolgáltatás fenntartott IP társítása
A következő szkript létrehoz egy új, foglalt IP-címet, majd társítja azt nevű új felhőszolgáltatásba való *TestService*.

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használatával
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Amikor létrehoz egy fenntartott IP-címet egy felhőalapú szolgáltatás használata, továbbra is hivatkozik, a virtuális gép használatával *virtuális IP-cím:&lt;portszám >* a bejövő kommunikációhoz. Egy IP-cím foglalása jelenti azt, hogy közvetlenül csatlakozzon a virtuális Géphez. A fenntartott IP-cím hozzá van rendelve a felhőszolgáltatás, amely a virtuális gép már alkalmazva van. Ha szeretne közvetlenül kapcsolódni egy virtuális gép IP-cím alapján, hogy egy példányszintű nyilvános IP-cím konfigurálnia. A példányszintű nyilvános IP-cím egy olyan típusú nyilvános IP-Címek (ILPIP-nevezik), amely közvetlenül a virtuális Géphez van rendelve. Nem foglalható. További információkért olvassa el a [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) cikk.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Fenntartott IP-cím eltávolítása egy üzemelő példányban

Távolítsa el a foglalt IP-cím új felhőszolgáltatáshoz hozzá a következő: 
### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használatával

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Az Azure parancssori felület (klasszikus) használatával
A parancs:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Példa:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Az üzemelő példányban egy fenntartott IP-cím eltávolítása nem távolítja el a foglalást az előfizetésből. Egyszerűen lehet az előfizetés egy másik erőforrás használja az IP-cím szabadít fel.
> 

Fenntartott IP-cím teljes eltávolítása egy előfizetésből, futtassa a következő parancsot:

A parancs:

```azurecli
azure network reserved-ip delete <name>
```
Példa:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Az üzemelő példányban fenntartott IP társítása

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használatával

A következő parancsok nevű felhőszolgáltatás létrehozása *TestService2* nevű új virtuális gépen *TestVM2*. A meglévő fenntartott IP-nevű *MyReservedIP* áll majd a felhőszolgáltatáshoz társított.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Az Azure parancssori felület (klasszikus) használatával
Új fenntartott IP-cím társítható a futó felhőszolgáltatás üzembe helyezésének Azure CLI használatával az alábbiak szerint:

A parancs:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Példa:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Felhőszolgáltatás fenntartott ip társítása a szolgáltatás konfigurációs fájl segítségével
Felhőszolgáltatás fenntartott IP-szolgáltatás (CSCFG) konfigurációs fájl használatával is társíthat. A következő minta xml bemutatja, hogyan nevű fenntartott virtuális IP-címhez használandó felhőszolgáltatás konfigurálása *MyReservedIP*:
```
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
```
## <a name="next-steps"></a>További lépések
* Megismerheti hogyan [IP-címkezelés](virtual-network-ip-addresses-overview-classic.md) működik a klasszikus üzemi modellben.
* Ismerje meg [magánhálózati IP-címek fenntartott](virtual-networks-reserved-private-ip.md).
* Ismerje meg [példány szintű nyilvános IP (ILPIP) címek](virtual-networks-instance-level-public-ip.md).

