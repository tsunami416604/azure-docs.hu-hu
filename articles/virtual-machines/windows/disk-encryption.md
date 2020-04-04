---
title: Az Azure felügyelt lemezeinek kiszolgálóoldali titkosítása – PowerShell
description: Az Azure Storage védi az adatokat az inaktív titkosítással, mielőtt a Storage-fürtök. A felügyelt lemezek titkosításához a Microsoft által felügyelt kulcsokra támaszkodhat, vagy az ügyfél által felügyelt kulcsok segítségével kezelheti a titkosítást a saját kulcsaival.
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: d8e6f4b58cf17a7502e2c0d67e4fa67af7cdb3f5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632947"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Az Azure által kezelt lemezek kiszolgálóoldali titkosítása

Az Azure által kezelt lemezek alapértelmezés szerint automatikusan titkosítják az adatokat, amikor a felhőben megőrzik azokat. A kiszolgálóoldali titkosítás védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az Azure-ban kezelt lemezeken lévő adatok transzparens módon vannak titkosítva 256 bites [AES titkosítással,](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)amely az egyik legerősebb rendelkezésre álló blokktitkosítás, és fips 140-2-kompatibilis.

A titkosítás nincs hatással a felügyelt lemezek teljesítményére. A titkosításnak nincs további költsége.

Az Azure által felügyelt lemezek alapjául szolgáló kriptográfiai modulokról a [Kriptográfiai API: Következő generáció című](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) témakörben talál további információt.

## <a name="about-encryption-key-management"></a>Titkosítási kulcs kezelése –

A felügyelt lemez titkosításához platformáltal kezelt kulcsokra támaszkodhat, vagy saját kulcsokkal kezelheti a titkosítást. Ha úgy dönt, hogy a titkosítást saját kulcsaival kezeli, megadhatja az *ügyfél által felügyelt kulcsot,* amelyet a felügyelt lemezek összes adatának titkosításához és visszafejtéséhez használhat. 

A következő szakaszok részletesebben ismertetik a kulcskezelés egyes lehetőségeit.

## <a name="platform-managed-keys"></a>Platform által kezelt kulcsok

Alapértelmezés szerint a felügyelt lemezek platformáltal felügyelt titkosítási kulcsokat használnak. 2017. június 10-ig minden új felügyelt lemez, pillanatkép, lemezkép és új, meglévő felügyelt lemezekre írt adat automatikusan titkosítva lesz a platform által felügyelt kulcsokkal.

## <a name="customer-managed-keys"></a>Felhasználó által kezelt kulcsok

A titkosítást kezelheti az egyes felügyelt lemezek szintjén, a saját kulcsaival. Az ügyfél által felügyelt kulcsokkal rendelkező felügyelt lemezek kiszolgálóoldali titkosítása integrált élményt nyújt az Azure Key Vault szolgáltatással. Importálhatja [az RSA-kulcsokat a](../../key-vault/key-vault-hsm-protected-keys.md) Key Vaultba, vagy új RSA-kulcsokat hozhat létre az Azure Key Vaultban. 

