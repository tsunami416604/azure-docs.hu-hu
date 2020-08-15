---
title: Előnézet – a saját kulcsaival titkosított rendszerkép-verzió létrehozása
description: Hozzon létre egy rendszerkép-verziót egy megosztott rendszerkép-gyűjteményben az ügyfél által felügyelt titkosítási kulcsok használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/11/2020
ms.author: cynthn
ms.openlocfilehash: 0d2b840b401dc90b332f91c93a9eda03d6643432
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245553"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Előzetes verzió: az ügyfél által felügyelt kulcsok használata a rendszerképek titkosításához

A katalógus lemezképeit felügyelt lemezként tárolja a rendszer, így azok automatikusan titkosítva lesznek a kiszolgálóoldali titkosítással. A kiszolgálóoldali titkosítás 256 bites [AES-titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ, amely az egyik legerősebb blokk titkosítási algoritmus, és az FIPS 140-2-kompatibilis. Az Azure Managed Disks mögöttes titkosítási modulokkal kapcsolatos további információkért lásd: a [kriptográfiai API: következő generáció](/windows/desktop/seccng/cng-portal)

Használhatja a platform által felügyelt kulcsokat a lemezképek titkosításához, saját kulcsokat használhat, vagy mindkettőt használhatja dupla titkosításhoz. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a lemezképek összes lemezének titkosítására és visszafejtésére használhat. 

Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás Azure Key Vaultt használ. Importálhatja az [RSA-kulcsokat](../key-vault/keys/hsm-protected-keys.md) a Key Vault, vagy LÉTREHOZHAT új RSA-kulcsokat Azure Key Vault.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez már van szükség a lemezképhez használni kívánt lemezes titkosításra.

- Ha csak az ügyfél által felügyelt kulcsot szeretné használni, tekintse meg az **ügyfél által felügyelt kulcsok engedélyezése kiszolgálóoldali titkosítással** a [Azure Portal](./windows/disks-enable-customer-managed-keys-portal.md) vagy a [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset)használatával című témakört.

- Ha a platform által felügyelt és az ügyfél által felügyelt kulcsokat is szeretné használni (dupla titkosításhoz), tekintse meg a következőt: a [Azure Portal](./windows/disks-enable-double-encryption-at-rest-portal.md) vagy a [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md)használatával **engedélyezze a kettős titkosítás** használatát.
    > [!IMPORTANT]
    > Ezt a hivatkozást kell használnia [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) a Azure Portal eléréséhez. A kettős titkosítás jelenleg nem látható a nyilvános Azure Portal a hivatkozás használata nélkül.

## <a name="limitations"></a>Korlátozások

Az ügyfél által felügyelt kulcsok használata több korlátozást is igénybe vesz a megosztott képgyűjteményi rendszerképek titkosításához:  

- A titkosítási kulcsokhoz tartozó készleteknek ugyanabban az előfizetésben és régióban kell lenniük, mint a rendszerképnek.

- Az ügyfél által felügyelt kulcsokat használó lemezképek nem oszthatók meg. 

- Ügyfelek által felügyelt kulcsokat használó rendszerképeket nem lehet más régiókba replikálni.

- Ha a saját kulcsait használta egy lemez vagy kép titkosításához, nem térhet vissza a platform által felügyelt kulcsok használatára a lemezek és a képek titkosításához.


> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsokkal történő titkosítás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

A nyilvános előzetes verzióhoz először regisztrálnia kell a szolgáltatást.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

A regisztráció elvégzése néhány percet vesz igénybe. A Get-AzProviderFeature használatával keresse meg a szolgáltatás regisztrációjának állapotát.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Ha a RegistrationState visszaadja a regisztrációt, átléphet a következő lépésre.

Győződjön meg arról, hogy a szolgáltató regisztrálva van. Győződjön meg róla, hogy visszaadja `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Ha nem ad vissza `Registered` , a következő paranccsal regisztrálhatók a szolgáltatók:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Ha meg szeretné adni egy lemez titkosítását a lemezkép verziójához, használja a  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) `-TargetRegion` paramétert. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @($region1)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Létrehozhat egy virtuális gépet egy megosztott rendszerkép-gyűjteményből, és az ügyfél által felügyelt kulcsokkal titkosíthatja a lemezeket. A szintaxis ugyanaz, mint egy [általánosított](vm-generalized-image-version-powershell.md) vagy [speciális](vm-specialized-image-version-powershell.md) virtuális gép rendszerképből való létrehozásához, a kiterjesztett paramétert kell használnia, és hozzá `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` kell adnia a virtuális gép konfigurációjához.

Adatlemezek esetén hozzá kell adnia a `-DiskEncryptionSetId $setID` paramétert az [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)használatakor.


## <a name="cli"></a>parancssori felület 

A nyilvános előzetes verzióhoz először regisztrálnia kell a szolgáltatást.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

A szolgáltatás regisztrációjának állapotát vizsgálja meg.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

A visszatérés `"state": "Registered"` után a következő lépésre léphet.

Győződjön meg a regisztrációról.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Ha a regisztráció nem jelent meg, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


A lemezkép-verzióhoz beállított lemezes titkosítás megadásához használja az az  [rendszerkép-katalógus Create-lemezkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create) `--target-region-encryption` paramétert. A formátuma az `--target-region-encryption` operációs rendszer és az adatlemezek titkosítására szolgáló kulcsok szóközzel tagolt listája. A következőhöz hasonlóan kell kinéznie: `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>` . 

Ha az operációsrendszer-lemez forrása felügyelt lemez vagy virtuális gép, a használatával `--managed-image` adhatja meg a lemezkép verziójának forrását. Ebben a példában a forrás egy olyan felügyelt lemezkép, amely egy operációsrendszer-lemezzel, valamint egy adatlemezzel rendelkezik a 0. logikai egységben. Az operációsrendszer-lemez titkosítva lesz a DiskEncryptionSet1, és az adatlemez titkosítva lesz a DiskEncryptionSet2 szolgáltatással.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Ha az operációsrendszer-lemez forrása pillanatkép, `--os-snapshot` az operációsrendszer-lemez megadásához használja a következőt:. Ha vannak olyan adatlemez-Pillanatképek, amelyeknek a lemezkép verziójának is szerepelniük kell, adja hozzá ezeket a használatával `--data-snapshot-luns` a LUN megadásához és `--data-snapshots` a pillanatképek megadásához.

Ebben a példában a források lemezes Pillanatképek. Létezik egy operációsrendszer-lemez, valamint egy adatlemez a (z) 0 LUN-on. Az operációsrendszer-lemez titkosítva lesz a DiskEncryptionSet1, és az adatlemez titkosítva lesz a DiskEncryptionSet2 szolgáltatással.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>A virtuális gép létrehozása

Létrehozhat egy virtuális gépet egy megosztott rendszerkép-gyűjteményből, és az ügyfél által felügyelt kulcsokkal titkosíthatja a lemezeket. A szintaxis megegyezik egy [általánosított](vm-generalized-image-version-cli.md) vagy [speciális](vm-specialized-image-version-cli.md) virtuális gép rendszerképből való létrehozásával, csak a paramétert kell hozzáadnia a `--os-disk-encryption-set` titkosítási csoport azonosítójával. Adatlemezek esetén adja hozzá az `--data-disk-encryption-sets` adatlemezek lemezes titkosítási készletének szóközzel tagolt listáját.


## <a name="portal"></a>Portál

Amikor létrehozza a rendszerkép verzióját a portálon, a **titkosítás** lapon megadhatja a tárolási titkosítási készletek alkalmazását.

> [!IMPORTANT]
> A kettős titkosítás használatához ezt a hivatkozást kell használnia [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) a Azure Portal eléréséhez. A kettős titkosítás jelenleg nem látható a nyilvános Azure Portal a hivatkozás használata nélkül.


1. A **rendszerkép verziójának létrehozása** lapon válassza a **titkosítás** lapot.
2. A **titkosítás típusa**beállításnál válassza ki a **REST-alapú titkosítást egy ügyfél által felügyelt kulccsal** , vagy a **platform által felügyelt és az ügyfél által felügyelt kulcsokkal rendelkező dupla titkosítással**. 
3. A rendszerkép minden egyes lemezén válassza ki a legördülő listából a használni kívánt **lemezes titkosítási beállítást** . 

### <a name="create-the-vm"></a>A virtuális gép létrehozása

Létrehozhat egy virtuális gépet a rendszerkép verziójának használatával, és az ügyfél által felügyelt kulcsokkal titkosíthatja a lemezeket. Amikor létrehozza a virtuális gépet a portálon, a **lemezek** lapon válassza a **titkosítás az ügyfél által felügyelt kulcsokkal** vagy a **kettős titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal** a **titkosítási típushoz**lehetőséget. Ezután kiválaszthatja a titkosítási készletet a legördülő menüből.

## <a name="next-steps"></a>További lépések

További információ a [kiszolgálóoldali lemezek titkosításáról](./windows/disk-encryption.md).

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).
