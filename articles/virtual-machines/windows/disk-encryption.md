---
title: Az Azure Managed Disks kiszolgálóoldali titkosítása – PowerShell
description: Az Azure Storage védi az adatait úgy, hogy titkosítja a nyugalmát, mielőtt megőrzi azt a Storage-fürtökön. A felügyelt lemezek titkosításához a Microsoft által felügyelt kulcsokat használhatja, vagy az ügyfél által felügyelt kulcsokkal kezelheti a titkosítást a saját kulcsaival.
author: roygara
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: f3ce439f3e8c2290539e088402c2636974d37821
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898847"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Az Azure Managed Disks kiszolgálóoldali titkosítása

Az Azure Managed Disks szolgáltatás alapértelmezés szerint automatikusan titkosítja az adatait, amikor a felhőbe tart. A kiszolgálóoldali titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az Azure Managed Disks szolgáltatásban tárolt adatforgalom transzparens módon, 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), az egyik legerősebb blokk titkosítási algoritmussal, valamint az FIPS 140-2-kompatibilis.   

A titkosítás nem befolyásolja a felügyelt lemezek teljesítményét. A titkosításhoz nem jár további díj.

Az Azure Managed Disks mögöttes titkosítási modulokkal kapcsolatos további információkért lásd: a [kriptográfiai API: következő generáció](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A felügyelt lemez titkosításához a platform által felügyelt kulcsokat használhatja, vagy a titkosítást saját kulcsok használatával is kezelheti. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a felügyelt lemezeken lévő összes érték titkosítására és visszafejtésére használhat. 

A következő szakaszok részletesebben ismertetik a kulcskezelő lehetőségeit.

## <a name="platform-managed-keys"></a>Platform által felügyelt kulcsok

Alapértelmezés szerint a felügyelt lemezek platform által felügyelt titkosítási kulcsokat használnak. 2017. június 10-én a meglévő felügyelt lemezekre írt összes új felügyelt lemez, pillanatkép, kép és új adatok automatikusan titkosítva vannak a platform által felügyelt kulcsokkal. 

## <a name="customer-managed-keys"></a>Felhasználó által kezelt kulcsok

Az egyes felügyelt lemezek szintjén is kezelheti a titkosítást a saját kulcsaival. Az ügyfél által felügyelt kulcsokkal rendelkező felügyelt lemezek kiszolgálóoldali titkosítása integrált élményt nyújt a Azure Key Vault. Importálhatja az [RSA-kulcsokat](../../key-vault/key-vault-hsm-protected-keys.md) a Key Vault, vagy LÉTREHOZHAT új RSA-kulcsokat Azure Key Vault. Az Azure Managed Disks a titkosítást és a visszafejtést teljes mértékben átlátható módon kezeli a [boríték-titkosítás](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)használatával. Titkosítja az adatait egy [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-alapú adattitkosítási kulccsal (adattitkosítási kulcsot), amely viszont a kulcsok használatával védett. Hozzáférést kell biztosítania a Key Vault felügyelt lemezéhez, hogy a kulcsokat a ADATTITKOSÍTÁSI kulcsot titkosítására és visszafejtésére használja. Ez lehetővé teszi, hogy teljes körűen vezérelje adatait és kulcsait. Bármikor letilthatja a kulcsokat, vagy visszavonhatja a hozzáférést a felügyelt lemezekhez. A titkosítási kulcs használatát Azure Key Vault figyeléssel is naplózhatja, így biztosítva, hogy csak a felügyelt lemezek vagy más megbízható Azure-szolgáltatások férhessenek hozzá a kulcsokhoz.

A következő ábra azt mutatja be, hogy a felügyelt lemezek hogyan használják a Azure Active Directory és Azure Key Vault a kérelmeket az ügyfél által felügyelt kulcs használatával:

![Felügyelt lemez és ügyfél által felügyelt kulcsok munkafolyamata. A rendszergazda létrehoz egy Azure Key Vault, majd létrehoz egy lemezes titkosítási készletet, és beállítja a lemez titkosítási készletét. A készlet egy olyan virtuális géphez van társítva, amely lehetővé teszi, hogy a lemez használhassa az Azure AD-t a hitelesítéshez](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A következő lista a diagramot mutatja be még részletesebben:

1. A Azure Key Vault rendszergazdája létrehoz egy Key Vault-erőforrásokat.
1. A Key Vault rendszergazdája importálja az RSA-kulcsokat, hogy Key Vault vagy új RSA-kulcsokat generáljon a Key Vaultban.
1. A rendszergazda a lemez titkosítási készletének erőforrásának egy példányát hozza létre, amely egy Azure Key Vault azonosítót és egy kulcs URL-címet ad meg. A lemezes titkosítási készlet egy új erőforrás, amely leegyszerűsíti a felügyelt lemezek kulcsfontosságú felügyeletét. 
1. Lemezes titkosítási csoport létrehozásakor a [rendszer hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) hoz létre Azure Active Directory (ad) és a lemez titkosítási készletéhez társítva. 
1. Az Azure Key Vault rendszergazdája ezt követően engedélyt ad a felügyelt identitásnak a kulcstartó műveleteinek elvégzésére.
1. A virtuálisgép-felhasználók lemezek létrehozásával társítják őket a lemezes titkosítási készlettel. A virtuális gép felhasználója a meglévő erőforrások ügyfél által felügyelt kulcsaival is engedélyezheti a kiszolgálóoldali titkosítást, ha a lemezes titkosítási készlettel társítja őket. 
1. A felügyelt lemezek a felügyelt identitás használatával küldenek kéréseket a Azure Key Vault.
1. Az adatok olvasásához vagy írásához a felügyelt lemezek kéréseket küldenek Azure Key Vaultnek az adatok titkosításához és visszafejtéséhez (tördeléséhez), valamint az adattitkosítási kulcs visszafejtéséhez (kicsomagolásához). 

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához lásd: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) és [Azure Key Vault parancssori](https://docs.microsoft.com/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Storage-fiókban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs nem érhető el az Azure Storage-ban.

### <a name="supported-regions"></a>Támogatott régiók

Jelenleg csak a következő régiók támogatottak:

- Elérhető az USA keleti régiójában, az USA 2. nyugati régiójában, az USA déli középső régiójában, Egyesült Királyság déli régiója régióban.
- Elérhető nyilvános előzetesként az USA nyugati középső régiójában, az USA 2. keleti régiójában, Közép-Kanada és Észak-Európa régióban.

### <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsokra a következő korlátozások vonatkoznak:

- Csak a 2080 méretű ["Soft" és "Hard" RSA-kulcsok](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) támogatottak, nincsenek más kulcsok vagy méretek.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított egyéni rendszerképekből létrehozott lemezeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani, és ugyanahhoz az előfizetéshez kell tartoznia.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított lemezekről létrehozott pillanatképeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított egyéni lemezképek nem használhatók a megosztott lemezképek gyűjteményében.
- Az ügyfél által felügyelt kulcsokhoz (Azure Key Vaultok, lemez titkosítási készletek, virtuális gépek, lemezek és Pillanatképek) kapcsolódó összes erőforrásnak ugyanabban az előfizetésben és régióban kell lennie.
- Az ügyfél által felügyelt kulcsokkal titkosított lemezek, Pillanatképek és lemezképek nem helyezhetők át másik előfizetésbe.
- Ha a Azure Portal használatával hozza létre a lemez titkosítási készletét, a pillanatképek jelenleg nem használhatók.
- Az ügyfél által felügyelt kulcsokkal titkosított felügyelt lemezek nem titkosíthatók Azure Disk Encryptionval.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>A Azure Key Vault és a DiskEncryptionSet beállítása

1. Győződjön meg arról, hogy telepítette a legújabb [Azure PowerShell-verziót](/powershell/azure/install-az-ps), és be van jelentkezve egy Azure-fiókba a következővel: kapcsolat-AzAccount

1. Hozzon létre egy Azure Key Vault és egy titkosítási kulcs egy példányát.

    A Key Vault-példány létrehozásakor engedélyeznie kell a Soft delete és a kiürítési védelmet. A helyreállítható törlés biztosítja, hogy a Key Vault a megadott megőrzési időtartam (90 nap alapértelmezett értéke) törölt kulcsát tárolja. A védelem kiürítése biztosítja, hogy a törölt kulcsok ne legyenek véglegesen törölve, amíg a megőrzési időszak megszűnik. Ezek a beállítások a véletlen törlés miatt védik az adatok elvesztését. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához.

    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Hozzon létre egy DiskEncryptionSet egy példányát. 
    
    ```powershell
    $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned

    $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
    ```

1.  Adja meg a DiskEncryptionSet-erőforrás hozzáférését a kulcstartóhoz.

    > [!NOTE]
    > Eltarthat néhány percig, amíg az Azure létrehozza a DiskEncryptionSet identitását a Azure Active Directoryban. Ha a következő parancs futtatásakor a "nem találja a Active Directory objektumot" hibaüzenet jelenik meg, várjon néhány percet, és próbálkozzon újra.
    
    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
     
    New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuális gép létrehozása Piactéri rendszerkép használatával, az operációs rendszer és az adatlemezek titkosítása az ügyfél által felügyelt kulcsokkal

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítással titkosított üres lemez létrehozása, amely egy virtuális géphez csatolható

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>Meglévő, nem csatlakoztatott felügyelt lemezek titkosítása 

A meglévő lemezeket nem lehet egy futó virtuális géphez csatolni ahhoz, hogy a következő parancsfájl használatával titkosítani lehessen őket:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuálisgép-méretezési csoport létrehozása Piactéri rendszerkép használatával, az operációs rendszer és az adatlemezek titkosítása az ügyfél által felügyelt kulcsokkal

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kiszolgálóoldali titkosítás és az Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) kihasználja a Windows [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját, hogy a felügyelt lemezeket a vendég virtuális gépen lévő, az ügyfél által felügyelt kulcsokkal titkosítsa.  Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

## <a name="next-steps"></a>További lépések

- [Az ügyfél által felügyelt kulcsokkal rendelkező titkosított lemezek létrehozásához Azure Resource Manager-sablonok megismerése](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Gépek replikálása az ügyfél által felügyelt kulcsokkal rendelkező lemezekkel](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [A VMware virtuális gépek vész-helyreállításának beállítása az Azure-ba a PowerShell használatával](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Az Azure-ba irányuló vész-helyreállítás beállítása a Hyper-V virtuális gépekhez a PowerShell és a Azure Resource Manager használatával](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
