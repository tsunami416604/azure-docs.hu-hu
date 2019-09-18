---
title: Azure fenntartott IP-címek kezelése (klasszikus) | Microsoft Docs
description: A fenntartott IP-címek (klasszikus) ismertetése, valamint a Azure PowerShell és az Azure CLI használatával történő kezelésük.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 675e7a7b557a3f19ea4d8d4960316c3859cbb9c1
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058477"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Fenntartott IP címek (klasszikus üzembe helyezés)

 Az Azure-beli IP-címek két kategóriába sorolhatók: dinamikus és fenntartott. Az Azure által felügyelt nyilvános IP-címek alapértelmezés szerint dinamikusak. Ez azt jelenti, hogy egy adott felhőalapú szolgáltatás (VIP) vagy egy virtuális gép vagy szerepkör-példány közvetlen eléréséhez használt IP-cím (ILPIP) időről időre változhat az erőforrások leállítása vagy leállítása (fel nem foglalt) miatt.

Ha meg szeretné akadályozni, hogy az IP-címek módosítva legyenek, fenntarthat egy IP-címet. A fenntartott IP-címek csak VIP-ként használhatók, így biztosítható, hogy a felhőalapú szolgáltatás IP-címe azonos maradjon, még akkor is, ha az erőforrások le vannak állítva vagy le vannak állítva (fel van foglalva). Emellett a virtuális IP-címekhez használt meglévő dinamikus IP-címeket a fenntartott IP-címekre is konvertálhatja.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához:  [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Megtudhatja, hogyan foglalhat le statikus nyilvános IP-címet a [Resource Manager](virtual-network-ip-addresses-overview-arm.md)-alapú üzemi modell használatával.

Ha többet szeretne megtudni az Azure-beli IP-címekről, olvassa el az [IP-címekről](virtual-network-ip-addresses-overview-classic.md) szóló cikket.

## <a name="when-do-i-need-a-reserved-ip"></a>Mikor van szükség a fenntartott IP-címekre?
* **Biztosítani szeretné, hogy az IP-cím le legyen foglalva az előfizetésben**. Ha olyan IP-címet szeretne lefoglalni, amely semmilyen körülmények között nem érhető el az előfizetésből, a fenntartott nyilvános IP-címeket kell használnia.  
* Azt **szeretné, hogy az IP-címe még a leállított vagy a nem lefoglalt állapot (VM-EK) között is maradjon a felhőalapú szolgáltatásban**. Ha azt szeretné, hogy a szolgáltatás egy olyan IP-cím használatával legyen elérhető, amely nem változik, még akkor is, ha a felhőalapú szolgáltatásban lévő virtuális gépek le vannak állítva vagy le vannak állítva (fel van foglalva).
* **Szeretné biztosítani, hogy az Azure-ból érkező kimenő forgalom kiszámítható IP-címet használjon**. Előfordulhat, hogy a helyszíni tűzfal úgy van konfigurálva, hogy csak adott IP-címekről érkező forgalmat engedélyezzen. Egy IP-cím megadásával ismeri a forrás IP-címét, és az IP-változás miatt nem kell frissítenie a tűzfalszabályok szabályait.

## <a name="faqs"></a>Gyakori kérdések
- Használhatom a fenntartott IP-címet az összes Azure-szolgáltatáshoz?
    Nem. A fenntartott IP-címek csak VIP-en keresztül elérhető virtuális gépekhez és felhőalapú szolgáltatási példányokhoz használhatók.
- Hány fenntartott IP-címet használhatok?
    Részletekért tekintse meg az [Azure korlátozásait](../azure-subscription-service-limits.md#networking-limits) ismertető cikket.
- Számítanak fel díjat a fenntartott IP-címekért?
    Néha. A díjszabással kapcsolatos részletekért tekintse meg a [fenntartott IP címe díjszabását](https://go.microsoft.com/fwlink/?LinkID=398482) ismertető oldalt.
- Hogyan a fenntartott IP-címet?
    Az Azure-régiókban a PowerShell, az [Azure felügyeleti REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)vagy a [Azure Portal](https://portal.azure.com) használatával foglalhat le IP-címet. A fenntartott IP-címek az előfizetéshez vannak társítva.
- Használhatom a fenntartott IP-címet affinitási csoport alapú virtuális hálózatok?
    Nem. A fenntartott IP-címek csak a regionális virtuális hálózatok támogatottak. A fenntartott IP-címek nem támogatottak az affinitási csoportokhoz társított virtuális hálózatok esetén. A VNet régióval vagy affinitással való társításával kapcsolatos további információkért tekintse meg a [regionális virtuális hálózatok és az affinitási csoportok ismertetése](virtual-networks-migrate-to-regional-vnet.md) című cikket.

## <a name="manage-reserved-vips"></a>Fenntartott VIP-címek kezelése

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használata

A fenntartott IP-címek használata előtt fel kell vennie azt az előfizetésbe. Hozzon létre egy fenntartott IP-címet az *USA középső* régiójában elérhető nyilvános IP-címek készletéből a következő módon:

> [!NOTE]
> A klasszikus üzemi modell esetében telepítenie kell Azure PowerShell Service Management verzióját. További információ: [Az Azure PowerShell Service Management modul áttekintése](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Figyelje meg azonban, hogy nem tudja megadni, hogy milyen IP-címet foglal le. Ha szeretné megtekinteni az előfizetésben foglalt IP-címeket, futtassa a következő PowerShell-parancsot, és figyelje meg a *ReservedIPName* és a *cím*értékét:

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
>Ha fenntartott IP-címet hoz létre a PowerShell-lel, nem adhat meg erőforráscsoportot a fenntartott IP-cím létrehozásához. Az Azure egy *alapértelmezett-hálózatkezelés* nevű erőforráscsoportba helyezi azt. Ha a [Azure Portal](https://portal.azure.com)használatával hozza létre a fenntartott IP-címet, megadhat bármely kiválasztott erőforráscsoportot. Ha a fenntartott IP-címet az *alapértelmezett hálózatkezeléstől* eltérő erőforráscsoporthoz hozza létre, akkor is, ha a fenntartott IP-címet a `Get-AzureReservedIP` ( `Remove-AzureReservedIP`z) és a (z), a (z) és a (z) parancsra hivatkozik, akkor a név *Csoport fenntartott IP-név*.  Ha például egy *myResourceGroup*nevű erőforráscsoport létrehoz egy *myReservedIP* nevű fenntartott IP-címet, akkor a fenntartott IP-címet a *csoport myResourceGroup myReservedIP*kell hivatkoznia.   


Ha egy IP-cím le van foglalva, az előfizetés addig marad, amíg nem törli. A következő módon törölheti a fenntartott IP-címet:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Az Azure CLI (klasszikus) használata
Hozzon létre egy fenntartott IP-címet az *USA középső* régiójában elérhető nyilvános IP-címek készletéről a klasszikus Azure CLI használatával:

> [!NOTE]
> A klasszikus üzembe helyezéshez a klasszikus Azure CLI-t kell használnia. A klasszikus Azure CLI telepítésével kapcsolatos információkért lásd: [a klasszikus Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Parancs
 
```azurecli
azure network reserved-ip create <name> <location>
```
Példa:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

A következő módon tekintheti meg, hogy milyen IP-címek vannak lefoglalva az előfizetésben az Azure CLI használatával: 

Parancs
```azurecli
azure network reserved-ip list
```
Ha egy IP-cím le van foglalva, az előfizetés addig marad, amíg nem törli. A következő módon törölheti a fenntartott IP-címet:

Parancs

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Példa:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Meglévő felhőalapú szolgáltatás IP-címének lefoglalása
A meglévő felhőalapú szolgáltatások IP-címét a `-ServiceName` (z) paraméter hozzáadásával is lefoglalhatja. Foglaljon le egy Cloud Service- *TestService* IP-címét az *USA középső* régiójában a következőképpen:

- Azure PowerShell (klasszikus) használata:

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Az Azure CLI (klasszikus) használata:
  
    Parancs

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Példa:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Fenntartott IP-cím hozzárendelése egy új felhőalapú szolgáltatáshoz
A következő szkript létrehoz egy új fenntartott IP-címet, majd társítja azt egy *TestService*nevű új felhőalapú szolgáltatáshoz.

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használata
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Ha egy felhőalapú szolgáltatással való használatra fenntartott IP-címet hoz létre, továbbra is a virtuális gépet a következő VIP-cím használatával tekintheti meg *:&lt;>* a bejövő kommunikációhoz. Az IP-címek lefoglalása nem jelenti azt, hogy közvetlenül tud csatlakozni a virtuális géphez. A fenntartott IP-cím ahhoz a felhőalapú szolgáltatáshoz van rendelve, amelyre a virtuális gép telepítve lett. Ha közvetlenül IP-címmel szeretne csatlakozni egy virtuális géphez, konfigurálnia kell egy példány szintű nyilvános IP-címet. A példány szintű nyilvános IP-cím olyan nyilvános IP-cím (ILPIP), amely közvetlenül a virtuális géphez van rendelve. Nem foglalható le. További információért olvassa el a [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) című cikket.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Lefoglalt IP-cím eltávolítása egy futó telepítésből

Távolítson el egy új felhőalapú szolgáltatáshoz hozzáadott fenntartott IP-címet a következőképpen: 
### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használata

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Az Azure CLI (klasszikus) használata
Parancs

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Példa:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> A fenntartott IP-címek egy futó telepítésből való eltávolítása nem távolítja el a foglalást az előfizetésből. Egyszerűen felszabadítja az IP-címet, amelyet az előfizetése egy másik erőforrása használ.
> 

A fenntartott IP-címek teljes előfizetésből való eltávolításához futtassa a következő parancsot:

Parancs

```azurecli
azure network reserved-ip delete <name>
```
Példa:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Fenntartott IP-cím hozzárendelése futó központi telepítéshez

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (klasszikus) használata

A következő parancsok létrehoznak egy *TestService2* nevű Cloud Service-t egy új, *TestVM2*nevű virtuális géppel. Ezután a *MyReservedIP* nevű meglévő fenntartott IP-címet társítjuk a Cloud Service-hez.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Az Azure CLI (klasszikus) használata
A következő módon rendelhet hozzá új fenntartott IP-címet a futó felhőalapú szolgáltatás üzembe helyezéséhez az Azure CLI használatával:

Parancs
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Példa:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Fenntartott IP-cím hozzárendelése egy felhőalapú szolgáltatáshoz szolgáltatás-konfigurációs fájl használatával
Egy fenntartott IP-címet egy CSCFG-fájl használatával is hozzárendelhet egy felhőalapú szolgáltatáshoz. Az alábbi XML-minta bemutatja, hogyan konfigurálhat egy felhőalapú szolgáltatást egy *MyReservedIP*nevű fenntartott VIP használatára:
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
* Ismerje meg, hogyan működik az [IP-címzés](virtual-network-ip-addresses-overview-classic.md) a klasszikus üzemi modellben.
* További információ a [fenntartott magánhálózati IP-címekről](virtual-networks-reserved-private-ip.md).
* További információ a [példányok szintjének nyilvános IP-címeiről (ILPIP)](virtual-networks-instance-level-public-ip.md).

