---
title: Virtuális gépek üzembe helyezése az Azure Stack Edge GPU-eszközön a Azure PowerShell használatával
description: Ismerteti, hogyan lehet virtuális gépeket (VM-ket) létrehozni és felügyelni egy Azure Stack Edge GPU-eszközön Azure PowerShell használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 2c8d9a27725259c34060552494a56f14621f54b4
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147031"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Virtuális gépek üzembe helyezése az Azure Stack Edge-eszközön Azure PowerShell használatával

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

Ez az oktatóanyag leírja, hogyan hozhat létre és kezelhet virtuális gépeket a Azure Stack peremhálózati eszközön Azure PowerShell használatával.

## <a name="vm-deployment-workflow"></a>Virtuális gép üzembe helyezésének munkafolyamata

Az üzembe helyezési munkafolyamat a következő ábrán látható.

![Virtuális gép üzembe helyezésének munkafolyamata](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>Az eszköz beépített előfizetésének lekérdezése

Azure Resource Manager esetében csak egyetlen felhasználó által látható rögzített előfizetés támogatott. Ez az előfizetés eszközönként egyedi, és ez az előfizetés-név vagy előfizetés-azonosító nem módosítható.

Ez az előfizetés tartalmazza a virtuális gépek létrehozásához szükséges összes erőforrást. 

> [!IMPORTANT]
> Ez az előfizetés nem kapcsolódik az Azure-előfizetéshez, és helyileg él az eszközön.

Ezt az előfizetést a virtuális gépek üzembe helyezéséhez fogjuk használni.

1.  Az előfizetés listázásához írja be a következőt:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Az alábbiakban egy példa látható a kimenetre.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  Az eszközön futó regisztrált erőforrás-szolgáltatók listájának beolvasása. Ez a lista általában számítást, hálózatot és tárterületet tartalmaz.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Az erőforrás-szolgáltatók előre regisztrálva vannak, és nem módosíthatók és nem módosíthatók.
    
    Alább látható egy minta kimenet:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat, például a Storage-fiókot, a lemezt, a felügyelt lemezt.

> [!IMPORTANT]
> Az összes erőforrás ugyanabban a helyen jön létre, mint az eszköz, és a hely a **DBELocal**értékre van állítva.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Az alábbiakban egy példa látható a kimenetre.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy új Storage-fiókot az előző lépésben létrehozott erőforráscsoport használatával. Ez egy **helyi Storage-fiók** , amely a virtuális gép virtuális lemezének lemezképének feltöltésére szolgál majd.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> A Azure Resource Manager használatával csak a helyi Storage-fiókok, például a helyileg redundáns tárolás (Standard_LRS vagy Premium_LRS) hozhatók létre. A többplatformos tárolási fiókok létrehozásához tekintse [meg a Hozzáadás, kapcsolódás a Storage-fiókokhoz a Azure stack Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md)-ben című témakör lépéseit.

Az alábbiakban egy példa látható a kimenetre.

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

A Storage-fiók kulcsainak lekéréséhez futtassa a `Get-AzureRmStorageAccountKey` parancsot. Itt látható a parancs mintájának kimenete.

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>BLOB URI hozzáadása a Hosts fájlhoz

Már hozzáadta a blob URI-t a Hosts fájlban ahhoz az ügyfélhez, amelyet a blob Storage-hoz való kapcsolódáshoz használ a [gazdagép fájljának módosítása végpont-névfeloldáshoz](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)című szakaszban. Ez volt a blob URI bejegyzése:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>


## <a name="install-certificates"></a>Tanúsítványok telepítése

Ha *https*-t használ, telepítenie kell a megfelelő tanúsítványokat az eszközre. Ebben az esetben telepítse a blob Endpoint-tanúsítványt. További információ: tanúsítványok létrehozása és feltöltése a [tanúsítványok kezelése](azure-stack-edge-j-series-manage-certificates.md)szolgáltatásban.

## <a name="upload-a-vhd"></a>VHD feltöltése

Másolja a korábbi lépésekben létrehozott helyi Storage-fiókban a lapok blobba felhasználható lemezes lemezképeit. Egy eszköz, például a [AzCopy](../storage/common/storage-use-azcopy-v10.md) használatával feltöltheti a virtuális merevlemezt a korábbi lépések során létrehozott Storage-fiókba. 

A AzCopy használata előtt győződjön meg arról, hogy a [AzCopy megfelelően van](#configure-azcopy) -e konfigurálva a blob storage-REST API verziójával, amelyet az Azure stack Edge-eszközzel használ.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Állítsa be a `BlobType` lapot a felügyelt lemez VHD-ből való létrehozásához. Állítsa be `BlobType` a blokkot, ha a AzCopy használatával rétegű Storage-fiókokba ír.

A lemezképeket letöltheti a Piactérről. A részletes lépésekért lásd: [a virtuális lemezkép beszerzése az Azure Marketplace-ről](azure-stack-edge-j-series-create-virtual-machine-image.md).

Alább látható egy AzCopy 7,3-et használó minta kimenet. A paranccsal kapcsolatos további információkért lépjen a VHD- [fájl feltöltése a Storage-fiókba a AzCopy használatával](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md)című témakörben.


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>Felügyelt lemezek létrehozása a VHD-ből

Hozzon létre egy felügyelt lemezt a feltöltött VHD-ből.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Alább látható egy minta kimenet: 

$DiskConfig = New-AzureRmDiskConfig-Location DBELocal-CreateOption import – SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Az alábbiakban egy példa látható a kimenetre. A parancsmaggal kapcsolatos további információkért nyissa meg a [New-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Virtuálisgép-rendszerkép létrehozása a rendszerkép által felügyelt lemezről

A következő parancs használatával hozzon létre egy virtuálisgép-rendszerképet a felügyelt lemezről. Cserélje le az értékeket a \< \> kiválasztott nevekre.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Az alábbiakban egy példa látható a kimenetre. A parancsmaggal kapcsolatos további információkért nyissa meg a [New-AzureRmImage](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>Virtuális gép létrehozása korábban létrehozott erőforrásokkal

Létre kell hoznia egy virtuális hálózatot, és hozzá kell rendelnie egy virtuális hálózati adaptert a virtuális gép létrehozása és telepítése előtt.

> [!IMPORTANT]
> A virtuális hálózat és a virtuális hálózati adapter létrehozásakor a következő szabályok érvényesek:
> - Csak egy vnet hozható létre (még az erőforráscsoportok között is), és pontosan egyeznie kell a logikai hálózattal a címtartomány szempontjából.
> -   A vnet csak egy alhálózat lesz engedélyezve. Az alhálózatnak pontosan ugyanaz a címtartomány kell lennie, mint a vnet.
> -   A Vnic létrehozásakor csak a statikus kiosztási módszer lesz engedélyezve, és a felhasználónak magánhálózati IP-címet kell megadnia.

 
**Vnet létrehozása**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**Vnic létrehozása a vnet alhálózati azonosító használatával**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

A parancsok mintájának kimenete alább látható:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Ha a virtuális gép számára Vnic hoz létre, a nyilvános IP-címet is átadhatja. Ebben az esetben a nyilvános IP-cím a magánhálózati IP-címet fogja visszaadni. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**Virtuális gép létrehozása**

Mostantól a virtuálisgép-rendszerkép használatával létrehozhat egy virtuális gépet, és csatlakoztathatja azt a korábban létrehozott virtuális hálózathoz.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>Kapcsolódás virtuális géphez

Kapcsolódjon a virtuális géphez a virtuális gép létrehozása során átadott magánhálózati IP-címmel.

Nyisson meg egy SSH-munkamenetet az IP-címhez való kapcsolódáshoz.

`ssh -l <username> <ip address>`

Ha a rendszer kéri, adja meg a virtuális gép létrehozásakor használt jelszót.

Ha meg kell adnia az SSH-kulcsot, használja ezt a parancsot.

SSH-i c:/Users/Administrator/. ssh/id_rsa Administrator@5.5.41.236

Ha a virtuális gép létrehozása során nyilvános IP-címet használt, akkor az adott IP-cím használatával csatlakozhat a virtuális géphez. A nyilvános IP-cím beszerzése: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
Ebben az esetben a nyilvános IP-cím megegyezik a virtuális hálózati adapter létrehozásakor átadott magánhálózati IP-címmel.


## <a name="manage-vm"></a>Virtuális gép kezelése

A következő szakasz ismerteti a virtuális gép azon gyakori műveleteit, amelyeket az Azure Stack Edge-eszközön fog létrehozni.

### <a name="list-vms-running-on-the-device"></a>Az eszközön futó virtuális gépek listázása

Az Azure Stack Edge-eszközön futó összes virtuális gép listájának visszaküldéséhez futtassa a következő parancsot.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>A virtuális gép bekapcsolása

Futtassa a következő parancsmagot az eszközön futó virtuális gépek bekapcsolásához:


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


A parancsmaggal kapcsolatos további információkért nyissa meg a [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0).

### <a name="suspend-or-shut-down-the-vm"></a>A virtuális gép felfüggesztése vagy leállítása

Futtassa a következő parancsmagot az eszközön futó virtuális gép leállításához vagy leállításához:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


A parancsmaggal kapcsolatos további információkért nyissa meg a [stop-AzureRmVM parancsmagot](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0).

### <a name="add-a-data-disk"></a>Adatlemez hozzáadása

Ha a virtuális gép számítási feladatainak növekedése megnövekszik, akkor előfordulhat, hogy adatlemezt kell hozzáadnia.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>A virtuális gép törlése

Futtassa a következő parancsmagot a virtuális gép eszközről való eltávolításához:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

A parancsmaggal kapcsolatos további információkért nyissa meg a [Remove-AzureRmVm parancsmagot](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0).


## <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

A virtuális gép mérete határozza meg a virtuális gép számára elérhető számítási erőforrások, például a processzor, a GPU és a memória mennyiségét. A virtuális gépeket a számítási feladatok számára megfelelő virtuálisgép-mérettel kell létrehozni. Annak ellenére, hogy az összes gép ugyanazon a hardveren fut, a gépi méretek eltérő korlátokkal rendelkeznek a lemezes hozzáféréshez, ami segíthet a teljes lemezes hozzáférés kezelésében a virtuális gépeken. Ha a számítási feladatok mennyisége nő, a meglévő virtuális gépet át is lehet méretezni.

A következő standard Dv2 sorozatú virtuális gépek támogatottak Azure Stack peremhálózati eszközön történő létrehozáshoz.

### <a name="dv2-series"></a>Dv2-sorozat
|Méret     |vCPU     |Memória (GiB) | Ideiglenes tároló (GiB)  | OPERÁCIÓSRENDSZER-lemez maximális átviteli sebessége (IOPS) | Maximális Temp Storage-átviteli sebesség (IOPS) | Adatlemezek maximális száma/átviteli sebesség (IOPS) | Hálózati adapterek maximális száma |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3000  |4 / 4x500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64/64x500 |8 |

### <a name="dsv2-series"></a>DSv2-sorozat
|Méret     |vCPU     |Memória (GiB) | Ideiglenes tároló (GiB)  | OPERÁCIÓSRENDSZER-lemez maximális átviteli sebessége (IOPS) | Maximális Temp Storage-átviteli sebesség (IOPS) | Adatlemezek maximális száma/átviteli sebesség (IOPS) | Hálózati adapterek maximális száma |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Dv2-sorozat
|Méret     |vCPU     |Memória (GiB) | Ideiglenes tároló (GiB)  | OPERÁCIÓSRENDSZER-lemez maximális átviteli sebessége (IOPS) | Maximális Temp Storage-átviteli sebesség (IOPS) | Adatlemezek maximális száma/átviteli sebesség (IOPS) | Hálózati adapterek maximális száma |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64/64x500  |8 |


### <a name="dsv2-series"></a>DSv2-sorozat
|Méret     |vCPU     |Memória (GiB) | Ideiglenes tároló (GiB)  | OPERÁCIÓSRENDSZER-lemez maximális átviteli sebessége (IOPS) | Maximális Temp Storage-átviteli sebesség (IOPS) | Adatlemezek maximális száma/átviteli sebesség (IOPS) | Hálózati adapterek maximális száma |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

További információért látogasson el [általános célú VM-méretek Dv2 sorozatára](../virtual-machines/dv2-dsv2-series.md#dv2-series).

## <a name="unsupported-vm-operations-and-cmdlets"></a>Nem támogatott VM-műveletek és-parancsmagok

A bővítmény, a méretezési csoportok, a rendelkezésre állási csoportok, a pillanatképek nem támogatottak.

## <a name="configure-azcopy"></a>AzCopy konfigurálása

A AzCopy legújabb verziójának telepítésekor konfigurálnia kell a AzCopy-t, hogy az megfeleljen az Azure Stack Edge-eszköz blob Storage REST API verziójának.

Az Azure Stack Edge-eszköz eléréséhez használt ügyfélen állítson be egy globális változót, amely megfelel a blob Storage REST API verziójának.

### <a name="on-windows-client"></a>Windows-ügyfélen 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>Linux-ügyfélen

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

A következő lépésekkel ellenőrizheti, hogy a AzCopy környezeti változója helyesen van-e beállítva:

1. "Azcopy env" futtatása
2. Keresse meg a `AZCOPY_DEFAULT_SERVICE_API_VERSION` paramétert. Ennek az előző lépésekben beállított értéknek kell lennie.


## <a name="next-steps"></a>Következő lépések

[Azure Resource Manager-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
