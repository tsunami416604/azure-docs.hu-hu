---
title: Lemezek az Azure-beli Windows virtuális gép titkosítása |} A Microsoft Docs
description: A fokozott biztonságot nyújtó Azure PowerShell használatával Windows virtuális gép virtuális lemezek titkosítása
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
ms.openlocfilehash: 28bb4c9fd4827f534c5f7bac2bae15451e3ca16c
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914702"
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>A Windows virtuális gép virtuális lemezek titkosítása
Bővített virtuális gép (VM) biztonsági és megfelelőségi virtuális lemezeket az Azure-ban is titkosítva. Lemezek vannak titkosítva, egy Azure Key vaultban biztosított titkosítási kulcsokat. Szabályozhatja a kriptográfiai kulcsokat és naplózhatja azok használatát. Ez a cikk részletesen bemutatja az Azure PowerShell használatával Windows virtuális gép virtuális lemezeinek titkosításához. Emellett [az Azure parancssori felületével Linux virtuális gép titkosítása](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Lemeztitkosítás – áttekintés

Windows virtuális gépek a virtuális lemezek vannak titkosítása a Bitlocker használatával. Nem jár költséggel az Azure-beli virtuális lemezek titkosításához. Titkosítási kulcsok Azure Key Vault szoftver védelemmel vannak tárolva, vagy Ön importálhat vagy hozhat létre a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 szabványnak megfelelő 2. szint. Ezek a titkosítási kulcsok titkosítására és visszafejtésére a virtuális Géphez csatolt virtuális lemezek segítségével. Ezek a titkosítási kulcsok feletti ellenőrzés megtartása és naplózhatja azok használatát. 

Virtuális gépek titkosításához a folyamat a következőképpen történik:

1. Hozzon létre egy titkosítási kulcsot az Azure Key vaultban.
1. A titkosítási kulcs is használható titkosításához lemezek konfigurálása.
1. A virtuális lemezek lemeztitkosítás engedélyezve.
1. A szükséges titkosítási kulcsokat az Azure Key vault kérnek.
1. A virtuális lemezek vannak titkosítva, a megadott titkosítási kulcs használatával.

### <a name="azure-key-vault"></a>Azure Key Vault

Lemeztitkosítás támaszkodik [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). A Key Vault segítségével a kriptográfiai kulcsok és titkos kulcsokat, a lemez titkosítási/visszafejtési folyamathoz használt. 
  * Ha ilyen használhatja egy meglévő Azure Key Vaultban. Nem kell rendelnie egy Key Vaultot a lemezek titkosítása.
  * Külön adminisztratív határokat és a kulcs láthatóságát, létrehozhat egy dedikált Key Vaultot.


## <a name="requirements-and-limitations"></a>Követelmények és korlátozások

Támogatott esetek és lemeztitkosításhoz követelmények:

* Új Windows virtuális gépek az Azure Piactérről származó rendszerképek vagy egyéni VHD-rendszerképet titkosításának engedélyezését.
* A meglévő Windows-beli virtuális gépek titkosításának engedélyezését.
* A tárolóhelyek szolgáltatással konfigurált Windows virtuális gépek titkosításának engedélyezését.
* Windows virtuális gépek meghajtók letiltja a titkosítást az operációs rendszer és az adatokat.
* Az azonos Azure-régióban és az előfizetés összes erőforrás (például a Key Vault, a Storage-fiók és a virtuális gép) kell lennie.
* Standard szintű virtuális gépeket, például A, D, DS, G és GS sorozatú virtuális gépek.

Lemeztitkosítás jelenleg nem támogatott a következő esetekben:

* Alapszintű csomag virtuális gépeket.
* A virtuális gépek a klasszikus üzemi modellel létrehozott.
* A titkosítási kulcsok egy már a titkosított virtuális gép frissítése.
* Integráció a helyszíni kulcskezelő szolgáltatás.

## <a name="create-azure-key-vault-and-keys"></a>Az Azure Key Vault és a kulcsok létrehozása

A kezdés előtt győződjön meg arról, hogy az Azure PowerShell-modul legújabb verziója telepítve van. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. Során a parancspéldákban cserélje le az összes példában paramétereket a saját nevét, helyét és kulcsérték. Az alábbi példák a szabályt használ *myResourceGroup*, *myKeyVault*, *myVM*stb.

Az első lépés, ha az Azure Key Vaultban tárolni a titkosítási kulcsokat. Az Azure Key Vaultban tárolhatók, kulcsok, titkos kódok és jelszavak, amelyek lehetővé teszik, hogy biztonságosan végrehajtja azokat az alkalmazásokat és szolgáltatásokat. A virtuális lemez titkosításhoz hozzon létre egy Key Vaultot titkosításakor vagy visszafejtésekor a virtuális lemezek használt kriptográfiai kulcs tárolására. 

Engedélyezze az Azure Key Vault-szolgáltató belül az Azure-előfizetésbe [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider), majd hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a a *USA keleti Régiójában* helye:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Az Azure Key Vault a kriptográfiai kulcsokat és a kapcsolódó számítási erőforrásokat, például a storage és a virtuális gépre tartalmazó ugyanabban a régióban kell lennie. Hozzon létre egy Azure Key Vault- [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) , és engedélyezze a Key Vault lemeztitkosítás való használatra. Adjon meg egy egyedi Key Vault számára *keyVaultName* módon:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Titkosítási kulcsok használatával a szoftver vagy hardver biztonsági modell (HSM) védelmet is tárolhatja. Egy prémium szintű Key Vault HSM használata szükséges. Nincs hozhat létre egy standard szintű tároló Key Vaultot, szoftveres védelemmel ellátott kulcsokkal helyett a Key Vault premium további költségekkel járnak. Hozzon létre egy Key Vault premium, az előző lépésben adja hozzá a *- Sku "Prémium"* paramétereket. Az alábbi példában használja a szoftveres védelemmel ellátott kulcsokkal, mivel hoztunk létre egy standard Key Vaultot. 

Mindkét védelme esetében az Azure platform kell hozzáférést kérni a titkosítási kulcsok a virtuális gép indításakor a virtuális lemezek visszafejtéséhez. Hozzon létre egy titkosítási kulcsot a Key vaultban a [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). A következő példában létrehozunk egy kulcsot *myKey*:

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
A folyamat teszteléséhez hozzon létre egy virtuális Gépet a [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). A következő példában létrehozunk egy nevű virtuális Gépet *myVM* használatával egy *Windows Server 2016 Datacenter* kép. Amikor a rendszer kéri a hitelesítő adatokat, adja meg a felhasználónevet és jelszót a virtuális géphez használandó:

```azurepowershell-interactive
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Virtuális gép titkosítása
A virtuális lemezek titkosítását, hogy egyesítik az előző összetevők:

1. Adja meg az Azure Active Directory egyszerű szolgáltatás és a jelszót.
2. Adja meg a Key Vault, a titkosított lemezek metaadatok tárolására.
3. Adja meg a titkosítási kulcsok használata a tényleges titkosításra és visszafejtésre.
4. Adja meg, hogy szeretné-e az operációsrendszer-lemez, az adatlemezeket, vagy az összes titkosítása.

A virtuális gép titkosítása [Set-azurermvmdiskencryptionextension parancs](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) az Azure Key Vault-kulcs és az Azure Active Directory egyszerű szolgáltatás hitelesítő adatai használatával. Az alábbi példa lekéri a kulccsal kapcsolatos adatokat, majd titkosítja a virtuális gép nevű *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Fogadja el a virtuális gép titkosítási folytatásához. A folyamat során a virtuális gép újraindul. Ha a titkosítási folyamat befejeződik, és a virtuális gép újraindult, tekintse át a titkosítási állapot [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

A kimenet a következő példához hasonló:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>További lépések
* Az Azure Key Vault kezelésével kapcsolatos további információkért lásd: [Key Vault beállítása a virtuális gépek](key-vault-setup.md).
* További információ a lemeztitkosítást, például egy titkosított egyéni VM feltöltése az Azure-ba való felkészülés: [az Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
