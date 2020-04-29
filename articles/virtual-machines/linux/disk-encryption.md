---
title: Az Azure Managed Disks kiszolgálóoldali titkosítása – Azure parancssori felület
description: Az Azure Storage védi az adatait úgy, hogy titkosítja a nyugalmát, mielőtt megőrzi azt a Storage-fürtökön. A felügyelt lemezek titkosításához a Microsoft által felügyelt kulcsokat használhatja, vagy az ügyfél által felügyelt kulcsokkal kezelheti a titkosítást a saját kulcsaival.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 027efd268ee80fbaf921b42d09cc424c8e8483ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136923"
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

Az egyes felügyelt lemezek szintjén is kezelheti a titkosítást a saját kulcsaival. Az ügyfél által felügyelt kulcsokkal rendelkező felügyelt lemezek kiszolgálóoldali titkosítása integrált élményt nyújt a Azure Key Vault. Importálhatja az [RSA-kulcsokat](../../key-vault/keys/hsm-protected-keys.md) a Key Vault, vagy LÉTREHOZHAT új RSA-kulcsokat Azure Key Vault. 

Az Azure Managed Disks a titkosítást és a visszafejtést teljes mértékben átlátható módon kezeli a [boríték-titkosítás](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)használatával. Titkosítja az adatait egy [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-alapú adattitkosítási kulccsal (adattitkosítási kulcsot), amely viszont a kulcsok használatával védett. A Storage szolgáltatás adattitkosítási kulcsokat hoz létre, és az ügyfél által felügyelt kulcsokkal titkosítja azokat az RSA encryption használatával. A borítékok titkosítása lehetővé teszi a kulcsok rendszeres elforgatását a megfelelőségi szabályzatok szerint, a virtuális gépek befolyásolása nélkül. A kulcsok elforgatásakor a Storage szolgáltatás újra titkosítja az adattitkosítási kulcsokat az új ügyfél által felügyelt kulcsokkal. 

Hozzáférést kell biztosítania a Key Vault felügyelt lemezéhez, hogy a kulcsokat a ADATTITKOSÍTÁSI kulcsot titkosítására és visszafejtésére használja. Ez lehetővé teszi, hogy teljes körűen vezérelje adatait és kulcsait. Bármikor letilthatja a kulcsokat, vagy visszavonhatja a hozzáférést a felügyelt lemezekhez. A titkosítási kulcs használatát Azure Key Vault figyeléssel is naplózhatja, így biztosítva, hogy csak a felügyelt lemezek vagy más megbízható Azure-szolgáltatások férhessenek hozzá a kulcsokhoz.

Prémium SSD-k, standard SSD-k és standard HDD-k esetén: Ha letiltja vagy törli a kulcsot, a kulcsot használó virtuális gépek automatikusan le lesznek állítva. Ezt követően a virtuális gépek nem lesznek használhatók, kivéve, ha a kulcs ismét engedélyezve van, vagy új kulcsot rendel hozzá.

Az ultra lemezek esetében a kulcs letiltásával vagy törlésével a kulcsot használó, ultra lemezzel rendelkező virtuális gépek nem állnak le automatikusan. Ha felszabadítja és újraindítja a virtuális gépeket, akkor a lemezek nem fogják tudni használni a kulcsot, és a virtuális gépek nem fognak ismét online állapotba állni. Ahhoz, hogy a virtuális gépek ismét online állapotba kerüljenek, hozzá kell rendelnie egy új kulcsot, vagy engedélyeznie kell a meglévő kulcsot.

A következő ábra azt mutatja be, hogy a felügyelt lemezek hogyan használják a Azure Active Directory és Azure Key Vault a kérelmeket az ügyfél által felügyelt kulcs használatával:

![Felügyelt lemez és ügyfél által felügyelt kulcsok munkafolyamata. A rendszergazda létrehoz egy Azure Key Vault, majd létrehoz egy lemezes titkosítási készletet, és beállítja a lemez titkosítási készletét. A készlet egy virtuális géphez van társítva, amely lehetővé teszi, hogy a lemez használhassa az Azure AD-t a hitelesítéshez](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


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

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsokra a következő korlátozások vonatkoznak:

- Ha ez a funkció engedélyezve van a lemezen, nem tiltható le.
    Ha ennek megkerülésére van szüksége, az összes olyan felügyelt lemezre [át kell másolnia az összes](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) olyan felügyelt lemezt, amely nem használja az ügyfél által felügyelt kulcsokat.
- Csak a 2048 méretű ["Soft" és "Hard" RSA-kulcsok](../../key-vault/keys/about-keys.md) támogatottak, nincsenek más kulcsok vagy méretek.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított egyéni rendszerképekből létrehozott lemezeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani, és ugyanahhoz az előfizetéshez kell tartoznia.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított lemezekről létrehozott pillanatképeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított egyéni lemezképek nem használhatók a megosztott lemezképek gyűjteményében.
- Az ügyfél által felügyelt kulcsokhoz (Azure Key Vaultok, lemez titkosítási készletek, virtuális gépek, lemezek és Pillanatképek) kapcsolódó összes erőforrásnak ugyanabban az előfizetésben és régióban kell lennie.
- Az ügyfél által felügyelt kulcsokkal titkosított lemezek, Pillanatképek és lemezképek nem helyezhetők át másik előfizetésbe.
- Ha a Azure Portal használatával hozza létre a lemez titkosítási készletét, a pillanatképek jelenleg nem használhatók.
- Az ügyfél által felügyelt kulcsokkal titkosított felügyelt lemezek nem titkosíthatók Azure Disk Encryptionval.

### <a name="cli"></a>parancssori felület
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>A Azure Key Vault és a DiskEncryptionSet beállítása

1. Győződjön meg arról, hogy telepítette a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és bejelentkezett egy Azure-fiókba az az [login](/cli/azure/reference-index)paranccsal.

1. Hozzon létre egy Azure Key Vault és egy titkosítási kulcs egy példányát.

    A Key Vault-példány létrehozásakor engedélyeznie kell a Soft delete és a kiürítési védelmet. A helyreállítható törlés biztosítja, hogy a Key Vault a megadott megőrzési időtartam (90 nap alapértelmezett értéke) törölt kulcsát tárolja. A védelem kiürítése biztosítja, hogy a törölt kulcsok ne legyenek véglegesen törölve, amíg a megőrzési időszak megszűnik. Ezek a beállítások a véletlen törlés miatt védik az adatok elvesztését. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához.

    > [!IMPORTANT]
    > Ha ezt a lehetőséget választja, nem kell a Camel-esetet megadnia, ha további lemezeket rendel hozzá az erőforráshoz a Azure Portal.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Hozzon létre egy DiskEncryptionSet egy példányát. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Adja meg a DiskEncryptionSet-erőforrás hozzáférését a kulcstartóhoz. 

        > [!NOTE]
        > Eltarthat néhány percig, amíg az Azure létrehozza a DiskEncryptionSet identitását a Azure Active Directoryban. Ha a következő parancs futtatásakor a "nem találja a Active Directory objektumot" hibaüzenet jelenik meg, várjon néhány percet, és próbálkozzon újra.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuális gép létrehozása Piactéri rendszerkép használatával, az operációs rendszer és az adatlemezek titkosítása az ügyfél által felügyelt kulcsokkal

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-managed-disks"></a>Meglévő felügyelt lemezek titkosítása 

A meglévő lemezeket nem lehet egy futó virtuális géphez csatolni ahhoz, hogy a következő parancsfájl használatával titkosítani lehessen őket:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuálisgép-méretezési csoport létrehozása Piactéri rendszerkép használatával, az operációs rendszer és az adatlemezek titkosítása az ügyfél által felügyelt kulcsokkal

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítással titkosított üres lemez létrehozása, amely egy virtuális géphez csatolható

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>DiskEncryptionSet kulcsának módosítása a DiskEncryptionSet hivatkozó összes erőforrás kulcsának elforgatásához

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Lemez kiszolgálóoldali titkosításának állapotának megkeresése

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kiszolgálóoldali titkosítás és az Azure Disk Encryption

A [virtuális gépek és a virtuálisgép-méretezési csoportok Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) kihasználja a Windows [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját, hogy a felügyelt lemezeket a vendég virtuális gépen lévő ügyfél által felügyelt kulcsokkal titkosítsa.  Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

## <a name="next-steps"></a>További lépések

- [Az ügyfél által felügyelt kulcsokkal rendelkező titkosított lemezek létrehozásához Azure Resource Manager-sablonok megismerése](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
- [Gépek replikálása az ügyfél által felügyelt kulcsokkal rendelkező lemezekkel](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [A VMware virtuális gépek vész-helyreállításának beállítása az Azure-ba a PowerShell használatával](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Az Azure-ba irányuló vész-helyreállítás beállítása a Hyper-V virtuális gépekhez a PowerShell és a Azure Resource Manager használatával](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