Az Azure által kezelt lemezek a titkosítást és a visszafejtést teljes mértékben átlátható módon kezelik [a borítéktitkosítás használatával.](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Az adatokat [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 alapú adattitkosítási kulccsal (DEK) titkosítja, amely viszont a kulcsokkal védett. A Storage szolgáltatás adattitkosítási kulcsokat hoz létre, és rsa titkosítással titkosítja azokat az ügyfél által felügyelt kulcsokkal. A borítéktitkosítás lehetővé teszi a kulcsok rendszeres elforgatását (módosítását) a megfelelőségi szabályzatok szerint anélkül, hogy ez befolyásolna a virtuális gépeket. A kulcsok elforgatásakor a Storage szolgáltatás újra titkosítja az adattitkosítási kulcsokat az új ügyfél által felügyelt kulcsokkal. 

A dek titkosításához és visszafejtéséhez hozzáférést kell biztosítania a key vaultban lévő felügyelt lemezekhez. Ez lehetővé teszi az adatok és kulcsok teljes körű vezérlését. Bármikor letilthatja a kulcsokat, vagy visszavonhatja a felügyelt lemezekhez való hozzáférést. A titkosítási kulcs használatát az Azure Key Vault figyelésével is naplózhatja, így biztosíthatja, hogy csak felügyelt lemezek vagy más megbízható Azure-szolgáltatások férnek hozzá a kulcsokhoz.

Prémium szintű SSD-k, szabványos SSD-k és szabványos HDD-k esetén: Ha letiltja vagy törli a kulcsot, az adott kulccsal rendelkező virtuális gépek automatikusan leállnak. Ezt követően a virtuális gépek nem lesz használható, kivéve, ha a kulcs újra engedélyezve van, vagy hozzá egy új kulcsot.

Az ultralemezek esetében, ha letiltja vagy törli a kulcsot, a kulccsal ultralemezzel rendelkező virtuális gépek nem állnak le automatikusan. Miután felszabadítja, és indítsa újra a virtuális gépeket, majd a lemezek leáll a kulcs használata, és majd a virtuális gépek nem jön vissza online. A virtuális gépek online állapotba hozásához új kulcsot kell hozzárendelnie, vagy engedélyeznie kell a meglévő kulcsot.

Az alábbi ábra bemutatja, hogy a felügyelt lemezek hogyan használják az Azure Active Directoryt és az Azure Key Vaultot az ügyfél által felügyelt kulcs használatával történő kéréskérésekhez:

![Felügyelt lemez- és ügyféláltal kezelt kulcsok munkafolyamata. Egy rendszergazda létrehoz egy Azure Key Vault, majd létrehoz egy lemeztitkosítási készletet, és beállítja a lemeztitkosítási készlet. A készlet egy virtuális géphez van társítva, amely lehetővé teszi, hogy a lemez az Azure AD-t használja a hitelesítéshez](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Az alábbi lista részletesebben ismerteti a diagramot:

1. Az Azure Key Vault rendszergazdája key vault-erőforrásokat hoz létre.
1. A key vault rendszergazdája vagy importálja az RSA-kulcsokat a Key Vaultba, vagy új RSA-kulcsokat hoz létre a Key Vaultban.
1. Ez a rendszergazda létrehoz egy példányt a lemeztitkosítási készlet erőforrás, megadva egy Azure Key Vault-azonosító t és egy kulcs URL-címet. A lemeztitkosítási készlet egy új erőforrás, amelya felügyelt lemezek kulcskezelésének egyszerűsítésére szolgáló új erőforrás. 
1. Lemeztitkosítási készlet létrehozásakor létrejön egy [rendszeráltal hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) az Azure Active Directoryban (AD), és a lemeztitkosítási készlethez társítva. 
1. Az Azure key vault rendszergazdája majd megadja a felügyelt identitás engedélyt a key vaultban műveletek végrehajtásához.
1. A virtuális gép felhasználó lemezeket hoz létre a lemeztitkosítási készlettel társítva. A virtuális gép felhasználó is engedélyezheti a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokkal a meglévő erőforrásokhoz, társítva őket a lemeztitkosítási készlettel. 
1. A felügyelt lemezek a felügyelt identitás használatával küldési kérelmeket az Azure Key Vault.
1. Az adatok olvasása vagy írása, felügyelt lemezek küld kéréseket az Azure Key Vault titkosítására (wrap) és visszacsomagolja (csomagolja ki) az adattitkosítási kulcs végrehajtása érdekében az adatok titkosítása és visszafejtése érdekében. 

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásáról az [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) és [az Azure Key Vault CLI.](https://docs.microsoft.com/cli/azure/keyvault) A hozzáférés visszavonása hatékonyan blokkolja a hozzáférést a tárfiókban lévő összes adathoz, mivel a titkosítási kulcs nem érhető el az Azure Storage számára.

### <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsok a következő korlátozásokkal rendelkeznek:

- Ha ez a szolgáltatás engedélyezve van a lemezen, nem lehet letiltani.
    Ha meg kell kerülnie ezt, az összes adatot át kell [másolnia](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) egy teljesen más felügyelt lemezre, amely nem ügyfél által felügyelt kulcsokat használ.
- Csak a 2080 méretű ["puha" és "kemény" RSA-kulcsok](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) támogatottak, más billentyűk és méretek nem támogatottak.
- A kiszolgálóoldali titkosítással és ügyfél által kezelt kulcsokkal titkosított egyéni lemezképekből létrehozott lemezeket ugyanazzal az ügyfél által felügyelt kulccsal kell titkosítani, és ugyanabban az előfizetésben kell lenniük.
- A kiszolgálóoldali titkosítással és ügyféláltal kezelt kulcsokkal titkosított lemezekről létrehozott pillanatképeket ugyanazzal az ügyfél által kezelt kulccsal kell titkosítani.
- A kiszolgálóoldali titkosítással és az ügyfél által kezelt kulcsokkal titkosított egyéni lemezképek nem használhatók a megosztott képtárban.
- Az ügyfél által felügyelt kulcsokhoz kapcsolódó összes erőforrásnak (Azure Key Vaults, lemeztitkosítási készletek, virtuális gépek, lemezek és pillanatképek) ugyanabban az előfizetésben és régióban kell lennie.
- Az ügyfél által felügyelt kulccsal titkosított lemezek, pillanatképek és képek nem helyezhető át egy másik előfizetésre.
- Ha az Azure Portalon a lemeztitkosítási készlet létrehozásához, nem használhatja a pillanatképek egyelőre.
- Az ügyfél által kezelt kulcsokkal titkosított felügyelt lemezek nem titkosíthatók az Azure Disk Encryption segítségével is.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Az Azure Key Vault és a DiskEncryptionSet beállítása

1. Győződjön meg arról, hogy telepítette a legújabb [Azure PowerShell-verziót,](/powershell/azure/install-az-ps)és be van jelentkezve egy Azure-fiókba a Connect-AzAccount szolgáltatással

1. Hozzon létre egy példányt az Azure Key Vault és a titkosítási kulcs.

    A Key Vault-példány létrehozásakor engedélyeznie kell a helyreállítható törlést és a védelem kiürítését. A helyreállítható törlés biztosítja, hogy a Key Vault egy adott megőrzési időszakban törölt kulcsot tartalmaz (90 napos alapértelmezett). A kiürítési védelem biztosítja, hogy a törölt kulcs nem törölhető véglegesen, amíg a megőrzési időszak le nem áll. Ezek a beállítások megvédik önt a véletlen törlés miatti adatvesztéstől. Ezek a beállítások kötelezőek, ha a felügyelt lemezek titkosításához key vaultot használ.

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

1.    DiskEncryptionSet példányának létrehozása. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Adja meg a DiskEncryptionSet erőforrás-hozzáférést a key vaulthoz.

        > [!NOTE]
        > Az Azure-nak néhány percig is eltarthat, amíg létrehozza a DiskEncryptionSet identitását az Azure Active Directoryban. Ha a következő parancs futtatásakor a következő parancs futtatásakor a következő hibaüzenet jelenik meg, várjon néhány percet, majd próbálkozzon újra.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuális gép létrehozása Piactér-lemezkép használatával, az operációs rendszer és az adatlemezek titkosítása ügyfél által kezelt kulcsokkal

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Kiszolgálóoldali titkosítással titkosított üres lemez létrehozása ügyfél által kezelt kulcsokkal, és csatolása virtuális géphez

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

A meglévő lemezeknem csatolhatók egy futó virtuális géphez, hogy titkosíthassa őket a következő parancsfájl használatával:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuálisgép-méretezési csoport létrehozása Piactér-lemezkép használatával, az operációs rendszer és az adatlemezek titkosítása ügyfél által kezelt kulcsokkal

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

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>A DiskEncryptionSet kulcsának módosítása a DiskEncryptionSet-re hivatkozó összes erőforrás kulcsának elforgatásához

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Lemez kiszolgálóoldali titkosítási állapotának megkeresése

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure Active Directory (Azure AD) egyik szolgáltatásának felügyelt identitásaira támaszkodnak. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel idáig az erőforrásokhoz a feldolgozási jog alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt az egyik Azure AD-könyvtárból egy másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok már nem működnek. További információ: [Előfizetés átvitele az Azure AD-könyvtárak között.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure Active Directory (Azure AD) egyik szolgáltatásának felügyelt identitásaira támaszkodnak. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel idáig az erőforrásokhoz a feldolgozási jog alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt az egyik Azure AD-könyvtárból egy másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok már nem működnek. További információ: [Előfizetés átvitele az Azure AD-könyvtárak között.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kiszolgálóoldali titkosítás és az Azure lemeztitkosítás

[Az Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) a Windows [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) szolgáltatását és a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatását használja a felügyelt lemezek titkosításához a vendég virtuális gépügyfél által felügyelt kulcsokkal.  Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t azáltal, hogy lehetővé teszi a virtuális gépek operációsrendszer-típusainak és lemezképeinek használatát a Storage szolgáltatásban lévő adatok titkosításával.

## <a name="next-steps"></a>További lépések

- [Fedezze fel az Azure Resource Manager sablonjait titkosított lemezek ügyfél által kezelt kulcsokkal való létrehozásához](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Gépek replikálása ügyfél által kezelt kulcsokáltal engedélyezett lemezekkel](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [A VMware virtuális gépek vészutáni helyreállításának beállítása az Azure-ba a PowerShell segítségével](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Vészhelyreállítás beállítása az Azure-ba a PowerShell és az Azure Resource Manager használatával az Azure-ban a Virtuális-gépekkel](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
