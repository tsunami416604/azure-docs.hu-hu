---
title: Lemezek az Azure-beli Windows virtuális gép titkosítása |} A Microsoft Docs
description: A fokozott biztonságot Windows virtuális gép virtuális lemezek titkosítása az Azure PowerShell-lel
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: cc0eee9dc36878f7a02b97453c859d94ea99b901
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217138"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>A Windows virtuális gép virtuális lemezek titkosítása
Bővített virtuális gép (VM) biztonsági és megfelelőségi virtuális lemezeket az Azure-ban is titkosítva. Lemezek kriptográfiai kulcsokat, egy Azure Key vaultban védett algoritmussal vannak titkosítva. Szabályozhatja a kriptográfiai kulcsokat és naplózhatja azok használatát. Ez a cikk ismerteti a Windows virtuális gép virtuális lemezek titkosítása az Azure PowerShell használatával. Emellett [Linux rendszerű virtuális gép titkosítása az Azure CLI-vel](../linux/encrypt-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás – áttekintés
Windows virtuális gépek a virtuális lemezek inaktív bitlockerrel titkosított. Nem jár költséggel az Azure-beli virtuális lemezek titkosításához. Titkosítási kulcsok szoftvervédelmi segítségével egy Azure Key vaultban tárolt, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Tartsa kézben a kriptográfiai kulcsokat, és naplózhatja azok használatát. 

Virtuális gépek titkosításához a folyamat a következőképpen történik:

1. Hozzon létre egy titkosítási kulcsot az Azure Key vaultban.
1. A titkosítási kulcs is használható titkosításához lemezek konfigurálása.
1. A virtuális lemezek lemeztitkosítás engedélyezve.
1. A szükséges titkosítási kulcsokat az Azure Key vault kérnek.
1. A virtuális lemezek vannak titkosítva, a megadott titkosítási kulcs használatával.


## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

Támogatott esetek és lemeztitkosításhoz követelmények:

* Új Windows virtuális gépek az Azure Piactérről származó rendszerképek vagy az egyéni Virtuálismerevlemez-képek titkosításának engedélyezését.
* A meglévő Windows-beli virtuális gépek titkosításának engedélyezését.
* A tárolóhelyek segítségével konfigurált Windows virtuális gépek titkosításának engedélyezését.
* Windows virtuális gépek meghajtók letiltja a titkosítást az operációs rendszer és az adatokat.
* Standard szintű virtuális gépeket, például A, D, DS, G és GS sorozatú virtuális gépek.

    > [!NOTE]
    > Az azonos Azure-régióban és az előfizetés összes erőforrást (beleértve a Key Vault, a Storage-fiók és a virtuális gép) kell lennie.

Lemeztitkosítás jelenleg nem támogatott a következő esetekben:

* Alapszintű csomag virtuális gépeket.
* A klasszikus üzemi modellel létrehozott virtuális gépek.
* A titkosítási kulcsok egy már a titkosított virtuális gép frissítése.
* Integráció a helyszíni kulcskezelő szolgáltatás.


## <a name="create-an-azure-key-vault-and-keys"></a>Az Azure Key Vaultban, és a kulcsok létrehozása
Mielőtt elkezdené, győződjön meg arról, hogy az Azure PowerShell-modul legújabb verziója telepítve van. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. A következő parancspéldákban cserélje le az összes példában paramétereket a saját nevét, helyét és értéket a például *myResourceGroup*, *myKeyVault*, *myVM*, és így tovább.

Az első lépés, ha az Azure Key Vaultban tárolni a titkosítási kulcsokat. Az Azure Key vault-Kulcstartók tárolhatja a kulcsok, titkos kódok és jelszavak, amelyek lehetővé teszik, hogy biztonságosan végrehajtja azokat az alkalmazásokat és szolgáltatásokat. A Virtuálislemez-titkosításhoz létrehozhat egy Key Vaultot titkosításakor vagy visszafejtésekor a virtuális lemezek használt kriptográfiai kulcs tárolására. 

Engedélyezze az Azure Key Vault-szolgáltató belül az Azure-előfizetésbe [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider), majd hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Az alábbi példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a a *USA keleti Régiójában* helye:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

A kriptográfiai kulcsokat és a kapcsolódó számítási erőforrásokat, például a storage és a virtuális gépre, az Azure Key Vault ugyanabban a régióban kell. Hozzon létre egy Azure Key Vault- [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) , és engedélyezze a Key Vault lemeztitkosítás való használatra. Adjon meg egy egyedi Key Vault számára *keyVaultName* módon:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Szoftver vagy hardver biztonsági modell (HSM) védelmi kriptográfiai kulcsokat tárolhat.  Egy standard Key Vault csak a szoftveres védelemmel ellátott kulcsokkal tárolja. Egy külön díj nélkül egy prémium szintű Key Vault HSM használata szükséges. Hozzon létre egy Key Vault premium, az előző lépésben adja hozzá a *- Sku "Prémium"* paraméter. Az alábbi példában használja a szoftveres védelemmel ellátott kulcsokkal, mivel hoztunk létre egy standard Key Vaultot. 

Mindkét védelme esetében az Azure platform kell hozzáférést kérni a titkosítási kulcsok a virtuális gép indításakor a virtuális lemezek visszafejtéséhez. Hozzon létre egy titkosítási kulcsot a Key vaultban a [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). A következő példában létrehozunk egy kulcsot *myKey*:

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A folyamat teszteléséhez hozzon létre egy virtuális Gépet a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). A következő példában létrehozunk egy nevű virtuális Gépet *myVM* használatával egy *Windows Server 2016 Datacenter* kép. Amikor a rendszer kéri a hitelesítő adatokat, adja meg a felhasználónevet és jelszót a virtuális géphez használandó:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>A virtuális gép titkosítása
A virtuális gép titkosítása [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) az Azure Key Vault-kulcs használatával. Az alábbi példa lekéri a kulccsal kapcsolatos adatokat, majd titkosítja a virtuális gép nevű *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Fogadja el a virtuális gép titkosítási folytatásához. A folyamat során a virtuális gép újraindul. Ha a titkosítási folyamat befejeződik, és a virtuális gép újraindult, tekintse át a titkosítási állapot [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

A kimenet a következő példához hasonló:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>További lépések
* Az Azure Key Vault kezelésével kapcsolatos további információkért lásd: [állítsa be a virtuális gépek egy Key Vaultot](key-vault-setup.md).
* További információ a lemeztitkosítást, például egy titkosított egyéni VM feltöltése az Azure-ba való felkészülés: [az Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
