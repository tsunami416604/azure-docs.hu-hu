---
title: Az Azure felügyelt lemezeinek kiszolgálóoldali titkosítása – Azure CLI
description: Az Azure Storage védi az adatokat az inaktív titkosítással, mielőtt a Storage-fürtök. A felügyelt lemezek titkosításához a Microsoft által felügyelt kulcsokra támaszkodhat, vagy az ügyfél által felügyelt kulcsok segítségével kezelheti a titkosítást a saját kulcsaival.
author: roygara
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 88d25083a1105023279f3907a4573319fabe087c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520775"
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

A titkosítást kezelheti az egyes felügyelt lemezek szintjén, a saját kulcsaival. Az ügyfél által felügyelt kulcsokkal rendelkező felügyelt lemezek kiszolgálóoldali titkosítása integrált élményt nyújt az Azure Key Vault szolgáltatással. Importálhatja [az RSA-kulcsokat a](../../key-vault/key-vault-hsm-protected-keys.md) Key Vaultba, vagy új RSA-kulcsokat hozhat létre az Azure Key Vaultban. Az Azure által kezelt lemezek a titkosítást és a visszafejtést teljes mértékben átlátható módon kezelik [a borítéktitkosítás használatával.](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Az adatokat [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 alapú adattitkosítási kulccsal (DEK) titkosítja, amely viszont a kulcsokkal védett. A dek titkosításához és visszafejtéséhez hozzáférést kell biztosítania a key vaultban lévő felügyelt lemezekhez. Ez lehetővé teszi az adatok és kulcsok teljes körű vezérlését. Bármikor letilthatja a kulcsokat, vagy visszavonhatja a felügyelt lemezekhez való hozzáférést. A titkosítási kulcs használatát az Azure Key Vault figyelésével is naplózhatja, így biztosíthatja, hogy csak felügyelt lemezek vagy más megbízható Azure-szolgáltatások férnek hozzá a kulcsokhoz.

Prémium szintű SSD-k, szabványos SSD-k és szabványos HDD-k esetén: Ha letiltja vagy törli a kulcsot, az adott kulccsal rendelkező virtuális gépek automatikusan leállnak. Ezt követően a virtuális gépek nem lesz használható, kivéve, ha a kulcs újra engedélyezve van, vagy hozzá egy új kulcsot.

Az ultralemezek esetében, ha letiltja vagy törli a kulcsot, a kulccsal ultralemezzel rendelkező virtuális gépek nem állnak le automatikusan. Miután felszabadítja, és indítsa újra a virtuális gépeket, majd a lemezek leáll a kulcs használata, és majd a virtuális gépek nem jön vissza online. A virtuális gépek online állapotba hozásához új kulcsot kell hozzárendelnie, vagy engedélyeznie kell a meglévő kulcsot.

Az alábbi ábra bemutatja, hogy a felügyelt lemezek hogyan használják az Azure Active Directoryt és az Azure Key Vaultot az ügyfél által felügyelt kulcs használatával történő kéréskérésekhez:

![Felügyelt lemez- és ügyféláltal kezelt kulcsok munkafolyamata. Egy rendszergazda létrehoz egy Azure Key Vault, majd létrehoz egy lemeztitkosítási készletet, és beállítja a lemeztitkosítási készlet. A készlet egy virtuális géphez van társítva, amely lehetővé teszi, hogy a lemez az Azure AD-t használja a hitelesítéshez](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Az alábbi lista még részletesebben ismerteti a diagramot:

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
    Ha meg kell kerülnie ezt, az összes adatot át kell [másolnia](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) egy teljesen más felügyelt lemezre, amely nem ügyfél által felügyelt kulcsokat használ.
- Csak a 2048 méretű ["puha" és "kemény" RSA-kulcsok](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) támogatottak, más billentyűk és méretek nem támogatottak.
- A kiszolgálóoldali titkosítással és ügyfél által kezelt kulcsokkal titkosított egyéni lemezképekből létrehozott lemezeket ugyanazzal az ügyfél által felügyelt kulccsal kell titkosítani, és ugyanabban az előfizetésben kell lenniük.
- A kiszolgálóoldali titkosítással és ügyféláltal kezelt kulcsokkal titkosított lemezekről létrehozott pillanatképeket ugyanazzal az ügyfél által kezelt kulccsal kell titkosítani.
- A kiszolgálóoldali titkosítással és az ügyfél által kezelt kulcsokkal titkosított egyéni lemezképek nem használhatók a megosztott képtárban.
- Az ügyfél által felügyelt kulcsokhoz kapcsolódó összes erőforrásnak (Azure Key Vaults, lemeztitkosítási készletek, virtuális gépek, lemezek és pillanatképek) ugyanabban az előfizetésben és régióban kell lennie.
- Az ügyfél által felügyelt kulccsal titkosított lemezek, pillanatképek és képek nem helyezhető át egy másik előfizetésre.
- Ha az Azure Portalon a lemeztitkosítási készlet létrehozásához, nem használhatja a pillanatképek egyelőre.
- Az ügyfél által kezelt kulcsokkal titkosított felügyelt lemezek nem titkosíthatók az Azure Disk Encryption segítségével is.

### <a name="cli"></a>parancssori felület
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Az Azure Key Vault és a DiskEncryptionSet beállítása

1. Győződjön meg arról, hogy telepítette a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és bejelentkezett egy Azure-fiókba [az az bejelentkezéssel.](/cli/azure/reference-index)

1. Hozzon létre egy példányt az Azure Key Vault és a titkosítási kulcs.

    A Key Vault-példány létrehozásakor engedélyeznie kell a helyreállítható törlést és a védelem kiürítését. A helyreállítható törlés biztosítja, hogy a Key Vault egy adott megőrzési időszakban törölt kulcsot tartalmaz (90 napos alapértelmezett). A kiürítési védelem biztosítja, hogy a törölt kulcs nem törölhető véglegesen, amíg a megőrzési időszak le nem áll. Ezek a beállítások megvédik önt a véletlen törlés miatti adatvesztéstől. Ezek a beállítások kötelezőek, ha a felügyelt lemezek titkosításához key vaultot használ.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=WestCentralUS
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    DiskEncryptionSet példányának létrehozása. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Adja meg a DiskEncryptionSet erőforrás-hozzáférést a key vaulthoz. 

        > [!NOTE]
        > Az Azure-nak néhány percig is eltarthat, amíg létrehozza a DiskEncryptionSet identitását az Azure Active Directoryban. Ha a következő parancs futtatásakor a következő parancs futtatásakor a következő hibaüzenet jelenik meg, várjon néhány percet, majd próbálkozzon újra.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuális gép létrehozása Piactér-lemezkép használatával, az operációs rendszer és az adatlemezek titkosítása ügyfél által kezelt kulcsokkal

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>Meglévő, nem csatlakoztatott felügyelt lemezek titkosítása 

A meglévő lemezeknem csatolhatók egy futó virtuális géphez, hogy titkosíthassa őket a következő parancsfájl használatával:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Virtuálisgép-méretezési csoport létrehozása Piactér-lemezkép használatával, az operációs rendszer és az adatlemezek titkosítása ügyfél által kezelt kulcsokkal

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=WestCentralUS
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Kiszolgálóoldali titkosítással titkosított üres lemez létrehozása ügyfél által kezelt kulcsokkal, és csatolása virtuális géphez

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=WestCentralUS
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure Active Directory (Azure AD) egyik szolgáltatásának felügyelt identitásaira támaszkodnak. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel idáig az erőforrásokhoz a feldolgozási jog alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt az egyik Azure AD-könyvtárból egy másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok már nem működnek. További információ: [Előfizetés átvitele az Azure AD-könyvtárak között.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure Active Directory (Azure AD) egyik szolgáltatásának felügyelt identitásaira támaszkodnak. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel idáig az erőforrásokhoz a feldolgozási jog alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt az egyik Azure AD-könyvtárból egy másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok már nem működnek. További információ: [Előfizetés átvitele az Azure AD-könyvtárak között.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kiszolgálóoldali titkosítás és az Azure lemeztitkosítás

[Az Azure Disk Encryption a virtuális gépek és](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) a virtuális gép méretezési készletek használja a [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) szolgáltatás a Windows és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkció a Linux titkosítja a felügyelt lemezeket az ügyfél által felügyelt kulcsok a vendég virtuális gépen belül.  Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t azáltal, hogy lehetővé teszi a virtuális gépek operációsrendszer-típusainak és lemezképeinek használatát a Storage szolgáltatásban lévő adatok titkosításával.

## <a name="next-steps"></a>További lépések

- [Fedezze fel az Azure Resource Manager sablonjait titkosított lemezek ügyfél által kezelt kulcsokkal való létrehozásához](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Gépek replikálása ügyfél által kezelt kulcsokáltal engedélyezett lemezekkel](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [A VMware virtuális gépek vészutáni helyreállításának beállítása az Azure-ba a PowerShell segítségével](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Vészhelyreállítás beállítása az Azure-ba a PowerShell és az Azure Resource Manager használatával az Azure-ban a Virtuális-gépekkel](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
